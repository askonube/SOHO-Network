# Small Office Home Office (SOHO) Network Design and Implementation


### Salty Dreams is a sardine packing company that operates their headquarters in Vancouver, Canada. They are planning to expand in Calgary, where a new network will need to be designed and operated separately from the network in Vancouver.

#### Checklist:
- Base network 192.168.1.0 assigned by the Internet Service Provider (ISP)
- 1 router and 1 switch
- All devices must communicate with each other
- Separate VLANs and WAPs for 3 departments (IT, HR/Finance, Production/Distribution)
- Automatic IPV4 address assignment for host machines



## Step 1: Network Topology

`Router0` is the main router of the network and is connected to `Switch0`. `Switch0` is connected to all of the devices in the three departments. Each department is configured to have their own virtual local area networks (VLANs).

**IT Department**
- PC0
- Access Point0
- Printer0
- VLAN 10

**HR/Finance Department**
- PC1
- Printer1
- Access Point1
- VLAN 20

**Production/Distribution Department**
- PC2
- Printer2
- Access Point2
- VLAN 30

![image](https://github.com/user-attachments/assets/09025ee5-a367-4638-8f57-2ae751b3b8be)


## Step 2: Subnetting

- The Internet Service Provider (ISP) assigned the IP address of `192.168.1.0` to this network, which will be considered as the base network.
- Because there are three different departments using three different VLANs, there will be three different subnets. To find the number of network bits that must be borrowed, the following formula is used:

`2^n = # of subnets`

Here, `n` is the number of bits borrowed from the host portion to create subnets. Since we need three subnets, the equation looks like this:

`2^n = 3`

The result of `2^n` must be greater than or equal to the number of subnets. 

If `n` is `1`, then `2^1 = 2` >> Incorrect, as there are 3 total subnets.

If `n` is `2`, then `2^2 = 4` >> Correct, as 4 is greater than or equal to the number of declared subnets, which is 3. 

Therefore, two network bits must be borrowed to help create the subnets.

As previously stated, the base network IP address is `192.168.1.0`, which is a Class C address. The subnet mask for a Class C address is `255.255.255.0`, which is equivalent to `11111111.11111111.11111111.00000000` in binary. 

In CIDR notation this is written as `192.168.1.0/24`. 

The purpose of the subnet mask is to divide an IP address into two parts: the network portion and the host portion. This division allows devices to determine whether another device is on the same local network or on a different network, which guides how data packets are routed. This improves network organisation and management as multiple smaller subnetworks (subnets) can be created within a larger network.

Because two network bits must be borrowed, the new subnet mask in binary form will be `11111111.11111111.11111111.11000000`, where the two bits are borrowed from the host portion. Converting it back to decimal form will yield `255.255.255.192`, which will be the new subnet mask or `/26` in CIDR notation. 

This new subnet mask defines the increment between network addresses in each subnet, also known as the `block size`. This formula will be used to calculate the block size.

`Block Size` = `256 - Value of the first octet in the subnet mask from left to right that is not 255`

`Block Size` = `256 - 192`

`Block Size` = `64`

Each of the three subnets will contain a block of 64 addresses. The addresses will be configured incrementally from one subnet to the next.

<ins>**IT Department**

*Subnet 1*
- Network ID: 192.168.1.0
- Host range: 192.168.1.1 - 192.168.1.62
- Broadcast ID: 192.168.1.63

<ins>**HR/Finance Department**

*Subnet 2*
- Network ID: 192.168.1.64
- Host range: 192.168.1.65 - 192.168.1.126
- Broadcast ID: 192.168.1.127

<ins>**Production/Distribution Department**

*Subnet 3*
- Network ID: 192.168.1.128
- Host range: 192.168.1.129 - 192.168.1.190
- Broadcast ID: 192.168.1.191

## Step 3: VLAN Configuration

`Switch0` will be used to configure the different VLANs for the different departments. The switch will be enabled to configure with the commands `en` and `conf t`. 

The first interface that needs to be configured is `Fa0/1` which is facing the main router `Router0`. This interface will need to be configured to have a trunk interface in order to allow traffic from multiple VLANs to pass through.

![image](https://github.com/user-attachments/assets/8221935c-cb0a-4ac8-8123-662e0a9ef804)


The interfaces that will need to be configured on `Switch0` will be `Fa0/2`, `Fa0/3`, and `Fa0/4`. These interfaces are connected to `VLAN 10` or the `IT Department`. 
![image](https://github.com/user-attachments/assets/94b1bdde-2b68-4b21-8a88-6fb51e465ff2)

The specified ports on `Switch0` need to be give access mode with this command `switchport mode access`. 

To access a specific VLAN, the following command will be `switchport access vlan 10`. Because this VLAN didn't exist yet, this command will create `VLAN 10`. 

The following will be done to interfaces `Fa0/5`, `Fa0/6`, `Fa0/7` for `VLAN 20`, and `Fa0/8`, `Fa0/9`, `Fa0/10` will connect to `VLAN 30`. 

![image](https://github.com/user-attachments/assets/04393fee-1501-4a5f-9b66-dfc7f1f57580)

![image](https://github.com/user-attachments/assets/85644e69-d86b-4b91-9926-3ed53d729398)

The Wireless Access Points (WAPs) will also need to be configured to allow wireless connectivity for the devices in each respective VLAN. The WiFi password and the Service Set Identifier (SSID) will be created on each of the access points. 

![image](https://github.com/user-attachments/assets/47f4bd1b-78b7-43e2-a9a3-3b606d4467d9)

![image](https://github.com/user-attachments/assets/b0b5ef4f-1c1b-46fb-9a53-3403574b272f)

![image](https://github.com/user-attachments/assets/c4e13800-3e40-4201-b153-912a4e43a5d8)

Now the main router `Router0` must be configured. Its interface `Gig0/0` must be activated with the command `no sh` or `no shutdown` as it is in a "shutdown" state and not operational by default. When using the `no shutdown` command, it changes the interface status from `administratively down` to `up`, which allows the interface to be operational and able to transmit data. 

![image](https://github.com/user-attachments/assets/1ca60a74-7ffa-419b-850d-955a3302cd28)


To enable inter-VLAN routing on `Router0`, we will use the Router-on-a-Stick method. This technique allows the router to route traffic between multiple VLANs using a single physical interface. The switch port connected to the router is configured as a trunk, carrying traffic for all VLANs. On `Router0`, the physical interface `Gig0/0` is divided into multiple logical sub-interfaces, one for each VLAN. Each sub-interface is assigned an IP address that acts as the default gateway for its respective VLAN. This setup enables communication between VLANs without requiring a separate physical interface for each VLAN. 

To create a sub-interface, the command `int gig0/0.10` will be run to correspond to `VLAN 10`. It is then required to configure `Router0` with VLAN encapsulation using the command `encapsulation dot1Q 10` to associate it with `VLAN 10`. This sub-interface will be assigned as the default gateway for `VLAN 10`. Referencing the Host Range of IP addresses for the IT Department or Subnet 1 is `192.168.1.1 - 192.168.1.63`, the IP address `192.168.1.1` and its subnet mask `255.255.255.192` will be the default gateway for `VLAN 10`. The following command will be used `ip address 192.168.1.1 255.255.255.192`

![image](https://github.com/user-attachments/assets/a8f66b07-5280-4bab-a2fd-297bf36c609c)

The same will be done for `VLAN 20` and `VLAN 30`.

![image](https://github.com/user-attachments/assets/90a821c1-5b20-471d-b0d9-ba0970525caf)


The following image shows the configuration of the three sub-interfaces from the physical interface `Gig0/0` on `Router0`. Each sub-interface corresponds to the respective VLAN and their designated number.

![image](https://github.com/user-attachments/assets/659a3f17-1d53-45e9-aa93-0456c0f85c1a)

The Dynamic Host Configuration Protocol (DHCP) server must be configured now on `Router0` to dynamically assign IPV4 addresses to all the devices that connect to the network. The command `service dhcp` is used to enable the DHCP server. The DHCP server must be configured to create three different pools that correspond to each VLAN's subnet. This is used to separate groups of IP addresses ranges on a DHCP server (usually a router or a Layer 3 switch) that correspond to the VLAN's unique IP subnet. Each DHCP pool is responsible for assigning IP addresses and network configuration parameters such as default gateway and DNS server. 

For `VLAN 10`, the DHCP server must create a pool for the IT department. The following command `ip dhcp pool IT-Pool` is used to create the DHCP pool. That pool represents that specific subnet. Now a network address is assigned to the `IT-Pool` using the command `network 192.168.1.0 255.255.255.192`. Afterwards, a default gateway address is also assigned to `IT-Pool` using the IP address of the sub-interface that was assigned to the physical interface `Gig0/0.10`, which would be `default-router 192.168.1.1`. The Domain Name System (DNS) server is also assigned the same IP address using the command `dns-server 192.168.1.1`. The domain name of that server will be assigned as `domain-name itdept.com`. Now all of the devices in this VLAN will be assigned automatically. 

![image](https://github.com/user-attachments/assets/cdf94834-813c-4aad-a0e9-b65f7df52c3a)

The same will be done for the other two VLANs. 

![image](https://github.com/user-attachments/assets/bb0ce811-c183-4721-a66a-8d9592847722)

To verify that the devices will dynamically receive an IP address from the DHCP server, the IP configuration can be viewed from the device itself. `PC0` was successfully assigned the IP address `192.168.1.2` with a subnet mask of `255.255.255.192`. The default gateway and DNS server are both configured to be `192.168.1.1`. `Printer0` was successfully assigned the IP address `192.168.1.3` with the subnet mask of `255.255.255.192`. 

![image](https://github.com/user-attachments/assets/9b8b1165-da4d-4e43-b2ed-95f39cee6a76)

![image](https://github.com/user-attachments/assets/36d9fe7b-4de1-4866-9529-8a1ec0fa92fa)


`PC1` was successfully assigned the IP address `192.168.1.66` with a subnet mask of `255.255.255.192`. The default gateway and DNS server are both configured to be `192.168.1.65`. `Printer1` was successfully assigned the IP address `192.168.1.67` with the subnet mask of `255.255.255.192`. 

![image](https://github.com/user-attachments/assets/963db6b9-bf40-4c2a-80fd-77535391778e)

![image](https://github.com/user-attachments/assets/81e44b48-e303-4dcf-b2d5-444ea8599f6c)


`PC2` was successfully assigned the IP address `192.168.1.130` with a subnet mask of `255.255.255.192`. The default gateway and DNS server are both configured to be `192.168.1.129`. `Printer2` was successfully assigned the IP address `192.168.1.131` with the subnet mask of `255.255.255.192`. 

![image](https://github.com/user-attachments/assets/49db8e46-3d45-4640-aedc-b5c79cf9de61)

![image](https://github.com/user-attachments/assets/a3bb77e1-4150-4239-ad28-3dd7dcd0cc59)

To verify that each department has wireless access for their users, smartphones and laptops will be included in this diagram. If `Smartphone0` wanted to connect to the IT department's wireless access point, it would use the configured password and SSID.

![image](https://github.com/user-attachments/assets/fc5d5b2a-2039-4e59-b3df-3a2dfd97210b)


![image](https://github.com/user-attachments/assets/c7a9bfc2-1804-4e2d-bb13-b20eb10daebf)

![image](https://github.com/user-attachments/assets/a43ed2e3-984b-4415-9e1f-f9d0fd4c73ee)

Here it is seen as connected to `Access Point0`. 

![image](https://github.com/user-attachments/assets/889db905-e327-4174-a4a9-89cfc8b1ac71)


`Smartphone0` was successfully assigned the IP address `192.168.1.5` with a subnet mask of `255.255.255.192`. The default gateway and DNS server are both configured to be `192.168.1.1`.

![image](https://github.com/user-attachments/assets/1a3ef081-c796-415f-93aa-0f86d2e5f3e5)

In regards to checking connectivity with the laptop, a wireless module must first be inserted. The laptop must be turned off first, then the existing module and replace it with the `WPC300N`. Finally turn the laptop back on. 

![image](https://github.com/user-attachments/assets/c811f34d-0b1a-4079-a574-a6b3593a6d60)

If `Laptop1` wanted to connect to the HR/Finance department's wireless access point, it would use the configured password and SSID.

![image](https://github.com/user-attachments/assets/767b06ea-01fd-4e75-b4ac-b13d2d90d497)

![image](https://github.com/user-attachments/assets/849b1c05-101e-4158-88d5-3568e63f81c9)

![image](https://github.com/user-attachments/assets/651234d1-8ea4-4a3b-a219-df43a9c7b7a6)

![image](https://github.com/user-attachments/assets/c17c485a-1f01-477a-9feb-0f98f0e70214)

`Laptop1` was successfully assigned the IP address `192.168.1.69` with a subnet mask of `255.255.255.192`. The default gateway and DNS server are both configured to be `192.168.1.65`.

![image](https://github.com/user-attachments/assets/ab5b44f1-4b2c-4f65-8ce0-d64a2617ae32)

![image](https://github.com/user-attachments/assets/3ca387ac-2326-479a-ac88-cf4e1a198db2)

![image](https://github.com/user-attachments/assets/f516efa3-5740-4251-a9aa-a52cd892f60d)


