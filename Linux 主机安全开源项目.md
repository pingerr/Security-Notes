Linux 主机安全开源项目

- [1. 概述](#1-概述)
  - [1.1. Reference](#11-reference)
  - [1.2. 分析维度](#12-分析维度)
- [2. Tracee](#2-tracee)
  - [2.1. 架构](#21-架构)
  - [2.2. 数据源](#22-数据源)
  - [2.3. 事件模型及规则](#23-事件模型及规则)
- [3. Elkeid](#3-elkeid)
  - [3.1. 架构](#31-架构)
  - [3.2. 核心功能](#32-核心功能)
  - [3.3. 技术原理](#33-技术原理)
    - [3.3.1. 内核层数据采集与 Rootkit 检测](#331-内核层数据采集与-rootkit-检测)
    - [3.3.2. 端上资产/关键信息采集](#332-端上资产关键信息采集)
- [4. Wazuh](#4-wazuh)
- [5. Falco](#5-falco)
- [6. Tetragon](#6-tetragon)
  - [6.1. 架构](#61-架构)
- [7. Fail2Ban](#7-fail2ban)
- [8. ThreatMapper](#8-threatmapper)
- [9. Trivy](#9-trivy)
- [10. ZeusCloud —— 预防性云安全平台](#10-zeuscloud--预防性云安全平台)
- [11. 驭龙 HIDS](#11-驭龙-hids)
  - [11.1. 架构](#111-架构)
  - [11.2. 数据源](#112-数据源)
    - [11.2.1. 信息收集](#1121-信息收集)
    - [11.2.2. 行为监控](#1122-行为监控)
  - [11.3.](#113)
- [12. Hades - eBPF based HIDS](#12-hades---ebpf-based-hids)
  - [12.1. 架构](#121-架构)
  - [12.2. 核心功能](#122-核心功能)
- [13. 安全狗云眼 云主机入侵监测及安全管理平台](#13-安全狗云眼-云主机入侵监测及安全管理平台)
- [14. 总结](#14-总结)
  - [14.1. 架构](#141-架构)
  - [14.2. 数据源](#142-数据源)
    - [14.2.1. 资产信息](#1421-资产信息)
    - [14.2.2. 行为监控](#1422-行为监控)



## 1. 概述 

### 1.1. Reference

https://github.com/topics/hids

https://github.com/topics/cwpp

https://github.com/topics/edr

https://github.com/topics/linux-security

https://github.com/topics/antivirus


### 1.2. 分析维度

- 架构
- 功能
- 数据源
- 分析层
  - 事件模型
  - 规则/算法
  - 技术原理
- 响应层


## 2. Tracee

> Star: 3k  
> Program：Go、C        
> URL: https://github.com/aquasecurity/tracee 


Linux Runtime Security and Forensics using eBPF

> About: Tracee 是一个用于 Linux 的运行时安全和取证工具。它使用 Linux eBPF 技术在运行时跟踪系统和应用程序，并分析收集的事件以检测可疑的行为模式。Tracee 以 Docker 镜像的形式交付，监控操作系统并根据预定义的行为模式集检测可疑行为   

### 2.1. 架构

![tracee_Arch](<assets/Linux 主机安全开源项目/image-8.png>)

### 2.2. 数据源



### 2.3. 事件模型及规则

**在Tracee中，一切系统和应用行为皆为事件（Event）**


6种内置事件类别
- syscalls
- network
- security
- lsm
- containers
- misc

## 3. Elkeid

Bytedance Cloud Workload Protection Platform

> Star: 1.9k  
> 
> URL: https://github.com/bytedance/Elkeid
>   
> About: Elkeid 是一款可以满足 主机，容器与容器集群，Serverless 等多种工作负载安全需求的开源解决方案，源于字节跳动内部最佳实践。  
> 
> Program：Go、C、Rust、C++

### 3.1. 架构

![Elkeid架构](<assets/Linux 主机安全开源项目/image.png>)

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
  - **Elkeid ServiceDiscovery** 后台中的各组件都会向该组件定时注册、同步服务信息，从而保证各组件相互可见，  便于直接通信
  - **Elkeid HUB** 策略引擎
  - **Elkeid Manager** 负责对整个后台进行管理，并提供相关的查询、管理接口
  - **Elkeid Console** Elkeid 前端部分


### 3.2. 核心功能

**资产盘点**
> 统一管理主机列表、容器列表、进程、Java进程依赖信息、端口、账号、系统组件、系统服务、定时任务、系统完整性等资产指纹信息，帮助企业资产可视化


**安全基线**

**入侵检测**

> 对主机与容器的入侵行为进行检测并提供处置方案，包括：反弹shell、本地提权、容器逃逸、后门木马、内核态后门、恶意命令、可疑命令序列、暴力破解等

**RASP**

> 对应用运行时入侵行为进行实时检测并提供处置方案，包括：各类内存马、各类RCE、SSRF、ONGL注入、可疑命令序列等，且支持热补丁技术

**云原生防护**

> 实现对云原生环境内的攻击行为、不安全资源实时监控，并提供对安全风险的溯源分析和大盘展示等能力。帮助企业进行入侵响应和风险分析。

**漏洞检测**

> 对主机、应用上存在的漏洞风险进行全面监测，包括系统组件漏洞、应用漏洞等，帮助企业应对漏洞风险


### 3.3. 技术原理

#### 3.3.1. 内核层数据采集与 Rootkit 检测

#### 3.3.2. 端上资产/关键信息采集

**1 进程**：

> 支持对exe md5的哈希计算，后续可关联威胁情报分析   
> 与容器信息进行关联，支撑后续数据溯源功能(跨容器)

获取进程信息目录
```
$ ll /proc
total 0
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 1
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 10
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 11
```

采集信息以 pid=2406 的进程为例子：

- **cmdLine**  启动当前进程的完整命令行信息
```
$ cat /proc/2406/cmdline
frps-c./frps.ini
```

- **cwd** 当前进程运行目录的符号链接
```
$ ls -lt /proc/2406/cwd
lrwxrwxrwx 1 root root 0 Dec 12 20:39 /proc/2406/cwd -> /home/mike/frp_0.13.0_linux_amd64
```

- **md5** 进程exe的md5校验和，需要计算
```
$ ls -lt /proc/2406/exe
lrwxrwxrwx 1 root root 0 Dec 11 19:00 /proc/2406/exe -> /usr/bin/frps
```

- **exe** 实际运行程序的符号链接
```
$ ls -lt /proc/2406/exe
lrwxrwxrwx 1 root root 0 Dec 11 19:00 /proc/2406/exe -> /usr/bin/frps
```

**进程 status 状态信息**
- **pid**  进程id
- **name** 进程名
- **state**  进程的状态信息(R 处于运行状态，S 处于休眠状态，D 处于不间断等待状态，Z 处于僵尸状态，T 处于跟踪或停止状态)
- **ppid**  父进程的pid
- **umask** 文件模式创建掩码
- **tracerPid** 跟踪进程的PID
- **ruid**  real uid
- **rUsername**
- **euid**  effective uid
- **eUsername**
- **suid**  saved set uid
- **sUsername**
- **fsuid** file system uid
- **fUsername**
- **rgid**  real gid
- **egid**  effective gid
- **sgid**  saved set gid
- **fsgid** file system gid
```
$ cat /proc/2406/status
Name:   frps
State:  S (sleeping)
Tgid:   2406
Ngid:   0
Pid:    2406
PPid:   2130
TracerPid:  0
Uid:    0   0   0   0  /*ruid euid suid fsuid*/
Gid:    0   0   0   0  /*rgid egid sgid fsgid*/
FDSize: 128
Groups: 0
NStgid: 2406
NSpid:  2406
NSpgid: 2406
NSsid:  2130
...
```

**进程 stat 状态信息**
- **pgid** 进程的 pgrp(第5个值)
- **sid** session id (第5个值)
- **startTime** 系统启动后进程开始的时间（第22个值，需要加上系统启动时间）
```
$ cat /proc/2406/stat
2406 (frps) S 2 0 0 0 -1 69247072 0 0 0 0 0 2453 0 0 20 0 1 0 70730273 0 0 18446744073709551615 0 0 0 0 0 0 0 2147483647 0 18446744072085041241 0 0 17 1 0 0 328 0 0 0 0 0 0 0 0 0 0
```


**2 端口**
> 支持tcp、udp监听端口的信息提取，以及与进程、容器信息的关联上报。  
> 基于sock状态及其关系，分析对外暴露服务，向上支撑主机暴露面分析功能。(跨容器)

```
type Port struct {
	// from inet
	Family   string 
	Protocol string 
	State    string 
	Sport    string 
	Dport    string 
	Sip      string 
	Dip      string 
	Uid      string 
	Inode    string 
	Username string 
	// from process
	Pid     string 
	Exe     string 
	Comm    string 
	Cmdline string 
	Psm     string 
	PodName string 
}
```


- 账户
  - 除了基本的账户字段外，基于弱口令字典进行端上hash碰撞检测弱口令，向上提供了Console的弱口令基线检测功能。另外，会关联分析sudoers配置，一同上报。
```
type User struct {
	Username            string 
	Password            string 
	Uid                 string 
	Gid                 string 
	Groupname           string 
	Info                string 
	Home                string 
	Shell               string 
	LastLoginTime       string 
	LastLoginIP         string 
	WeakPassword        string 
	WeakPasswordContent string 
	Sudoers             string 
}
```


```
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync

格式：
username:password:uid:gid:info:home:shell
```
补充password:
```
$ cat /etc/shadow
root:$6$sD8gshA6$d7rXAxa11g8uP4PRkrfSZ7rkYPNCYFXEltyoTbUUj3OPr5vWqlLmGJwRS9c5PidnWaqx/5QkT.BtpU2DUb2mG/:19619:0:99999:7:::
bin:*:17834:0:99999:7:::
daemon:*:17834:0:99999:7:::
adm:*:17834:0:99999:7:::
lp:*:17834:0:99999:7:::
sync:*:17834:0:99999:7:::

```




- 软件 
  - 支持系统软件包、pypi包、jar包，向上支撑漏洞扫描功能。(部分跨容器)
- 容器 
  - 支持docker、cri/containerd等多种运行时下的容器信息采集。
- 应用
  - 支持数据库、消息队列、容器组件、Web服务、DevOps工具等类 型的应用采集、目前支持30+中常见应用的版本、配置文件的匹配与提取。(跨容器)
- 硬件
  - 支持网卡、磁盘等硬件信息的采集。
- 系统完整性校验
  - 通过将软件包文件哈希与Host实际文件哈希进行对比，判断文件是否有被更改。
- 内核模块
  - 采集基本字段，以及内存地址、依赖关系等额外字段。

type Kmod struct {
    Name
    size
    refCount
    usedBy
    state
    addr
}

```
[root@hecs-393810 proc]# cat modules
xt_conntrack 12760 3 - Live 0xffffffffc03e4000
ipt_MASQUERADE 12678 3 - Live 0xffffffffc03da000
nf_nat_masquerade_ipv4 13463 1 ipt_MASQUERADE, Live 0xffffffffc03df000
nf_conntrack_netlink 36396 0 - Live 0xffffffffc03d0000
iptable_nat 12875 1 - Live 0xffffffffc03cb000
nf_conntrack_ipv4 15053 4 - Live 0xffffffffc03c6000

```


- 系统服务
  - 兼容不同发行版下的服务，并对核心字段进行解析。

type Service struct {
    Name       
    Type     
    Command 
    Restart   
    WorkingDir 
    Checksum   
    BusName    
}



- 定时任务
  - 兼容不同发行版下cron位置的定义，并对核心字段进行解析。

type Crontab struct {
	Path     
	Username 
	Schedule 
	Command  
	Checksum 
}




1. **日志采集**

- 认证授权日志
  - `/var/log/secure`
  - `/var/log/auth`
- 安全审计日志
  - `/var/log/audit`
- 整体系统日志
  - `/var/log/messages`

4. **基线检测**

- 身份鉴别-设置密码失效时间<=90天
  - 说明
    - 设置密码失效时间，定期修改密码策略，减少密码被泄漏和猜测风险，使用非密码登陆方式(如密钥对)请忽略此项。
  - 检测
    - 文件 `/etc/login.defs` 中 `PASS_MAX_DAYS <= 90`

- 身份鉴别-密码修改最短周期>=2天
  - 说明
    - 设置密码修改最小间隔时间，限制密码更改过于频繁
  - 检测
    - 文件 `/etc/login.defs` 中 `ASS_MIN_DAYS >= 2`

- 身份鉴别-密码到期时间警告>=7天
  - 说明
    - 确保密码到期警告天数为7或更多
  - 检测
    - 文件 `/etc/login.defs` 中 `PASS_WARN_AGE >= 7`

- 身份鉴别-密码复杂性检查
  - 说明
    - 检查密码长度和密码是否使用多种字符类型
  - 检测
    - 文件 `/etc/security/pwquality.conf` 中 `minlen >= 8`
    - 文件 `/etc/security/pwquality.conf` 中 `minclass >= 3`
    - 文件 `/etc/pam.d/password-auth` 和 `/etc/pam.d/system-auth`中`pam_pwquality.so`这一行的`try_first_pass`后边 `retry <= 3`

- 身份鉴别-确保root是唯一UID为0的用户
  - 说明
    - 除root以外其他UID为0的用户都应该删除，或者为其分配新的UID
  - 检测
    - cmd `cat /etc/passwd | awk -F: '($3 == 0) { print $1 }'|grep -v '^root$' )`，输出用户名

- 身份鉴别-查是否限制密码重用
  - 说明
    - 限制用户之间重用密码的行为，降低密码泄漏的风险
  - 检测
    - 文件`/etc/pam.d/password-auth`和`/etc/pam.d/system-auth`中`password sufficient pam_unix.so` 这行`remember=5`

- 身份鉴别-空口令账户检测
  - 查看`/etc/shadow`
  - `*`代表帐号被锁定,`!!`表示密码过期
  - 为空口令账户设置安全密码，或执行`passwd -l <username>`锁定用户

- SSH检测-SSH空密码检测
  - 文件`/etc/ssh/sshd_config`中配置`PermitEmptyPasswords no`

- SSH检测-SSH失败尝试次数<5
  - 说明
    - 设置较低的 MaxAuthTrimes 参数将降低 SSH 服务器被暴力攻击成功的风险
  - 检测
    - 文件`/etc/ssh/sshd_config`中配置`MaxAuthTries 5`

- SSH检测-减少空闲超时退出时间
  - 说明
    - 设置SSH空闲超时退出时间,可降低未授权用户访问其他用户ssh会话的风险
  - 检测
    - 文件`/etc/ssh/sshd_config`中
      - `ClientAliveInterval <= 900`(15分钟)
      - `ClientAliveCountMax`为`0-3`之间

- SH检测-确保SSH LogLevel设置为INFO
  - 说明
    - 确保SSH LogLevel设置为INFO,记录登录和注销活动
  - 检测
    - 文件`/etc/ssh/sshd_config`中`LogLevel INFO`

- 安全审计-确保开启日志守护进程(auditd)
  - check ：`systemctl is-enabled auditd`，output：`enabled`
  - 启动 auditd 服务: `systemctl --now enable auditd`

- 安全审计-确保开启日志守护进程(rsyslog)
  - check ：`systemctl is-enabled rsyslog`，output：`enabled`
  - 启动 rsyslog 服务: `systemctl --now enable rsyslog`

- 入侵防范-开启地址随机化(ASLR)
  - 说明
    - 将进程的内存空间地址随机化来增大入侵者预测目的地址难度，从而降低进程被成功入侵的风险
  - 检测 
    - cmd `grep -Rh ^kernel\.randomize_va_space /etc/sysctl.conf /etc/sysctl.d` 或 `sysctl kernel.randomize_va_space`， result `kernel.randomize_va_space = 2`
  - 解决
    - cmd `sysctl -w kernel.randomize_va_space=2`

- 文件权限-确保配置文件的安全性
  - 说明：
    - 为了保证系统的安全性，请确保配置文件的安全性和唯一性，限制未授权用户对配置文件的一切操作，包括访问、读写、删除等
  - 检测：
    - cmd `stat /etc/passwd |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0644`
    - cmd `stat /etc/shadow |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0400`
    - cmd `stat /etc/group |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0644`
    - cmd `stat /etc/gshadow |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0400`
  - 解决：
    - cmd `chown root:root /etc/passwd /etc/shadow /etc/group /etc/gshadow`
    - cmd `chmod 0644 /etc/group`
    - cmd `chmod 0644 /etc/passwd`
    - cmd `chmod 0400 /etc/shadow`
    - cmd `chmod 0400 /etc/gshadow`

- 文件权限-用户访问配置文件的权限
  - 检测
    - cmd `stat /etc/hosts.allow |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0644`
    - cmd `stat /etc/hosts.deny |sed -n '4p'|grep  -Eo '[0-9]{4}'`，result `0644`
  - 解决：
    - cmd `chown root:root /etc/hosts.allow`
    - cmd `chown root:root /etc/hosts.deny`
    - cmd `chmod 644 /etc/hosts.allow`
    - cmd `chmod 644 /etc/hosts.deny`



5. **恶意文件检测（静态）**
ClamAV + Yara

6. **运行时应用安全防护**

## 4. Wazuh

The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads.

> Star: 7.8k    
> URL: https://github.com/wazuh/wazuh      
> Program：C、Python、C++  


Wazuh 是一个用于威胁预防、检测和响应的免费开源平台。它能够保护企业内部、虚拟化、容器化和云环境中的工作负载。

Wazuh 解决方案由部署到受监控系统的端点安全代理和管理服务器组成，后者负责收集和分析代理收集的数据。此外，Wazuh 还与 Elastic Stack 完全集成，提供了一个搜索引擎和数据可视化工具，使用户能够浏览安全警报。


## 5. Falco

Cloud Native Runtime Security

> Star: 6.5k    
> URL: https://github.com/falcosecurity/falco      
> Program：C++  


## 6. Tetragon 

eBPF-based Security Observability and Runtime Enforcement

> Star: 2.9k    
> URL: https://github.com/cilium/tetragon     
> Program：Go、C  

- Tetragon 组件基于 **eBPF** 实现实时安全可观察性和运行时执行。
- Tetragon 可检测并应对安全重大事件，如：
  - **进程执行事件** 
  - **系统调用活动** 
  - **I/O 活动（网络和文件访问）** 
- Tetragon **具有 Kubernetes 感知能力**，即能够理解命名空间、pod 等 Kubernete 标识，可根据单个工作负载配置安全事件检测。

### 6.1. 架构

![Tetragon架构1](<assets/Linux 主机安全开源项目/image-2.png>)

![Tetragon架构2](<assets/Linux 主机安全开源项目/image-3.png>)



## 7. Fail2Ban

> Star: 9.1k  
> URL: https://github.com/fail2ban/fail2ban 
> About: Daemon to ban hosts that cause multiple authentication errors（禁止导致多次身份验证错误的主机的守护进程）  
> Program：Python


## 8. ThreatMapper

Runtime Threat Management and Attack Path Enumeration for Cloud Native

> Star: 4.5k  
> URL: https://github.com/deepfence/ThreatMapper  
> About: Deepfence ThreatMapper 可在您的生产平台中搜索威胁，并根据其暴露风险对这些威胁进行排序。它能发现易受攻击的软件组件、暴露的秘密以及偏离良好安全实践的情况。ThreatMapper 结合使用基于代理的检测和无代理监控，以提供尽可能广泛的威胁检测覆盖范围。利用 ThreatMapper 的 ThreatGraph 可视化功能，可以确定对应用程序安全构成最大风险的问题，并将这些问题按优先顺序排列，以便进行有计划的保护或修复。  
> Program：TypeScript、Go

## 9. Trivy 

> URL：https://github.com/aquasecurity/trivy  

## 10. ZeusCloud —— 预防性云安全平台

Open Source Cloud Security

> Star: 629 
> URL: https://github.com/Zeus-Labs/ZeusCloud 

> Program：TypeScript、Go

ZeusCloud 是一个预防性云安全平台。它可以帮助您发现、优先处理和补救云中的风险。使用 ZeusCloud，您可以:
- 建立 AWS 账户的资产清单。
- 持续监控环境中的攻击路径和错误配置。
- 自定义安全和合规控制，以满足您的需求。
- 根据上下文对安全发现进行优先排序和补救
- 符合 PCI DSS、CIS 等合规标准

## 11. 驭龙 HIDS

> Star: 2.1k  
> URL: https://github.com/ysrc/yulong-hids-archived 
> About: 驭龙HIDS是一款由 YSRC 开源的入侵检测系统，由 Agent， Daemon， Server 和 Web 四个部分组成，集异常检测、监控管理为一体，拥有异常行为发现、快速阻断、高级分析等功能，可从多个维度行为信息中发现入侵行为。 
> Program：Go

### 11.1. 架构

![yulongArch](<assets/Linux 主机安全开源项目/image-7.png>)

**Agent**为采集者角色，收集服务器信息、开机启动项、计划任务、监听端口、服务、登录日志、用户列表，实时监控文件操作行为、网络连接、进程创建，初步筛选整理后通过RPC协议传输到Server节点。

**Daemon**为守护服务进程，为Agent提供进程守护、静默环境部署作用，其任务执行功能通过接收服务端的指令实现Agent热更新、阻断功能和自定义命令执行等，任务传输过程使用RSA进行加密。

**Server**为整套系统的大脑，支持横向扩展分布式部署，解析用户定义的规则（已内置部分基础规则）对从各Agent接收到的信息和行为进行分析检测和保存，可从各个维度的信息中发现webshell写入行为、异常登录行为、异常网络连接行为、异常命令调用行为等，从而实现对入侵行为实时预警。


### 11.2. 数据源

#### 11.2.1. 信息收集

- 服务器信息

```
// ComputerInfo 计算机信息结构
type ComputerInfo struct {
	IP       string   // IP地址
	System   string   // 操作系统
	Hostname string   // 计算机名
	Type     string   // 服务器类型
	Path     []string // WEB目录
}
```

- 开机启动项

```
type startup struct {
	Caption  string // 描述信息
	Command  string // 执行的程序、命令
	Location string // 开机启动来源
	User     string // 启动用户
}
```

- 进程
基于`/proc`文件系统

- 计划任务

系统计划任务 `/etc/crontab`
用户计划任务 `/var/spool/cron/`

数据结构
```
name // 计划任务名
command // 要执行的程序或命令以及参数
arg // 启动参数
user // 启动用户
rule
description // 描述
```

- 监听端口
TCP监听端口 `ss -nltp`

```
proto // 类型
address // 监听地址
name // 监听程序名
pid // 监听程序pid
```

- 服务
```
type service struct {
	Caption   string // 描述信息
	Name      string // 服务名称
	PathName  string // 服务程序路径、启动命令
	Started   bool   // 是否已启动
	StartMode string // 启动模式
	StartName string // 启动用户
}
```

- 登录日志
`last`
`lastb`

数据结构
```
username // 用户名
hostname // 远程主机名
remote // 远程IP
status // 认证结果
time // 时间
```

- 用户列表
`/etc/passwd` 忽略 `/nologin` 用户

- Web目录


#### 11.2.2. 行为监控

1. **文件操作**

- 技术原理

  在 linux 内核中，Inotify 是一种用于通知用户空间程序文件系统变化的机制。它监控文件系统的变化，如文件新建、修改、删除等，并可以将相应的事件通知给应用程序。Inotify 既可以监控文件，也可以监控目录。当监控目录时，它可以同时监控目录及目录中的各子目录及文件。
  Golang 的标准库 syscall 实现了该机制。为了进一步扩展和抽象， https://github.com/fsnotify/fsnotify 包实现了一个基于 channel 的、跨平台的实时监听接口。

- 数据结构
  ```
  source  // 类型：文件、目录
  path    // 文件或者目录路径
  action  // 文件操作类型
  user    // 操作用户
  hash    // 文件md5 hash
  ```


2. **网络连接**
- 技术实现
[gopacket](https://github.com/google/gopacket) 是谷歌开源的一款抓包库，为 go 语言提供了处理网卡包的能力。其底层基于libpcap。

- 数据结构
```
dir // 方向
protocol // 类型（TCP、UDP）
local // 本机进行通讯ip:port
remote // 远程进行通讯的ip:port
name // 进程名
pid // 进程pid
```


3. **进程创建**
-技术实现
syshook netlink

数据模型
```
name // 进程名
command // 程序或命令以及参数
pid // 进程pid
ppid // 父进程pid
parentname // 父进程名
info // 进程其他相关信息
```

### 11.3. 


## 12. Hades - eBPF based HIDS

> Star: 260 
> URL: https://github.com/chriskaliX/Hades/tree/main  
> About: Hades 是一个基于 eBPF 的主机入侵检测系统，同时兼容低版本下通过 netlink(cn_proc) 进行事件审计，借鉴了Tracee和Elkeid 
> Program：C、Rust、Go


### 12.1. 架构

**Agent**

![hades-architecture-agent](<assets/Linux 主机安全开源项目/image-4.png>)

**Data Analysis**

![hades-architecture-data-analysis](<assets/Linux 主机安全开源项目/image-5.png>)

### 12.2. 核心功能

1. **Linux Kernel Hook**

| Hook                                       | Status & Description                  | ID   |
| :----------------------------------------- | :------------------------------------ | :--- |
| tracepoint/syscalls/sys_enter_execve       | ON                                    | 700  |
| tracepoint/syscalls/sys_enter_execveat     | ON                                    | 698  |
| tracepoint/syscalls/sys_enter_memfd_create | ON                                    | 614  |
| tracepoint/syscalls/sys_enter_prctl        | ON(PR_SET_NAME & PR_SET_MM)           | 1020 |
| tracepoint/syscalls/sys_enter_ptrace       | ON(PTRACE_PEEKTEXT & PTRACE_POKEDATA) | 1021 |
| kprobe/security_socket_connect             | ON                                    | 1022 |
| kprobe/security_socket_bind                | ON                                    | 1024 |
| kprobe/commit_creds                        | ON                                    | 1011 |
| k(ret)probe/udp_recvmsg                    | ON(53/5353 for dns data)              | 1025 |
| kprobe/do_init_module                      | ON                                    | 1026 |
| kprobe/security_kernel_read_file           | ON                                    | 1027 |
| kprobe/security_inode_create               | ON                                    | 1028 |
| kprobe/security_sb_mount                   | ON                                    | 1029 |
| kprobe/call_usermodehelper                 | ON                                    | 1030 |
| kprobe/security_inode_rename               | ON                                    | 1031 |
| kprobe/security_inode_link                 | ON                                    | 1032 |
| uprobe/trigger_sct_scan                    | ON                                    | 1200 |
| uprobe/trigger_idt_scan                    | ON                                    | 1201 |
| kprobe/security_file_permission            | ON                                    | 1202 |
| uprobe/trigger_module_scan                 | ON                                    | 1203 |
| kprobe/security_bpf                        | ON                                    | 1204 |

2. **系统信息采集**

> S 代表异步采集，P 代表周期采集，C 代表触发采集

|   Event   | Type |  ID  |
| :-------: | :--: |  :-: |
| processes |  P   | 1001 |
|  crontab  |  P   | 2001 |
|sshdconfig |  P   | 3002 |
| ssh login |  S   | 3003 |
|   user    |  P   | 3004 |
| sshconfig |  P   | 3005 |
|    yum    |  P   | 3006 |
|host detect|  C   | 3007 |
|    apps   |  P   | 3008 |
|    kmod   |  P   | 3009 |
|    disk   |  P   | 3010 |
|  systemd  |  P   | 3011 |
| interface |  P   | 3012 |
|  iptable  |  P   | 3013 |
|bpf_program|  P   | 3014 |
|    jar    |  P   | 3015 |
|   dpkg    |  P   | 3016 |
|    rpm    |  P   | 3017 |
| container |  P   | 3018 |
|  socket   |  P   | 5001 |


## 13. 安全狗云眼 云主机入侵监测及安全管理平台

https://www.yun88.com/product/2463.html



## 14. 总结

### 14.1. 架构

### 14.2. 数据源

#### 14.2.1. 资产信息

1. 操作系统基本信息
2. 软件安装信息 
   1. CVE漏洞库的匹配
3. 内核模块信息
   1. rookit，新增内核模块类，需要通过基线检查（白名单机制）发现非白名单的内核模块；
4. 账号信息
   1. 不安全/etc/shadow加密协议
   2. 存在uid=0且用户名不为root的特权账户
   3. 系统弱口令
   4. 系统空口令
5. 端口信息
6. 网络连接信息
7. 环境变量信息
   1. 环境变量 LD_PRELOAD 指向共享库的路径，该路径要在任何其他共享对象之前加载
8. 进程信息 + fd 
   1. 反弹 shell
      - bash进程或其父进程的fd重定向到pipe或socket
   2. 提权
      - 进程的UID是否有修改，uid,euid从非0提升为0，egid从非0提升为0
      - cap_setuid是否从无效变成有效
      - 父进程的euid和egid非0，但子进程的euid为0。
9.  自启动项
10. cron计划任务
   1.  命令检测
11. 系统服务



#### 14.2.2. 行为监控

1. bash明令
   - 恶意命令
     -  :(){:|:&};:
     -  

   - 敏感数据收集_查找具有setuid/setgid能力的文件
   - 防御规避_二进制填充
   - 防御规避_Bpfdoor TCP 端口重定向
   - 权限提升_挖矿的命令行参数
   - 敏感信息窃取_使用 Wget 进行数据泄露
   - 敏感信息窃取_拆分文件
   - 权限驻留_通过 ld.so 预加载进行代码注入
   - 权限提升_PwnKit 本地权限提升
   - 命令执行_漏洞利用代码中的shell命令
   - 命令执行_反弹shell
   - 权限驻留_Shellshock表达式
   - 命令执行_文件名后出现空格
   - 信息收集_可疑的 /dev/tcp 命令
   - 命令执行_可疑的JexBoss命令
   - 命令执行_创建/etc/passwd的符号链接
   - 防御规避_关闭安全工具
   - 命令执行_Python生成Pretty TTY



2. 网络行为（端口bind、连接connect）





3. 进程行为（创建、杀死、进程执行命令）系统调用/auditd
   - 防御规避_关闭系统防火墙 
   - 权限驻留_创建at/atd计划任务


4. 文件操作（新建、查看、删除、修改）
   - 权限驻留_更改.bash_profile和.bashrc文件
   - 防御规避_更改Audit配置
   - 命令执行_访问BPFDoor .lock 和 .pid 文档
   - 防御规避_更改文档时间属性
   - 防御规避_删除不可变文件的属性
   - 防御规避_隐藏文件和目录
   - 防御规避_更改ld.so.preload
   - 防御规避_更改syslog守护进程的配置
   - 环境信息收集_查看密码策略
   - 凭据获取_操作shell历史文件
   - 权限驻留_创建系统服务
   - 防御规避_清除历史命令
   - 命令执行_敏感文件访问
   - 权限提升_创建doas.conf文件
   - 权限驻留_通过Cron文件持久化
   - 权限驻留_通过sudoers文件持久化
   - 信息收集_查看sudo权限用户
   - 防御规避_清除Linux系统日志
   - 防御规避_Chmod可疑目录
   - 权限驻留_更改<user-home>/.ssh/authorized_keys
   - 权限驻留_更改/etc/ssh/sshd_config
   - 权限驻留
     - su后门：监控/bin/su是否被更改
     - PAM后门:监控pam_unix.so文件，看是否被篡改
     - ssh:
       - 检查/etc/pam.d下的异常创建、写入
       - 监控/etc/pam.d/sshd配置文件
       - 监控/usr/sbin/sshd完整性
       - 监控/usr/bin/ssh完整性
       - /etc/ld.so.preload
     - rookit进程隐藏
       - 监控常见的进程查看工具（比如：ps、top、lsof）等；
     - 进程注入
       - 监视/etc/ld.so.conf和/etc/ld.so.conf.d/*.conf的更改

5. 账户行为
   -  权限驻留_创建用户帐户

6. 内核行为
   - 权限驻留_通过 Insmod 加载内核模块 

7. 登录认证行为
   - 口令爆破 