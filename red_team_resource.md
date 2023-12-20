

## 概述

### ref

https://github.com/topics/penetration-testing

https://github.com/search?q=adversary%20emulation&type=repositories

[What is Adversary Emulation?](https://www.picussecurity.com/resource/glossary/what-is-adversary-emulation)

### 对手仿真（adversary emulation）

对手仿真是一种网络安全评估方法。通过在受控环境中模拟现实世界攻击者的战术、技术和程序（TTPs），来测试防御措施的有效性。

对手仿真可以基于MITRE ATT&CK框架，促进攻击和防御网络安全团队之间的协作，增进策略和战术的沟通和理解。

对于缺少专业红队的团队，可以使用开源的对手仿真工具评估防御措施的有效性，比如：
MITRE Caldera: 一种开源的自动对手仿真系统，使用MITRE ATT&CK框架来建模威胁并复制它们的行为。
Atomic Red Team: 一个脚本库，旨在模拟对手的行为并验证检测能力。它默认不提供自动化，但非常灵活且被广泛使用。
Infection Monkey: 一种开源的入侵和攻击模拟工具（BAS），重点是突破目标并通过从一个主机到另一个主机横向移动来感染整个网络。
Stratus Red Team: 一个专为云环境而设计的对手仿真工具，模拟来自MITRE ATT&CK for Cloud Matrix的对手技术。
