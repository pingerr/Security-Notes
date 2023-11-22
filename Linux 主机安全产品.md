Linux 主机安全产品

- [1. 概述](#1-概述)
  - [1.1. 搜索源](#11-搜索源)
  - [1.2. 分析维度](#12-分析维度)
- [2. 开源 HIDS/EDR/CWPP](#2-开源-hidsedrcwpp)
  - [2.1. Tracee - Linux Runtime Security and Forensics using eBPF](#21-tracee---linux-runtime-security-and-forensics-using-ebpf)
    - [2.1.1. 事件](#211-事件)
  - [2.2. Elkeid - Bytedance Cloud Workload Protection Platform](#22-elkeid---bytedance-cloud-workload-protection-platform)
    - [2.2.1. 架构](#221-架构)
    - [2.2.2. 核心业务](#222-核心业务)
  - [2.3. OSSEC](#23-ossec)
  - [2.4. Fail2Ban](#24-fail2ban)
  - [2.5. ThreatMapper - Runtime Threat Management and Attack Path Enumeration for Cloud Native](#25-threatmapper---runtime-threat-management-and-attack-path-enumeration-for-cloud-native)
  - [2.6. ZeusCloud - Open Source Cloud Security](#26-zeuscloud---open-source-cloud-security)
  - [2.7. 驭龙 HIDS](#27-驭龙-hids)
  - [2.8. Hades - eBPF based HIDS](#28-hades---ebpf-based-hids)
- [3. 非开源 HIDS/EDR/CWPP](#3-非开源-hidsedrcwpp)



## 1. 概述 

### 1.1. 搜索源

https://github.com/topics/hids

https://github.com/topics/cwpp

https://github.com/topics/edr

https://github.com/topics/linux-security

https://github.com/topics/antivirus


### 1.2. 分析维度

- 架构设计
  - 业务架构
  - 技术架构
- 核心业务
  - 数据模型
  - 事件模型
  - 分析
  - 告警/响应/处置
- 核心技术

## 2. 开源 HIDS/EDR/CWPP

### 2.1. Tracee - Linux Runtime Security and Forensics using eBPF

> Star: 3k  
> 
> URL: https://github.com/aquasecurity/tracee 
> 
> About: Tracee 是一款运行时安全和可观察性工具，可帮助您了解系统和应用程序的行为方式。它使用 eBPF 技术来接入系统，并将这些信息作为可以使用的事件。事件范围从实际系统活动事件到可检测可疑行为模式的复杂安全事件。  
> 
> Program：Go、C

#### 2.1.1. 事件

6种内置事件类别
- syscalls
- network
- security
- lsm
- containers
- misc

### 2.2. Elkeid - Bytedance Cloud Workload Protection Platform

> Star: 1.9k  
> 
> URL: https://github.com/bytedance/Elkeid/tree/main
>   
> About: Elkeid 是一款可以满足 主机，容器与容器集群，Serverless 等多种工作负载安全需求的开源解决方案，源于字节跳动内部最佳实践。  
> 
> Program：Go、C、Rust、C++

#### 2.2.1. 架构

![架构](https://github.com/bytedance/Elkeid/raw/main/server/docs/server_new.png)

- **Host Ability**
  - **Elkeid Agent** 用户态 Agent，负责管理各个端上能力组件并与 **Elkeid Agent Center** 通信
  - **Elkeid Driver** 负责 Linux Kernel 层采集数据，兼容容器，并能够检测常见 Rootkit
  - **Elkeid RASP** 支持 CPython、Golang、JVM、NodeJS、PHP 的运行时数据采集探针，支持动态注入到运行时
  - **Elkeid Agent Plugins**
    - **Driver Plugin**: 负责与 Elkeid Driver 通信，处理其传递的数据等
    - **Collector Plugin**: 负责端上的资产/关键信息采集工作，如用户，定时任务，包信息等
    - **Journal Watcher**: 负责监测systemd日志的插件，目前支持ssh相关日志采集与上报
    - **Scanner Plugin**: 负责在端上进行静态检测恶意文件的插件，支持 Yara
    - **RASP Plugin**: 分析系统进程运行时，上报运行时信息，处理下发的 Attach 指令，收集各个探针上报的数据
    - **Baseline Plugin**: 负责在端上进行基线风险识别的插件
- **Backend Ability**
  - **Elkeid AgentCenter** 负责与 Agent 进行通信并管理 Agent 如升级，配置修改，任务下发等
  - **Elkeid ServiceDiscovery** 后台中的各组件都会向该组件定时注册、同步服务信息，从而保证各组件相互可见，便于直接通信
  - **Elkeid HUB** 策略引擎
  - **Elkeid Manager** 负责对整个后台进行管理，并提供相关的查询、管理接口
  - **Elkeid Console** Elkeid 前端部分


#### 2.2.2. 核心业务

1. 主机侧功能

**数据采集&关联分析**：

-用户态
  - 主机信息
    - 进程
    - 端口
    - 账户
    - 软件
    - 容器
    - 应用
    - 硬件
    - 系统完整性校验
    - 内核
    - 系统服务
    - 定时任务
  - 日志
    - 认证授权
- 内核态

**基线风险识别**

**恶意文件静态检测**：

支持 Yara

**RASP**

### 2.3. OSSEC

> Star: 4.1k  
> URL: https://github.com/ossec/ossec-hids  
> About: OSSEC 是一个基于主机的开源入侵检测系统，可执行:日志分析、文件完整性检查、策略监控、rootkit 检测、实时警报和主动响应。它将 HIDS、日志监控和 SIM/SIEM 融合在一个简单、强大的开源解决方案中。 
> Program：C


### 2.4. Fail2Ban

> Star: 9.1k  
> URL: https://github.com/fail2ban/fail2ban 
> About: Daemon to ban hosts that cause multiple authentication errors（禁止导致多次身份验证错误的主机的守护进程）  
> Program：Python


### 2.5. ThreatMapper - Runtime Threat Management and Attack Path Enumeration for Cloud Native

> Star: 4.5k  
> URL: https://github.com/deepfence/ThreatMapper  
> About: Deepfence ThreatMapper 可在您的生产平台中搜索威胁，并根据其暴露风险对这些威胁进行排序。它能发现易受攻击的软件组件、暴露的秘密以及偏离良好安全实践的情况。ThreatMapper 结合使用基于代理的检测和无代理监控，以提供尽可能广泛的威胁检测覆盖范围。利用 ThreatMapper 的 ThreatGraph 可视化功能，可以确定对应用程序安全构成最大风险的问题，并将这些问题按优先顺序排列，以便进行有计划的保护或修复。  
> Program：TypeScript、Go

### 2.6. ZeusCloud - Open Source Cloud Security

> Star: 629 
> URL: https://github.com/Zeus-Labs/ZeusCloud 
> About:  
> Program：TypeScript、Go

### 2.7. 驭龙 HIDS

> Star: 2.1k  
> URL: https://github.com/ysrc/yulong-hids-archived 
> About: 驭龙HIDS是一款由 YSRC 开源的入侵检测系统，由 Agent， Daemon， Server 和 Web 四个部分组成，集异常检测、监控管理为一体，拥有异常行为发现、快速阻断、高级分析等功能，可从多个维度行为信息中发现入侵行为。 
> Program：Go

### 2.8. Hades - eBPF based HIDS

> Star: 260 
> URL: https://github.com/chriskaliX/Hades/tree/main  
> About: Hades 是一个基于 eBPF 的主机入侵检测系统，同时兼容低版本下通过 netlink(cn_proc) 进行事件审计，借鉴了Tracee和Elkeid 
> Program：C、Rust、Go


## 3. 非开源 HIDS/EDR/CWPP