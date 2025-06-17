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

## Step 3: Configuration







