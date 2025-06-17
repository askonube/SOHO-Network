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



