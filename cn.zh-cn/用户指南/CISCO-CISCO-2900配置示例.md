# CISCO CISCO-2900配置示例<a name="zh-cn_topic_0053755650"></a>

本章节以CISCO 2900系列15.0版本的防火墙配置过程为例进行说明，供参考。

假设数据中心的子网为192.168.3.0/24和192.168.4.0/24，VPC下的子网为192.168.1.0/24和192.168.2.0/24 ，VPC上IPSec隧道的出口公网IP为93.188.242.110（从VPC上IPSec VPN的本端网关参数上获取）。

## 操作步骤<a name="section2968051315169"></a>

1.  登录防火墙设备的命令行配置界面。
2.  配置isakmp策略。

    ```
    crypto isakmp policy 1 
    authentication pre-share 
    encryption aes 256 
    hash sha 
    group 5 
    lifetime 3600
    ```

3.  配置预共享密钥。

    ```
    crypto isakmp key ******** address 93.188.242.110（********为您输入的预共享密钥） 
    ```

4.  配置IPSec安全协议。

    ```
    crypto ipsec transform-set ipsecpro64 esp-aes 256 esp-sha-hmac
    mode tunnel
    ```

5.  配置ACL（访问控制列表），定义需要保护的数据流。

    ```
    access-list 100 permit ip 192.168.3.0 0.0.0.255 192.168.1.0 0.0.0.255
    access-list 100 permit ip 192.168.3.0 0.0.0.255 192.168.2.0 0.0.0.255
    access-list 100 permit ip 192.168.4.0 0.0.0.255 192.168.1.0 0.0.0.255
    access-list 100 permit ip 192.168.4.0 0.0.0.255 192.168.2.0 0.0.0.255
    ```

6.  配置IPSec策略。

    ```
    crypto map vpnipsec64 10 ipsec-isakmp
    set peer 93.188.242.110
    set transform-set ipsecpro64
    set pfs group5
    match address 100
    ```

7.  在接口上应用IPSec策略。

    ```
    interface g0/0
    crypto map vpnipsec64
    ```

8.  测试连通性。

    在上述配置完成后，我们可以利用您在云中的主机和您数据中心的主机进行连通性测试，如下图所示：

    ![](figures/printscreen1.png)


