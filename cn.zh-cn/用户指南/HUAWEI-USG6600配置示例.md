# HUAWEI USG6600配置示例<a name="zh-cn_topic_0053755649"></a>

本章节以Huawei USG6600系列V100R001C30SPC300版本的防火墙的配置过程为例进行说明，供参考。

假设数据中心的子网为192.168.3.0/24和192.168.4.0/24，VPC下的子网为192.168.1.0/24和192.168.2.0/24 ，VPC上IPSec隧道的出口公网IP为93.188.242.110（从VPC上IPSec VPN的本端网关参数上获取）。

## 操作步骤<a name="section33573705151334"></a>

1.  登录防火墙设备的命令行配置界面。
2.  查看防火墙版本信息。

    ```
    display version 
    17:20:502017/03/09
    Huawei Versatile Security Platform Software
    Software Version: USG6600 V100R001C30SPC300(VRP (R) Software, Version 5.30)
    ```

3.  创建ACL并绑定到对应的vpn-instance。

    ```
    acl number 3065 vpn-instance vpn64
    rule 1 permit ip source 192.168.3.0 0.0.0.255 destination 192.168.1.0 0.0.0.255
    rule 2 permit ip source 192.168.3.0 0.0.0.255 destination 192.168.2.0 0.0.0.255
    rule 3 permit ip source 192.168.4.0 0.0.0.255 destination 192.168.1.0 0.0.0.255
    rule 4 permit ip source 192.168.4.0 0.0.0.255 destination 192.168.2.0 0.0.0.255
    q 
    ```

4.  创建ike proposal。

    ```
    ike proposal 64 
    dh group5 
    authentication-algorithm sha1 
    integrity-algorithm hmac-sha2-256 
    sa duration 3600 
    q
    ```

5.  创建ike peer，并引用之前创建的ike proposal，其中对端IP地址是93.188.242.110。

    ```
    ike peer vpnikepeer_64
    pre-shared-key ******** （********为您输入的预共享密码）
    ike-proposal 64
    undo version 2
    remote-address vpn-instance vpn64 93.188.242.110
    sa binding vpn-instance vpn64
    q
    ```

6.  创建IPSec协议。

    ```
    ipsec proposal ipsecpro64
    encapsulation-mode tunnel
    esp authentication-algorithm sha1
    q
    ```

7.  创建IPSec策略，并引用ike policy和ipsec proposal。

    ```
    ipsec policy vpnipsec64 1 isakmp
    security acl 3065
    pfs dh-group5
    ike-peer vpnikepeer_64
    proposal ipsecpro64
    local-address xx.xx.xx.xx
    q
    ```

8.  将IPSec策略应用到相应的子接口上去。

    ```
    interface GigabitEthernet0/0/2.64
    ipsec policy vpnipsec64
    q
    ```

9.  测试连通性。

    在上述配置完成后，我们可以利用您在云中的主机和您数据中心的主机进行连通性测试，如下图所示：

    ![](figures/printscreen2.png)


