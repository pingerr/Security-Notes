# OWASP Core Rule Set 介绍

- [OWASP Core Rule Set 介绍](#owasp-core-rule-set-介绍)
  - [1. 概述](#1-概述)
  - [2. WAF 引擎集成](#2-waf-引擎集成)
    - [2.1. 开源WAF](#21-开源waf)
      - [2.1.1. ModSecurity v2](#211-modsecurity-v2)
      - [2.1.2. ModSecurity v3](#212-modsecurity-v3)
      - [2.1.3. Coraza](#213-coraza)
    - [2.2. 商业WAF](#22-商业waf)
      - [2.2.1. HAProxy Technologies](#221-haproxy-technologies)
      - [2.2.2. Cloudflare WAF](#222-cloudflare-waf)
      - [2.2.3. Fastly](#223-fastly)
      - [2.2.4. Google Cloud Armor](#224-google-cloud-armor)
      - [2.2.5. Microsoft Azure WAF](#225-microsoft-azure-waf)
      - [2.2.6. Oracle WAF](#226-oracle-waf)
  - [3. Coraza WAF](#3-coraza-waf)
    - [3.1. 简介](#31-简介)
    - [3.2. 快速使用](#32-快速使用)
      - [3.2.1. 创建一个WAF实例](#321-创建一个waf实例)
      - [3.2.2. 添加规则到WAF实例中](#322-添加规则到waf实例中)
      - [3.2.3. 创建事务 transaction](#323-创建事务-transaction)
      - [3.2.4. 处理中断](#324-处理中断)
      - [3.2.5. 处理请求](#325-处理请求)
      - [3.2.6. 集成 OWASP Core Rule Set](#326-集成-owasp-core-rule-set)
      - [3.2.7. 使用 Plugin 插件](#327-使用-plugin-插件)
  - [4. OWASP CRS 核心机制](#4-owasp-crs-核心机制)
    - [4.1. 异常评分机制（ANOMALY SCORING）](#41-异常评分机制anomaly-scoring)
      - [4.1.1. 简介](#411-简介)
      - [4.1.2. 异常评分机制如何运作](#412-异常评分机制如何运作)
    - [4.2. 偏执等级（PARANOIA LEVELS）](#42-偏执等级paranoia-levels)
      - [4.2.1. 简介](#421-简介)
      - [4.2.2. 4种偏执级别](#422-4种偏执级别)
  - [5. OWASP CRS 规则](#5-owasp-crs-规则)
    - [5.1. 规则 ID](#51-规则-id)
      - [5.1.1. 规则 ID 预留](#511-规则-id-预留)
      - [5.1.2. OWASP CRS 中的 ID](#512-owasp-crs-中的-id)
    - [5.2. 规则说明](#52-规则说明)
    - [5.3. 规则编写](#53-规则编写)
      - [5.3.1. 基本语法](#531-基本语法)


## 1. 概述

OWASP®（开放全球应用程序安全项目）CRS（核心规则集）是一个免费的开源规则集合，可与 ModSecurity® 以及兼容的 Web 应用程序防火墙 (WAF) 配合使用。这些规则旨在为 Web 应用程序提供易于使用的通用攻击检测功能，并将误报（误报）降至最低，作为均衡的深度防御解决方案的一部分。

该项目使用 Apache License 2.0。尽管它最初是为 ModSecurity 的 SecRules 语言开发的，但该规则集可以而且经常被自由修改、复制和改编以用于各种商业和非商业活动。CRS 项目鼓励个人和组织尽可能为 OWASP 核心规则集做出贡献。

## 2. WAF 引擎集成

### 2.1. 开源WAF

#### 2.1.1. ModSecurity v2
ModSecurity v2 最初是 Apache Web 服务器的安全模块，是CRS 的参考实现。

ModSecurity 2.9.x 在 Apache 平台上通过了 100% 的 CRS 单元测试。

ModSecurity 在 Apache License 2.0 下发布。它主要由 Trustwave 公司的 Spiderlabs 开发。2021 年夏天，Trustwave 宣布计划于 2024 年结束 ModSecurity 的开发。为了保证项目的持续性，说服 Trustwave 移交该项目的尝试失败了。Trustwave 表示，他们不会在 2024 年之前放弃对该项目的控制权。

#### 2.1.2. ModSecurity v3
ModSecurity v3 也称为libModSecurity，是 ModSecurity v3 的重新实现，其架构较少依赖于 Web 服务器。独立 ModSecurity 和 Web 服务器之间的连接是使用连接器模块建立的。

截至 2021 年春季，只有 Nginx 连接器模块真正可在生产中使用。

由于错误和实施差距，ModSecurity v3 的 CRS 单元测试中有 2-4% 失败。与 Apache + ModSecurity v2 平台相比，Nginx + ModSecurity v3 也存在性能问题。

#### 2.1.3. Coraza

新的 OWASP Coraza WAF 旨在为两个 ModSecurity 版本系列提供开源替代方案。

Coraza 100% 通过了 CRS v4 测试套件，因此与 CRS 完全兼容。

Coraza 是用 Go 开发的，目前运行在 Caddy 和 Traefik 平台上。其他端口正在开发中，开发人员还寻求将 Coraza 引入 Nginx，并最终引入 Apache。在此次扩展的同时，Coraza 将进一步开发其自己的功能集。


### 2.2. 商业WAF
数十个商业 WAF（无论是虚拟的还是基于硬件的）都提供 CRS 作为其服务的一部分。其中许多在底层使用 ModSecurity 或某种替代实现。

#### 2.2.1. HAProxy Technologies
HAProxy Technologies 通过 Libmodsecurity 模块将 ModSecurity v3 嵌入到其三个产品中。ModSecurity 包含在：HAProxy Enterprise、HAProxy ALOHA 和 HAProxy Enterprise Kubernetes Ingress Controller 中。

#### 2.2.2. Cloudflare WAF
Cloudflare WAF 支持将 CRS 作为其 WAF 规则集之一。

#### 2.2.3. Fastly
Fastly 一直将 CRS 作为其 Fastly WAF 的一部分，但他们已开始将现有客户迁移到最近收购的 Signal Sciences WAF。有趣的是，Fastly 正在将 CRS 规则移植到自己基于 Varnish 的 WAF 引擎中。

#### 2.2.4. Google Cloud Armor
Google 将 CRS 集成到其 Cloud Armor WAF 产品中。谷歌在自己的 WAF 引擎上运行 CRS 规则。从 2022 年秋季开始，谷歌提供 3.3.2 版本的 CRS。

Google Cloud Armor 是 CRS 的赞助商。

#### 2.2.5. Microsoft Azure WAF
Azure 应用网关可配置为使用 WAFv2 和不同版本 CRS 的托管规则。Azure 提供 3.2、3.1、3.0 和 2.2.9 CRS 版本。

#### 2.2.6. Oracle WAF
Oracle WAF 是一种基于云的产品，也集成了 CRS。


## 3. Coraza WAF 

### 3.1. 简介

Coraza 是一款开源、企业级、高性能 Web 应用程序防火墙 (WAF)。它用 Go 编写，支持 ModSecurity SecLang 规则集，并且与 OWASP Core Rule Set 100% 兼容。

**关键特性：**
- **直接集成**- Coraza 是替代 Trustwave ModSecurity Engine （计划于 2024 年 7 月 1 日弃用）的直接替代方案，并支持行业标准 SecLang 规则集。
- **安全性**- Coraza 运行OWASP 核心规则集 (CRS)，以保护您的 Web 应用程序免受各种攻击，包括 OWASP Top 10，并最大限度地减少误报。CRS 可防御许多常见的攻击类别，包括 SQL 注入 (SQLi)、跨站脚本 (XSS)、PHP 和 Java 代码注入、HTTPoxy、Shellshock、脚本/扫描程序/机器人检测以及元数据和错误泄漏。
- **可扩展性** Coraza 的核心是一个库，具有许多集成来部署本地 Web 应用程序防火墙实例。审核记录器、持久性引擎、操作员、操作，创建您自己的功能来根据您的需要扩展 Coraza。
- **高性能** 从大型网站到小型博客，Coraza 可以以最小的性能影响处理负载。
- **简单性** 任何人都可以理解和修改 Coraza 源代码。使用新功能扩展 Coraza 很容易。

### 3.2. 快速使用

**要求**
- Golang 1.18+

#### 3.2.1. 创建一个WAF实例
WAF 实例是设置和规则的主要容器，这些设置和规则由处理请求、响应和日志的事务继承。WAF 实例可以这样创建：
```go
package main
import (
  "github.com/corazawaf/coraza/v3"
)
func initCoraza(){
  cfg := coraza.NewWafConfig()
  waf, err := coraza.NewWaf(cfg)
}
```
#### 3.2.2. 添加规则到WAF实例中
Seclang 规则语法用于创建 Coraza 规则，这些规则将对 transaction 进行评估，并应用诸如 deny(403) 或仅记录事件等操作。
可使用 coraza.NewWAFConfig().WithDirectives() 方法添加规则：
```go
package main

import (
  "github.com/corazawaf/coraza/v3"
)

func createWAF() coraza.WAF {
  waf, err := coraza.NewWAF(coraza.NewWAFConfig().WithDirectives(`SecAction "id:1,phase:1,deny:403,log"`))
  if err != nil {
    panic(err)
  }
  return waf
}
```

#### 3.2.3. 创建事务 transaction
事务是为每个 http 请求创建的，它们是并发安全的，可以分为多个阶段（Phases）处理以评估规则并生成审计日志和中断。

阶段（Phases）是一个抽象概念，旨在适应大多数网络服务器的执行流程，并为其提供更多停止请求的机会。

![phases](<assets/OWASP Core Rule Set 介绍/image.png>)

**Phase 1: Request Headers**
该阶段将处理包含以下变量的规则：
- HTTP 连接数据，如 IP、端口和协议版本
- URI 和 GET 参数
- 请求头： cookies, content-type 和 content-length 等

**Phase 2: Request Body**
该阶段将处理包含以下变量的规则：
- POST 参数
- Multipart 参数和 Files
- JSON 和 XML 数据
- Raw Request Body

**Phase 3: Response Headers**
该阶段将处理包含以下变量的规则：
- 响应状态码
- 响应头：content-length、content-type等

**Phase 4: Response Body**
该阶段将处理包含以下变量的规则：
- Raw Response body

**Phase 5: Logging**
该阶段将评估第 5 阶段规则、保存持久性集合并写入日志条目。该阶段不会中断，可以在向客户端发送响应后运行。

使用 waf.NewTransaction() 创建事务。还可以使用 waf.NewTransactionWithID(id) 指定事务的 ID。

#### 3.2.4. 处理中断
事务创建中断，以根据操作规则告诉 Web 服务器或应用程序需要执行哪些操作。可以使用 检索中断tx.Interruption()，零中断意味着不需要执行任何操作（通过），非零中断意味着 Web 服务器必须执行诸如拒绝请求之类的操作。例如：
```go
//...
tx := waf.NewTransaction()
// Add some variables and process some phases
if it := tx.Interruption();it != nil {
  switch it.Action() {
    case "deny":
      rw.WriteStatus(it.Status())
      rw.Write([]byte("Some error message"))
      return
  }
}
```

#### 3.2.5. 处理请求
有两种方法可以处理请求，您可以手动处理请求的每个阶段，也可以向 Coraza 发送 http.Request。

要处理 http.Request 结构，您必须使用tx.ProcessRequest(req)。ProcessRequest 将评估阶段 1 和阶段 2，并在事务中断时停止执行流程。重要提示：req.Body 将被读取并替换为新指针，指向 Coraza 创建的缓冲区或文件。

要手动处理请求，我们必须按以下顺序运行 5 个函数：
- **ProcessConnection**：使用 IP 地址和端口等连接信息创建变量。
- **ProcessUri**：从请求行中提取的字符串创建变量，这些变量是method、url 和协议。
- **AddRequestHeader**：必须为每个 HTTP 头运行，它将创建请求头、请求变量和 cookie。
- **ProcessRequestHeaders**：使用之前所有变量评估第一阶段规则。这个功能是破坏性的。
- **RequestBodyBuffer.Write**：写入请求正文缓冲区，可以使用 io.Copy(tx.RequestBodyBuffer, someReader)
- **ProcessRequestBody**：使用 POST 变量评估第 2 阶段规则。还有其他情况，如 MULTIPART、JSON 和 XML.

```go
tx := waf.NewTransaction()
// 127.0.0.1:55555 -> 127.0.0.1:80
tx.ProcessConnection("127.0.0.1", 55555, "127.0.0.1", 80)
// Request URI was /some-url?with=args
tx.ProcessURI("/some-url?with=args")
// We add some headers
tx.AddRequestHeader("Host", "somehost.com")
tx.AddRequestHeader("Cookie", "some-cookie=with-value")
// Content-Type is important to tell coraza which BodyProcessor must be used
tx.AddRequestHeader("Content-Type", "application/x-www-form-urlencoded")
// We process phase 1 (Request)
if it := tx.ProcessRequestHeaders();it != nil {
  return processInterruption(it)
}
// We add urlencoded POST data
tx.RequestBodyBuffer.Write([]byte("somepost=data&with=paramenters"))
// We process phase 2 (Request Body)
if it := tx.ProcessRequestBody();it != nil {
  return processInterruption(it)
}
```

#### 3.2.6. 集成 OWASP Core Rule Set
```go
func initCoraza(){
  cfg := coraza.NewWafConfig()
    .WithDirectivesFromFile("coraza.conf")
    .WithDirectivesFromFile("coreruleset/crs-setup.conf.example")
    .WithDirectivesFromFile("coreruleset/rules/*.conf")
  waf, err := coraza.NewWaf(cfg)
  if err != nil {
    panic(err)
  }
}
```

#### 3.2.7. 使用 Plugin 插件
插件可以扩展 Coraza 功能，例如审计日志记录、地理 IP、运算符、操作、转换和body处理器。

插件是通过调用相应的API来导入的：
- plugins.RegisterOperator(...)
- plugins.RegisterAction(...)
- plugins.RegisterBodyProcessor(...)
- plugins.RegisterTransformation(...)


## 4. OWASP CRS 核心机制

### 4.1. 异常评分机制（ANOMALY SCORING）

#### 4.1.1. 简介
CRS 3 被设计为异常评分规则集。

异常评分，也称为“协作检测”，是核心规则集中使用的一种评分机制。它为 HTTP 事务（请求和响应）分配一个数字分数，表示它们的“异常”程度。然后可以使用异常分数来做出阻止决策。例如，默认的 CRS 阻止策略是阻止任何满足或超过定义的异常分数阈值的交易。

#### 4.1.2. 异常评分机制如何运作
异常评分机制结合了协作检测和延迟阻止的概念。要理解的关键思想是**检查/检测规则逻辑与阻止功能分离**。

执行旨在检测特定类型的攻击和恶意行为的单独规则。如果规则匹配，则不会立即采取破坏性操作（例如，事务不会被阻止）。相反，匹配的规则会产生事务异常分数，该分数充当运行总计。这些规则仅处理检测，如果匹配则添加异常分数。此外，单个匹配规则通常会记录匹配记录以供以后参考，包括匹配规则的 ID、导致匹配的数据以及所请求的 URI。

一旦执行了所有检查请求数据的规则，就会进行阻塞评估。如果异常分数大于或等于入站异常分数阈值，则交易将被拒绝。未被拒绝的交易将继续其旅程。
![异常评分](<assets/OWASP Core Rule Set 介绍/image-1.png>)
一旦执行了所有检查响应数据的规则，就会发生第二轮阻塞评估。如果出站异常分数大于或等于出站异常分数阈值，则不将响应返回给用户。

**异常评分机制流程总结**
CRS 中的异常评分机制的工作原理如下：
1. 执行所有请求规则
2. 使用入站异常分数阈值做出阻止决策
3. 执行所有响应规则
4. 使用出站异常分数阈值做出阻止决策



### 4.2. 偏执等级（PARANOIA LEVELS）

#### 4.2.1. 简介
**偏执等级 (PL) 可以定义核心规则集的激进度**。偏执级别 1（PL 1）提供了一套几乎不会触发误报的规则（理想情况下永远不会，但也有可能发生，这取决于本地设置）。偏执等级 2 提供了可检测更多攻击的附加规则（这些规则在偏执等级 1 规则之外运行），但附加规则也有可能在完全合法的 HTTP 请求上触发新的误报。
PL 3 中增加了更多针对某些专门攻击的规则，这将导致更多的误报。到了 PL 4，规则变得非常激进，几乎能检测到所有可能的攻击，但也会将大量合法流量标记为恶意流量。

![PL](<assets/OWASP Core Rule Set 介绍/image-2.png>)

偏执程度越高，攻击者就越难不被发现。然而，这样做的代价是更多的误报。这就是运行几乎能检测到一切的规则集的弊端：正常业务也会受到干扰。

一旦出现误报，就需要对其进行调整。用 ModSecurity 术语来说，就是需要编写规则排除。规则排除是指禁用某一条规则的规则，要么完全禁用，要么仅针对某些参数或某些 URI 部分禁用。这意味着规则集保持不变，但不再受误报影响。

#### 4.2.2. 4种偏执级别

|偏执级别|描述|
|:--:|:--:|
|1|基本安全性，只需极少的必要性即可消除误报。这是适用于在互联网上运行 HTTP 服务器的每个人的 CRS。|
|2|当涉及真实用户数据时，规则就足够了。|
|3|银行级别的安全性存在大量误报。从项目的角度来看，误报在这里是可以接受和预期的，因此学习如何编写规则排除非常重要。|
|4|规则足够强大（或偏执），但需要做好面对大量误报的准备。|


## 5. OWASP CRS 规则

### 5.1. 规则 ID
添加到 WAF 实例的每个规则都必须具有唯一的 ID。因此，以预先计划的方式生成 ID 非常重要，这样它们就不会与其他规则重叠。当涉及到规则集（例如 OWASP CRS）时，这一点变得尤为重要。由于 CRS 可以与其他规则集一起使用（例如，事实上它被设计为与 Trustwave 商业规则一起使用），因此两个规则集不能有重叠的 ID 范围，这一点很重要。为了帮助完成此过程，该项目维护了一个保留的 ID 空间列表。

#### 5.1.1. 规则 ID 预留

- 1-99,999；保留供本地（内部）使用。按照您认为合适的方式使用，但不要将此范围用于分发给其他人的规则。
- 100,000–199,999；为 Oracle 发布的规则保留。
- 200,000–299,999；保留用于 Comodo 发布的规则。
- 300,000-399,999；保留用于在 gotroot.com 上发布的规则。
- 400,000–419,999；未使用（可预订）。
- 420,000-429,999；为 ScallyWhack 保留。
- 430,000–439,999：保留给 Flameeyes 发布的规则
- 440,000-599,999；未使用（可预订）。
- 600,000-699,999；保留供 Akamai 使用。
- 700,000-799,999；为伊万·里斯蒂奇保留。
- 900,000-999,999；为 OWASP ModSecurity 核心规则集项目保留。
- 1,000,000-1,009,999；保留用于 Redhat 安全团队发布的规则
- 1,010,000-1,999,999；未使用（可预约）
- 2,000,000-2,999,999；保留 Trustwave 的 SpiderLabs 研究团队的规则
- 3,000,000-3,999,999；保留供 Akamai 使用
- 4,000,000-4,099,999；保留供 AviNetworks 使用
- 4,100,000-4,199,999；保留供 Fastly 使用
- 4,200,000-8,999,999；未使用（可预约）
- 9,000,000-9,999,999；为 OWASP ModSecurity 核心规则集项目保留。
- 10,000,000-89,999,999；未使用（可预约）
- 99,000,000-99,099,999：保留供 Microsoft 使用

#### 5.1.2. OWASP CRS 中的 ID

OWASP 核心规则集 (CRS) 中的 ID 具有特殊含义。根据规则在规则集中的位置为规则分配一个 ID。正如上面的列表所示，OWASP 核心规则集分配的 ID 范围为 900,000 到 999,999。这意味着CRS 3.x中的每个规则文件都为其保留了1000个ID。根据设计规则，将始终与其前面的 ID 分隔 10 个 ID。前 10 个 ID (000 010,020,030,040,050,060,070,080,090) 指定用于与控制流相关的规则，即通常会触发跳过操作的规则（这些规则将位于文件或通过的规则的开头）。由于这种结构，大多数规则文件将以 ID 9[File_ID]100 开头。随着新规则的制定，它们应该添加到最后。如果有必要在现有规则之间添加规则，则必须在注释中注明。

### 5.2. 规则说明

- REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example
  - 该文件用于为网站添加本地例外。在这个文件中，我们经常可以看到一些规则，它们可以让检查走捷径，允许跳过某些检查
- REQUEST-905-COMMON-EXCEPTIONS
  - 有些规则很容易在成熟软件中造成误报，如 Apache 回调或 Google Analytics 跟踪 cookie。该文件提供的规则可使事务避免触发这些误报
- REQUEST-910-IP-REPUTATION
  - 检测来自 IP 的流量，这些 IP 之前曾在本地网站或全球范围内参与过恶意活动
- REQUEST-912-DOS-PROTECTION
  - 该文件中的规则将尝试检测针对服务器的某些 7 层 DoS（拒绝服务）攻击
-  REQUEST-913-SCANNER-DETECTION
   -  检测安全工具和扫描器
-  REQUEST-920-PROTOCOL-ENFORCEMENT
   -  检测违反 HTTP 协议的请求或现代浏览器不会生成的请求，例如缺少用户代理的请求
-  REQUEST-921-PROTOCOL-ATTACK
   -  检测针对 HTTP 协议本身的特定攻击，如 HTTP 请求走私和响应分割
-  REQUEST-930-APPLICATION-ATTACK-LFI
   -  检测用户在网络服务器中访问他们不应该访问的本地文件
-  REQUEST-931-APPLICATION-ATTACK-RFI
   -  检测用户试图将远程资源包含到网络应用程序中并执行
-  REQUEST-932-APPLICATION-ATTACK-RCE
   -  检测远程命令执行攻击
-  REQUEST-933-APPLICATION-ATTACK-PHP
   -  检测PHP语言相关攻击
-  REQUEST-941-APPLICATION-ATTACK-XSS
   -  检测XSS攻击
-  REQUEST-942-APPLICATION-ATTACK-SQLI
   -  检测SQL注入攻击
-  REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION
   -  这些规则主要针对会话固定攻击提供保护
-  REQUEST-944-APPLICATION-ATTACK-JAVA
   -  检测JAVA语言相关攻击，如反序列化、log4j等
-  REQUEST-949-BLOCKING-EVALUATION
   -  这些规则为给定请求提供基于异常评分的拦截。如果处于异常检测模式，则不得删除此文件。
-  RESPONSE-954-DATA-LEAKAGES-IIS
   -  检测Microsoft IIS数据泄露
-  RESPONSE-952-DATA-LEAKAGES-JAVA
   -  检测Java应用的数据泄露
-  RESPONSE-953-DATA-LEAKAGES-PHP
   -  检测PHP应用的数据泄露
-  RESPONSE-950-DATA-LEAKAGES
   -  检测常规的数据泄露
-  RESPONSE-951-DATA-LEAKAGES-SQL
   -  检测后端 SQL 服务器可能发生的数据泄漏。这通常表明存在 SQL 注入问题。
-  RESPONSE-959-BLOCKING-EVALUATION
   -  这些规则为给定响应提供基于异常评分的拦截。如果处于异常检测模式，则不得删除此文件。
-  RESPONSE-980-CORRELATION
   -  该配置文件中的规则有助于收集有关服务器成功和失败攻击的数据。

### 5.3. 规则编写

#### 5.3.1. 基本语法
SecRule 主要由 4 部分组成：
- **Variables** - 变量。需要检查的目标
- **Operators** - 运算符，对变量做匹配
- **Transformations** - 转换方法，规范化变量数据
- **Actions** - 操作，规则匹配时执行的动作

结构如下：
```
SecRule VARIABLES "OPERATOR" "TRANSFORMATIONS,ACTIONS"
```

示例：
```
SecRule REQUEST_URI "@streq /index.php" "id:1,phase:1,t:lowercase,deny"
```
上述规则将获取每个 HTTP 请求并仅检查 URI 部分。首先将 URI 值转换为小写。随后将检查转换后的值是否完全等于'/index.php'。如果是，WAF将拒绝该请求，即停止后续检查并拦截该请求。

SecRule 指令的独特之处之一是配置中列出的每个 SecRule 都会在每个事务上进行评估。

Variables 变量有6个类别，一共105个：
- 请求变量- ARGS、REQUEST_HEADERS、REQUEST_COOKIES
- 响应变量- RESPONSE_HEADERS、RESPONSE_BODY
- 服务器变量- REMOTE_ADDR、AUTH_TYPE
- 时间变量- TIME、TIME_EPOCH、TIME_HOUR
- 集合变量- TX、IP、SESSION、GEO
- 其他变量- HIGHEST_SEVERITY、MATCHED_VAR

Operators 运算符有 4 个类别，约 36 个：
- 字符串运算符- rx、pm、beginsWith、contains、endsWith、streq、within
- 数值运算符- eq、ge、gt、le、lt
- 验证运算符- validateByteRange、validateUrlEncoding、validateSchema
- 其他运算符- rbl、geoLookup、inspectFile、verifyCC

Transformations 转换方法有 6 个类别，约 35 个：
- 反规避函数- lowercase、normalisePath、removeNulls、replaceComments、compressWhitespace
- 解码函数- base64Decode、hexDecode、jsDecode、urlDecodeUni
- 编码函数- base64Encode、hexEncode
- 哈希函数- sha1、md5

Actions 操作有 47 个：
- 破坏性行为- block、drop、deny、proxy
- 流程操作- chain、skip、skipAfter
- 元数据操作- phase、id、msg、severity、tag
- 变量操作- capture、setvar、initcol
- 日志记录操作- log、auditlog、nolog、sanitiseArg
- 其他操作- ctl、multiMatch、exec、pause、append/prepend