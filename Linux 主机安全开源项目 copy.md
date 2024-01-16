# 1. Linux 主机安全开源项目

- [1. Linux 主机安全开源项目](#1-linux-主机安全开源项目)
  - [1.1. 前言](#11-前言)
    - [1.1.1. 参考](#111-参考)
    - [1.1.2. 分析维度](#112-分析维度)
  - [1.2. Tracee](#12-tracee)
    - [1.2.1. 架构](#121-架构)
    - [1.2.2. 数据采集层](#122-数据采集层)
    - [1.2.3. 数据分析层](#123-数据分析层)
  - [1.3. Elkeid](#13-elkeid)
    - [1.3.1. 架构](#131-架构)
    - [1.3.2. 核心功能](#132-核心功能)
    - [1.3.3. 技术原理](#133-技术原理)
      - [1.3.3.1. 端上资产/关键信息采集](#1331-端上资产关键信息采集)
      - [1.3.3.2. 日志采集](#1332-日志采集)
      - [1.3.3.3. 基线检测](#1333-基线检测)
      - [1.3.3.4. 恶意文件检测（静态）](#1334-恶意文件检测静态)
      - [1.3.3.5. 运行时应用安全防护](#1335-运行时应用安全防护)
      - [1.3.3.6. 内核层数据采集与 Rootkit 检测](#1336-内核层数据采集与-rootkit-检测)
  - [1.4. Wazuh](#14-wazuh)
    - [1.4.1. 架构](#141-架构)
    - [1.4.2. 数据采集层](#142-数据采集层)
      - [1.4.2.1. Wazuh代理架构](#1421-wazuh代理架构)
      - [1.4.2.2. Wazuh代理功能模块](#1422-wazuh代理功能模块)
      - [1.4.2.3. 与Wazuh服务器通信](#1423-与wazuh服务器通信)
    - [1.4.3. 数据分析层](#143-数据分析层)
  - [1.5. 驭龙 HIDS](#15-驭龙-hids)
    - [1.5.1. 架构](#151-架构)
    - [1.5.2. 数据采集层](#152-数据采集层)
      - [1.5.2.1. 信息收集](#1521-信息收集)
      - [1.5.2.2. 行为监控](#1522-行为监控)
  - [1.6. Hades - eBPF based HIDS](#16-hades---ebpf-based-hids)
    - [1.6.1. 架构](#161-架构)
    - [1.6.2. 数据采集层](#162-数据采集层)



## 1.1. 前言 

### 1.1.1. 参考

https://github.com/topics/hids

https://github.com/topics/cwpp

https://github.com/topics/edr

https://github.com/topics/linux-security

https://github.com/topics/antivirus


### 1.1.2. 分析维度

- 架构
- 业务功能
- 数据采集层
- 数据分析层
- 响应层


## 1.2. Tracee

> Star: 3k  
> Program：Go、C        
> URL: https://github.com/aquasecurity/tracee  
> Tracee 是一个用于 Linux 的运行时安全和取证工具。它使用 Linux eBPF 技术在运行时跟踪系统和应用程序，并分析收集的事件以检测可疑的行为模式。Tracee 以 Docker 镜像的形式交付，监控操作系统并根据预定义的行为模式集检测可疑行为。   

### 1.2.1. 架构

![tracee_Arch](<assets/Linux 主机安全开源项目/image-8.png>)

Tracee 主要由以下两部分组件构成，

1) **Tracee-eBPF** 使用 eBPF 技术向内核插入探针捕获内核层面发生的事件，并将这些受到用户关注的内核事件信息通过 BPF Maps 传递至用户态形成事件流，告知用户当前内核层面正在发生什么，是整个项目的数据来源。

2) **Tracee-Rules** 是一个运行时安全检测引擎，其负责分析 Tracee-eBPF 提交上来的事件流，从而判断在当前安全上下文环境中是否有异常行为发生，通过自定义或内置的规则（Signature/Rules) 产生实时告警，以告知管理员潜在的安全威胁。

### 1.2.2. 数据采集层
tracee-ebpf 在 Tracee 项目中扮演了信息收集者的角色，通过向值得关注的内核函数或事件中插入预定义的探针，收集相关的上下文信息，并最终通过 BPF Maps 将信息汇总至用户态 Go 程序，为用户展示了内核世界发生的图景。

Tracee 项目中将信息收集的模块与事件分析研判的模块进行了解耦分离，这也是大多是安全平台项目的架构思路。

一整个大的安全体系需要由多个具有不同职责的模块共同工作来进行构建，这其中又无外乎有这么几个抽象的功能点，包括，

1) 由某些主体的行为导致的一系列事件的产生，这些事件可能分散于系统的各个部分，发生的时间点也可能有所不同，但他们都是由同一个因导致的。

2) 一个收集的方法，将游离于系统各处（时间和空间上的）的事件进行收集、关联、聚合，成为具有高度上下文语义信息的聚合体。

3) 一个进行分析的方法，有了丰富的上下文环境信息之后，我们需要一个高效的检测方法来识别其中偏离系统基准的行为事件，通俗的来说就是异常事件。

4) 根据检出的异常事件以及当前系统的安全上下文，定义威胁度等级以及相应的行为动作，匹配安全策略。

5) 对进行异常操作的行为主体进行封堵或诱捕，通过预先定义的通知渠道告知相关责任人，最终以求实现安全事件响应处置闭环。

上述每一项功能点都有足够的深度可以进行研究发掘。目前没有来说还没有出现一家独大的场面，各安全厂商也都在积极布局云原生安全，适应这种敏捷轻量的安全体系建设思路。


### 1.2.3. 数据分析层

**在 Tracee 中，一切系统和应用行为皆为事件（Event）**

6 种内置事件类别:
- syscalls
- network
- security
- lsm
- containers
- misc

tracee-rules 支持用户自定义规则（Signatures），目前支持以下三种方式：

- 通过 Golang 编码实现 Signature 接口，用户便可以实现高度定制化的匹配规则与响应逻辑，这也是官方推荐的方式。你也可以使用 Go Plugin 的形式编写 signature，并在程序运行时动态加载，不过这种方式有诸多限制，所以并不被推荐。
- 通过 [Rego](https://www.openpolicyagent.org/docs/latest/#rego) 编写定制化的 Signature. 编写的 rule 文件示例：
```go
package tracee.TRC_2

__rego_metadoc__ := {
        "id": "TRC-2",
        "version": "0.1.0",
        "name": "Anti-Debugging",
        "description": "Process uses anti-debugging technique to block debugger",
        "tags": ["linux", "container"],
        "properties": {
                "Severity": 3,
                "MITRE ATT&CK": "Defense Evasion: Execution Guardrails",
        },
}

tracee_selected_events[eventSelector] {
        eventSelector := {
                "source": "tracee",
                "name": "ptrace",
        }
}

tracee_match {
        input.eventName == "ptrace"
        arg := input.args[_]
        arg.name == "request"
        arg.value == "PTRACE_TRACEME"
}
```

目前，Tracee 内置的 rules 可以通过 `tracee-rules --list` 命令查看，这里截取其中的一部分，
```bash
ID         NAME                                VERSION DESCRIPTION
TRC-2      Anti-Debugging                      0.1.0   Process uses anti-debugging technique to block debugger
TRC-14     CGroups Release Agent File Modification 0.1.0   An Attempt to modify CGroups release agent file was detected. CGroups are a Linux kernel feature which can change a process's resource limitations. Adversaries may use this feature for container escaping.
TRC-3      Code injection                      0.1.0   Possible code injection into another process
TRC-11     Container Device Mount Detected     0.1.0   Container device filesystem mount detected. A mount of a host device filesystem can be exploited by adversaries to perform container escape.
TRC-9      New Executable Was Dropped During Runtime 0.1.0   An Executable file was dropped in your system during runtime. Usually container images are built with all binaries needed inside, a dropped binary may indicate an adversary infiltrated into your container
```
可以看到，tracee-rules 检测引擎的工作本质是对语义化安全策略与源事件信息进行匹配，若事件在上下文语义中携带有策略所指定的信息，便会产生告警，拿上面提到的 `Anti-Debugging` Signature 为例，其具体的策略信息如下所示，

```go
tracee_selected_events[eventSelector] {
        eventSelector := {
                "source": "tracee",
                "name": "ptrace",
        }
}

tracee_match {
        input.eventName == "ptrace"
        arg := input.args[_]
        arg.name == "request"
        arg.value == "PTRACE_TRACEME"
}
```
其中 `eventSelector` 指定了我们关心的事件名称，这本质上就是一个过滤器，与在 `tracee-ebpf` 中指定 `--trace event=ptrace` 相差不大，在 `tracee_match` 中指定了策略所关心的相关 `ptrace` 系统调用入参的信息，其中指定了 `request` 参数值为 `PTRACE_TRACEME` 的 `ptrace` 调用。若在事件流中出现了满足上述策略定义的事件，tracee-rules 便会产生告警。

**Tracee 的内置安全事件**:

|   事件   | 描述 |
| :-------: | :--: |
| Anti-Debugging Technique |检测反调试技术|
|  ASLR Inspection  |检测 ASLR 检查|
| Cgroups notify_on_release File Modification |监控 cgroups 中 notify_on_release 文件的更改|
| Cgroups Release Agent File Modification |检测 cgroup release_agent 的变化|
|   Core Dumps Config File Modification    |监控核心转储配置更改|
| Default Dynamic Loader Modification |跟踪默认二进制加载器的更改|
|    Container Device Mount    |检测未经授权的容器设备安装|
|Docker Socket Abuse|潜在的 Docker 套接字滥用|
|    Dropped Executables   |检测运行时被删除的可执行文件|
|    Dynamic Code Loading   |监控动态代码加载事件|
|    Fileless Execution   |标记无文件执行技术|
|  Hidden Executable File Creation  |检测隐藏可执行文件的创建|
| Illegitimate Shell |标记未经授权或意外的 shell 执行|
|  Kernel Module Loading  |监控内核模块加载事件|
|Kubernetes API Server Connection|检测与 Kubernetes API 服务器的连接|
|Kubernetes TLS Certificate Theft|监控 Kubernetes 证书被盗|
|   LD_PRELOAD Code Injection    |监控 LD_PRELOAD 注入尝试|
|File Operations Hooking on Proc Filesystem|检测 /proc 文件操作hook|
|Kcore Memory File Read|监控 /proc/kcore 的读取|
|Process Memory Access|未经授权的 /proc/mem 访问|
|Procfs Mem Code Injection|检测 /proc/mem 中的代码注入|
|Process VM Write Code Injection|监控通过 process_vm_writev 进行代码注入| 
|Ptrace Code Injection|检测通过 ptrace 代码注入|
|RCD Modification|监控远程控制守护进程的变化|
|Sched Debug Reconnaissance|对 /proc/sched_debug 侦查|
|Scheduled Tasks Modification|跟踪对计划任务的修改|
|Process Standard Input/Output over Socket|通过套接字检测 IO 重定向|
|Sudoers File Modification|监控对 sudoers 文件的更改|
|Syscall Table Hooking|检测系统调用表hook尝试|
|System Request Key Configuration Modification	|监控系统请求键配置更改|

## 1.3. Elkeid

Bytedance Cloud Workload Protection Platform

> Star: 1.9k  
> 
> URL: https://github.com/bytedance/Elkeid
>   
> About: Elkeid 是一款可以满足 主机，容器与容器集群，Serverless 等多种工作负载安全需求的开源解决方案，源于字节跳动内部最佳实践。  
> 
> Program：Go、C、Rust、C++

### 1.3.1. 架构

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


### 1.3.2. 核心功能

**资产盘点**
统一管理主机列表、容器列表、进程、Java进程依赖信息、端口、账号、系统组件、系统服务、定时任务、系统完整性等资产指纹信息，帮助企业资产可视化。


**安全基线**
支持弱口令、等保二级、等保三级等基线标准检测。

**入侵检测**
对主机与容器的入侵行为进行检测并提供处置方案，包括：反弹shell、本地提权、容器逃逸、后门木马、内核态后门、恶意命令、可疑命令序列、暴力破解等。

**RASP**
对应用运行时入侵行为进行实时检测并提供处置方案，包括：各类内存马、各类RCE、SSRF、ONGL注入、可疑命令序列等，且支持热补丁技术。

**云原生防护**
实现对云原生环境内的攻击行为、不安全资源实时监控，并提供对安全风险的溯源分析和大盘展示等能力。帮助企业进行入侵响应和风险分析。

**漏洞检测**
对主机、应用上存在的漏洞风险进行全面监测，包括系统组件漏洞、应用漏洞等，帮助企业应对漏洞风险。


### 1.3.3. 技术原理

#### 1.3.3.1. 端上资产/关键信息采集

1. **进程**：
> 支持对exe md5的哈希计算，后续可关联威胁情报分析   
> 与容器信息进行关联，支撑后续数据溯源功能(跨容器)

获取进程信息目录
```bash
$ ll /proc
total 0
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 1
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 10
dr-xr-xr-x  5 root      root              0 Feb  8 17:08 11
```

采集信息以 pid=2406 的进程为例子：

**cmdLine**  启动当前进程的完整命令行信息
```bash
$ cat /proc/2406/cmdline
frps-c./frps.ini
```

**cwd** 当前进程运行目录的符号链接
```bash
$ ls -lt /proc/2406/cwd
lrwxrwxrwx 1 root root 0 Dec 12 20:39 /proc/2406/cwd -> /home/mike/frp_0.13.0_linux_amd64
```

**md5** 进程exe的md5校验和，需要计算
```bash
$ ls -lt /proc/2406/exe
lrwxrwxrwx 1 root root 0 Dec 11 19:00 /proc/2406/exe -> /usr/bin/frps
```

**exe** 实际运行程序的符号链接
```bash
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
```bash
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
```bash
$ cat /proc/2406/stat
2406 (frps) S 2 0 0 0 -1 69247072 0 0 0 0 0 2453 0 0 20 0 1 0 70730273 0 0 18446744073709551615 0 0 0 0 0 0 0 2147483647 0 18446744072085041241 0 0 17 1 0 0 328 0 0 0 0 0 0 0 0 0 0
```


2. **端口**
> 支持tcp、udp监听端口的信息提取，以及与进程、容器信息的关联上报。  
> 基于sock状态及其关系，分析对外暴露服务，向上支撑主机暴露面分析功能。(跨容器)

```go
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


3. **账户**
  - 除了基本的账户字段外，基于弱口令字典进行端上hash碰撞检测弱口令。另外，会关联分析sudoers配置，一同上报。
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


```bash
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
```
格式：
`username:password:uid:gid:info:home:shell`

补充 password:
```bash
$ cat /etc/shadow
root:$6$sD8gshA6$d7rXAxa11g8uP4PRkrfSZ7rkYPNCYFXEltyoTbUUj3OPr5vWqlLmGJwRS9c5PidnWaqx/5QkT.BtpU2DUb2mG/:19619:0:99999:7:::
bin:*:17834:0:99999:7:::
daemon:*:17834:0:99999:7:::
adm:*:17834:0:99999:7:::
lp:*:17834:0:99999:7:::
sync:*:17834:0:99999:7:::

```





4. **内核模块**
  - 采集基本字段，以及内存地址、依赖关系等额外字段。

```go
type Kmod struct {
    Name
    size
    refCount
    usedBy
    state
    addr
}
```


```bash
[root@hecs-393810 proc]# cat modules
xt_conntrack 12760 3 - Live 0xffffffffc03e4000
ipt_MASQUERADE 12678 3 - Live 0xffffffffc03da000
nf_nat_masquerade_ipv4 13463 1 ipt_MASQUERADE, Live 0xffffffffc03df000
nf_conntrack_netlink 36396 0 - Live 0xffffffffc03d0000
iptable_nat 12875 1 - Live 0xffffffffc03cb000
nf_conntrack_ipv4 15053 4 - Live 0xffffffffc03c6000

```


5. **系统服务**
  - 兼容不同发行版下的服务，并对核心字段进行解析。
```go
type Service struct {
    Name       
    Type     
    Command 
    Restart   
    WorkingDir 
    Checksum   
    BusName    
}
```




6. **定时任务**
  - 兼容不同发行版下cron位置的定义，并对核心字段进行解析。
```go
type Crontab struct {
	Path     
	Username 
	Schedule 
	Command  
	Checksum 
}

```
7. **软件** 
  - 支持系统软件包、pypi包、jar包，向上支撑漏洞扫描功能。(部分跨容器)
8. **容器** 
  - 支持docker、cri/containerd等多种运行时下的容器信息采集。
9. **应用**
  - 支持数据库、消息队列、容器组件、Web服务、DevOps工具等类 型的应用采集、目前支持30+中常见应用的版本、配置文件的匹配与提取。(跨容器)
10. **硬件**
  - 支持网卡、磁盘等硬件信息的采集。


#### 1.3.3.2. 日志采集

- 认证授权日志
  - `/var/log/secure`
  - `/var/log/auth`
- 安全审计日志
  - `/var/log/audit`
- 整体系统日志
  - `/var/log/messages`

#### 1.3.3.3. 基线检测

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



#### 1.3.3.4. 恶意文件检测（静态）
ClamAV 引擎 + Yara 规则

#### 1.3.3.5. 运行时应用安全防护

#### 1.3.3.6. 内核层数据采集与 Rootkit 检测

## 1.4. Wazuh

> Star: 7.8k    
> URL: https://github.com/wazuh/wazuh      
> Program：C、Python、C++  
> The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads.

Wazuh 是一个用于威胁预防、检测和响应的免费开源平台。它能够保护企业内部、虚拟化、容器化和云环境中的工作负载。

Wazuh 解决方案由部署到受监控系统的端点安全代理和管理服务器组成，后者负责收集和分析代理收集的数据。此外，Wazuh 还与 Elastic Stack 完全集成，提供了一个搜索引擎和数据可视化工具，使用户能够浏览安全警报。

### 1.4.1. 架构
![wazuh架构](<assets/Linux 主机安全开源项目/image-9.png>)

Wazuh平台主要包括三个主要组件，分别是Wazuh代理，Wazuh服务器和Elastic Stack。

- Wazuh代理：它安装在端点上，例如笔记本电脑，台式机，服务器，云实例或虚拟机。它提供了预防，检测和响应功能。它确实支持Windows，Linux，macOS，HP-UX，Solaris和AIX平台。
- Wazuh服务器：它分析从代理收到的数据，通过解码器和规则对其进行处理，并使用威胁情报来查找众所周知的危害指标（IOC）。一台服务器可以分析来自成百上千个代理的数据，并在设置为集群时水平扩展。该服务器还用于管理代理，在必要时进行远程配置和升级。
- Elastic Stack：它索引和存储Wazuh服务器生成的警报。此外，Wazuh和Kibana之间的集成为数据的可视化和分析提供了强大的用户界面。该界面还可用于管理Wazuh配置并监视其状态。

### 1.4.2. 数据采集层
Wazuh代理可在Linux，Windows，macOS，Solaris，AIX和其他操作系统上运行。它可以部署到笔记本电脑，台式机，服务器，云实例，容器或虚拟机。它提供了威胁预防，检测和响应功能。它还可用于收集不同类型的系统和应用程序数据，然后通过加密并经过身份验证的通道将其转发到Wazuh服务器。

#### 1.4.2.1. Wazuh代理架构
Wazuh代理具有模块化体系结构，其中不同的组件负责各自的任务：监视文件系统，读取日志消息，收集清单数据，扫描系统配置，查找恶意软件等。用户可以通过配置启用或禁用代理模块设置，使解决方案适应其特定的用例。

![wazuh agent](<assets/Linux 主机安全开源项目/image-10.png>)

#### 1.4.2.2. Wazuh代理功能模块

- 日志收集器：此代理组件可以读取平面日志文件和Windows事件，收集操作系统和应用程序日志消息。它确实支持Windows事件的XPath过滤器，并且可以识别多行格式（例如Linux审核日志）。它还可以使用其他元数据来丰富JSON事件。
- 命令执行：代理可以定期运行授权命令，收集其输出并将其报告回Wazuh服务器以进行进一步分析。此模块可用于满足不同的目的（例如，监视剩余的硬盘空间，获取上次登录用户的列表等）。
- 文件完整性监视（FIM）：此模块监视文件系统，并在创建，删除或修改文件时报告。它跟踪文件属性，权限，所有权和内容。事件发生时，它将实时捕获谁，什么以及何时显示详细信息。此外，该模块使用受监视文件的状态构建和维护数据库，从而允许远程运行查询。
- 安全配置评估（SCA）：此组件利用基于Internet安全中心（CIS）基准的即用型检查提供连续的配置评估。用户还可以创建自己的SCA检查来监视和实施其安全策略。
- 系统清单：此代理模块定期运行扫描，收集清单数据，例如操作系统版本，网络接口，运行的进程，已安装的应用程序以及打开的端口列表。扫描结果存储在本地SQLite数据库中，可以远程查询。
- 恶意软件检测：使用基于非签名的方法，此组件能够检测异常和rootkit的可能存在。监视系统调用，它将查找隐藏的进程，隐藏的文件和隐藏的端口。
- 主动响应：检测到威胁时，此模块将自动执行操作。它尤其可以阻止网络连接，停止正在运行的进程或删除恶意文件。用户也可以在必要时创建自定义响应（例如，在沙箱中运行二进制文件，捕获网络连接流量，使用防病毒软件扫描文件等）。
- 容器安全监视：此代理模块与Docker Engine API集成在一起，以监视容器化环境中的更改。例如，它检测到容器映像，网络配置或数据量的更改。此外，它还警告以特权模式运行的容器以及正在运行的容器中执行命令的用户。
- 云安全监视：此组件监视诸如Amazon AWS，Microsoft Azure或Google GCP之类的云提供商。它与它们的API进行本地通信。它能够检测云基础架构的更改（例如，创建新用户，修改安全组，停止云实例等），并收集云服务日志数据（例如，AWS Cloudtrail，AWS Macie，AWS GuardDuty ，Azure Active Directory等）

#### 1.4.2.3. 与Wazuh服务器通信

Wazuh代理与Wazuh服务器进行通信，以便运送收集的数据和与安全性相关的事件。此外，代理发送操作数据，报告其配置和状态。连接后，可以从Wazuh服务器远程升级，监视和配置代理。

Wazuh代理与服务器的通信通过安全通道（TCP或UDP）进行，实时提供数据加密和压缩。此外，它包括流控制机制，可避免泛洪，在必要时对事件进行排队并保护网络带宽。

在第一次将Wazuh代理连接到服务器之前，必须先注册Wazuh代理。此过程为代理提供了唯一的预共享密钥，该密钥用于身份验证和数据加密。

### 1.4.3. 数据分析层
Wazuh服务器组件负责分析从代理接收的数据，并在检测到威胁或异常时触发警报。它还用于远程管理代理配置并监视其状态。

Wazuh服务器运行分析引擎，Wazuh RESTful API，代理注册服务，代理连接服务，Wazuh集群守护程序和Filebeat。下图表示服务器体系结构和组件：

![wazuh通信](<assets/Linux 主机安全开源项目/image-11.png>)

该服务器通常在独立的物理机，虚拟机，docker容器或云实例上运行。它安装在Linux操作系统上。以下是主要服务器组件的列表：

- 代理注册服务：用于通过供应和分配每个代理唯一的预共享身份验证密钥来注册新代理。此过程作为网络服务运行，并支持通过TLS / SSL证书或提供固定密码进行身份验证。
- 代理连接服务：这是从代理接收数据的服务。它利用预共享密钥来验证每个代理身份并加密代理与Wazuh服务器之间的通信。此外，此服务用于提供集中的配置管理，能够远程推送新的代理设置。
- 分析引擎：这是执行数据分析的过程。它利用解码器来识别正在处理的信息的类型（例如Windows事件，SSHD日志，Web服务器日志等），并从日志消息中提取相关的数据元素（例如源IP地址，事件ID，用户名等）。 。接下来，通过使用规则，它可以识别解码事件中的特定模式，这些特定模式可能会触发警报，甚至可能要求采取自动对策（例如，防火墙上的IP禁止）。
- Wazuh RESTful API：此服务提供了与Wazuh基础结构进行交互的接口。它用于管理代理和服务器配置设置，监视基础结构状态和总体运行状况，管理和编辑Wazuh解码器和规则，以及查询受监视端点的状态。Wazuh Web用户界面（Kibana应用程序）也使用它。
- Wazuh群集守护程序：此服务用于水平扩展Wazuh服务器，将它们部署为群集。这种配置与网络负载平衡器相结合，可提供高可用性和负载平衡。Wazuh群集守护程序是Wazuh服务器用来相互通信并保持同步的服务器。
- Filebeat：用于将事件和警报发送到Elasticsearch。它读取Wazuh分析引擎的输出并实时发送事件。当连接到多节点Elasticsearch集群时，它还提供负载平衡。

## 1.5. 驭龙 HIDS

> Star: 2.1k  
> Program：Go   
> URL: https://github.com/ysrc/yulong-hids-archived 
> 驭龙HIDS是一款由 YSRC 开源的入侵检测系统，由 Agent， Daemon， Server 和 Web 四个部分组成，集异常检测、监控管理为一体，拥有异常行为发现、快速阻断、高级分析等功能，可从多个维度行为信息中发现入侵行为。 

### 1.5.1. 架构

![yulongArch](<assets/Linux 主机安全开源项目/image-7.png>)

**Agent**为采集者角色，收集服务器信息、开机启动项、计划任务、监听端口、服务、登录日志、用户列表，实时监控文件操作行为、网络连接、进程创建，初步筛选整理后通过RPC协议传输到Server节点。

**Daemon**为守护服务进程，为Agent提供进程守护、静默环境部署作用，其任务执行功能通过接收服务端的指令实现Agent热更新、阻断功能和自定义命令执行等，任务传输过程使用RSA进行加密。

**Server**为整套系统的大脑，支持横向扩展分布式部署，解析用户定义的规则（已内置部分基础规则）对从各Agent接收到的信息和行为进行分析检测和保存，可从各个维度的信息中发现webshell写入行为、异常登录行为、异常网络连接行为、异常命令调用行为等，从而实现对入侵行为实时预警。


### 1.5.2. 数据采集层

#### 1.5.2.1. 信息收集

- 服务器信息

```go
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

```go
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
```go
name // 计划任务名
command // 要执行的程序或命令以及参数
arg // 启动参数
user // 启动用户
rule
description // 描述
```

- 监听端口
TCP监听端口 `ss -nltp`
```go
proto // 类型
address // 监听地址
name // 监听程序名
pid // 监听程序pid
```

- 服务
```go
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
```go
username // 用户名
hostname // 远程主机名
remote // 远程IP
status // 认证结果
time // 时间
```

- 用户列表
`/etc/passwd` 忽略 `/nologin` 用户

- Web目录


#### 1.5.2.2. 行为监控

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
```go
name // 进程名
command // 程序或命令以及参数
pid // 进程pid
ppid // 父进程pid
parentname // 父进程名
info // 进程其他相关信息
```

## 1.6. Hades - eBPF based HIDS

> Star: 260 
> URL: https://github.com/chriskaliX/Hades/tree/main  
> Program：C、Rust、Go  
> Hades 是一个基于 eBPF 的主机入侵检测系统，同时兼容低版本下通过 netlink(cn_proc) 进行事件审计，借鉴了Tracee和Elkeid 


### 1.6.1. 架构

**Agent**

![hades-architecture-agent](<assets/Linux 主机安全开源项目/image-4.png>)

**Data Analysis**

![hades-architecture-data-analysis](<assets/Linux 主机安全开源项目/image-5.png>)

### 1.6.2. 数据采集层

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
