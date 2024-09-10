# mod-amazoncloudwatch-manageagent
A mod of Systems Manager AmazonCloudWatch-ManageAgent Automation Document

## 背景
### 关于CloudWatch Agent
Amazon CloudWatch Agent 是一个用于收集和监控指标和日志的工具，它可以从您的 Amazon EC2 实例、本地服务器以及其他云服务中收集数据，并将这些数据发送到 Amazon CloudWatch。

### CloudWatch Agent配置的挑战
目前CloudWatch Agent提供过了Wizard，json配置文件和从SSM Parameter Store加载三种配置方式。但是无论哪种方式，对于disk等相关指标，都只能通过文件系统类型或者指定挂载点进行静态过滤，无法很好的适配复杂的操作系统磁盘配置，可能产生冗余和无意义的磁盘监控指标。
因此我们对Amazon提供的用于配置CloudWatch Agetn的自动化脚本”AmazonCloudWatch-ManageAgent"进行了扩展，以进行更灵活准确的CloudWatch Agent配置。

## 如何工作？
- 在自定义CloudWatch Agent配置(保存于json文件或者SSM Parameter Store中)，可以在disk的resources设置中填写“**PARTITIONMOUNTPOINTS**"占位字符串，经过修改的AmazonCloudWatch-ManageAgent自动化脚本会通过lsblk扫描磁盘分区对应的挂载点，过滤到虚拟和逻辑挂载点，真正反应磁盘容量使用情况。

## 使用方法
- 前置条件：已经配置好Systems Manager和在虚机上安装号CloudWatch Agent
- 创建Document：可以使用Mod-AmazonCloudWatch-ManageAgent.yaml CloudFormation模板创建，或者使用AutomationDocumentContent.json文件手动创建Systems Manager Document
- 创建保存CloudWatch Agent配置的SSM Parameter Store, 详见：https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file.html
- 使用State Manager关联需要配置CloudWatch Agent的EC2实例，实现CWAgent的自动配置。

## Background
### About CloudWatch Agent
Amazon CloudWatch Agent is a tool for collecting and monitoring metrics and logs. It can gather data from your Amazon EC2 instances, on-premises servers, and other cloud services, and send this data to Amazon CloudWatch.

### Challenges in Configuring CloudWatch Agent
Currently, CloudWatch Agent provides three configuration methods: Wizard, JSON configuration files, and loading from SSM Parameter Store. However, regardless of the method used, metrics related to disks can only be statically filtered by file system type or specified mount points. This does not adapt well to complex operating system disk configurations and may result in redundant and meaningless disk monitoring metrics. 
Therefore, we have extended Amazon's automated script for configuring CloudWatch Agent, "AmazonCloudWatch-ManageAgent," to enable more flexible and accurate CloudWatch Agent configuration.

## How Does It Work?
- In the customized CloudWatch Agent configuration (saved in a JSON file or SSM Parameter Store), you can fill in the placeholder string "**PARTITIONMOUNTPOINTS**" in the disk resources settings. The modified AmazonCloudWatch-ManageAgent automated script will scan the disk partitions corresponding to the mount points using `lsblk`, filter out virtual and logical mount points, and accurately reflect disk capacity usage.

## Usage Instructions
- Prerequisites: Systems Manager configured and CloudWatch Agent installed on the virtual machine.
- Create a Document: You can use the "Mod-AmazonCloudWatch-ManageAgent.yaml" CloudFormation template to create it, or manually create a Systems Manager Document using the "AutomationDocumentContent.json" file.
- Create an SSM Parameter Store to save the CloudWatch Agent configuration. For details, refer to: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file.html
- Use State Manager to associate the EC2 instances that need to configure CloudWatch Agent, achieving automatic CWAgent configuration.