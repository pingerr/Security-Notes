# Threat Hunting Notes

> 参考 ThrearHunter Playbook，遵循ATT&CK结构，以战术组为单位对Windows、Linux系统的入侵行为进行分类，梳理技术背景、攻击技术、检测逻辑及对应Sigma规则；
>
> - **非结构化猎杀**基于触发器发起的行为，该触发器是众多失陷指标 (IoC) 之一
> - **结构化猎杀**基于攻击者的战术、技术和程序 (TTP)，如 ATT&CK


- [Threat Hunting Notes](#threat-hunting-notes)
  - [1. 背景知识](#1-背景知识)
    - [1.1. 功能架构](#11-功能架构)
      - [1.1.1. 系统](#111-系统)
      - [1.1.2. 存储](#112-存储)
      - [1.1.3. 网络](#113-网络)
      - [1.1.4. 应用](#114-应用)
    - [1.2. 基本安全机制](#12-基本安全机制)
      - [1.2.1. 认证](#121-认证)
      - [1.2.2. 权限](#122-权限)
      - [1.2.3. 日志](#123-日志)
  - [2. ATT\&CK](#2-attck)
  - [3. Threat Hunting](#3-threat-hunting)
    - [3.1. 数据管理](#31-数据管理)
      - [3.1.1. 数据源（采集）](#311-数据源采集)
        - [3.1.1.1. Window](#3111-window)
      - [3.1.2. 数据标准化](#312-数据标准化)
        - [3.1.2.1. 统一数据模型](#3121-统一数据模型)
        - [3.1.2.2. ETL](#3122-etl)
      - [3.1.3. 数据存储](#313-数据存储)
    - [3.2. 检测场景及规则](#32-检测场景及规则)
      - [3.2.1.](#321)
  - [4. 参考](#4-参考)



## 1. 背景知识

### 1.1. 功能架构

#### 1.1.1. 系统



#### 1.1.2. 存储



#### 1.1.3. 网络



#### 1.1.4. 应用

### 1.2. 基本安全机制

#### 1.2.1. 认证



#### 1.2.2. 权限



#### 1.2.3. 日志

## 2. ATT&CK

## 3. Threat Hunting

### 3.1. 数据管理

#### 3.1.1. 数据源（采集） 

##### 3.1.1.1. Window

**日志（Event Log）**

1. Application
2. System
3. Security
4. PowerShell
5. Sysmon
6. etc.

**Sysmon 参考**

[官网](https://learn.microsoft.com/zh-cn/sysinternals/downloads/sysmon)

[配置](https://github.com/ion-storm/sysmon-config)

#### 3.1.2. 数据标准化

##### 3.1.2.1. 统一数据模型

##### 3.1.2.2. ETL

#### 3.1.3. 数据存储

### 3.2. 检测场景及规则

#### 3.2.1. 

## 4. 参考

1. [ATT&CK官网](https://attack.mitre.org/)
2. [Intranet_Penetration_Tips](https://github.com/Ridter/Intranet_Penetration_Tips)

3. [《ATT&CK与威胁猎杀实战》](https://weread.qq.com/web/bookDetail/03c32320729b708603c3f86)
4. [《ATT&CK框架实践指南》 (第2版)](https://weread.qq.com/web/bookDetail/8ff324f0813ab7fffg016499)
5. [腾讯SRC红蓝对抗专题](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MjM5NzE1NjA0MQ==&action=getalbum&album_id=1625340858235404289&scene=173&from_msgid=2651202058&from_itemidx=1&count=3&nolastread=1#wechat_redirect)

6. [Pentest-Notes](https://github.com/p0keeper/Pentest-Notes)

7. [Threat Hunter Playbook](https://github.com/OTRF/ThreatHunter-Playbook)
8. [《ATT&CK视角下的红蓝对抗实战指南》](https://book.douban.com/subject/36579994/)