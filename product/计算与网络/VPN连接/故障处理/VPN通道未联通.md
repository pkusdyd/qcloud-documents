## 现象描述
使用 VPN 连接建立 VPC 与 IDC 的通信，完成配置后，发现 VPN 通道状态为【未联通】。
![](https://main.qcloudimg.com/raw/d37bb0a17e5b7ab17db816872ff5dfa5.png)

## 可能原因
通道状态异常，一般有如下可能原因：
+ 无流量激活通道
+ VPN 网关公网 IP 不通
+ 安全策略配置不正确
+ 协商参数、协商模式不一致

## 处理方案
1. 登录 VPC 中的一台服务器，ping 对端 IDC 侧服务器的内网 IP 来激活通道。
    - 如果 ping 通，表示通道已激活，查看 VPN 通道状态是否已联通，如已联通，则问题解决，结束。
    - 如果 ping 不通，请直接执行 [步骤2](#step2)。
2. [](id:step2)请登录 IDC 侧的 VPN 设备，ping 腾讯云侧 VPN 网关的公网 IP（本例假设 VPN 网关公网 IP 为139.186.120.129），查看是否可以 ping 通。
    - 是 => 请执行 [步骤4](#step4)。
    - 否 => 请执行 [步骤3](#step3)。
		![](https://main.qcloudimg.com/raw/8202c85bf541bcd3df296b72ce177a3a.png)
3. [](id:step3)请检查 IDC 侧公网网络连接状态，是否可以正常连接到互联网。
    - 是 => 请执行 [步骤4](#step4)。
    - 否 => 请修复本地网络后，再查看 VPN 通道状态是否已联通，如已联通，则问题解决，结束；如未联通，则继续执行 [步骤4](#step4)。
4. [](id:step4)查看 IDC 侧 VPN 设备的安全策略，是否放通了腾讯云侧 VPN 网关的公网 IP 地址以及需要互通的内网地址。
```plaintext
display current-configuration configuration security-policy      //此处以华为防火墙为例
```
  - 是 => 请执行 [步骤5](#step5)。
  - 否 => 请修改安全策略，放通腾讯云侧 VPN 网关的 IP 以及对应 SPD 策略，再查看 VPN 通道状态是否已联通，如已联通，则问题解决，结束；如未联通，则继续执行[ 步骤5](#step5)。
5. [](id:step5)比对腾讯云侧 VPN 网关与对端 IDC 的 VPN 设备协商参数（IKE、IPsec 配置）及协商模式（主模式 main/野蛮模式 aggressive）是否一致。
   腾讯云侧配置参数可在 [VPN 通道控制台](https://console.cloud.tencent.com/vpc/vpnConn?rid=17) 相应通道详情的高级配置中查看：
   ![](https://main.qcloudimg.com/raw/003f674d04966acebd7b713a64743609.png)
	 IDC 侧设备配置参数可通过如下命令获取（此处以华为防火墙为例）：
	  ```plaintext
display current-configuration configuration ike profile
display current-configuration configuration ipsec  policy
  ```
   - 是 => 请执行 [步骤6](#step6)。
   - 否 => 请修改相应参数，确保两端配置一致，然后再查看 VPN 通道状态是否已联通，如已联通，则问题解决，结束；如未联通，则继续执行 [步骤6](#step6)。
6. [](id:step6)请收集以上检查信息，并[ 提交工单 ](https://console.cloud.tencent.com/workorder/category)或联系设备厂商跟进处理。

