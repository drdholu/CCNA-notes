### Importance of the Subnet Mask

A subnet mask is important because it defines how many networks and hosts can exist within a given network. By converting an IP address and its subnet mask into binary, we can determine the **network bits** and **host bits**.

- **Network Bits**: These bits define the network portion of the IP address and generally remain constant.
- **Host Bits**: These bits identify individual hosts within the network and are used to assign addresses to devices.

> Number of hosts = 2 ^ (number of host bits) - 2

The subtraction of 2 accounts for the *network address and the broadcast address.*

> **Classless Inter-Domain Routing (CIDR)**: CIDR extends the number of available IP addresses and provides a compact representation of an IP address and its associated network mask.

### Subnetting Based on Number of Networks

If you need to create more networks, you can increase the number of network bits by *borrowing bits from the host* portion. This process is known as **subnetting**.

To determine how many **bits to borrow**:

1. Use powers of 2:
    - `128 <- 64 <- 32 <- 16 <- 8 <- 4 <- 2 <- 1`
2. For example, if you need 4 additional networks, you need to borrow 2 bits (from 2 -> 4 & also 2^2 = 4).
    

In **CIDR notation**, borrowing 2 bits changes the subnet mask from `/24` to `/26`:

- Original subnet mask: `11111111.11111111.11111111.00000000` -> `/24`
- After borrowing 2 bits: `11111111.11111111.11111111.11000000` -> `/26`

To determine the range of each subnet, **find the increment**, which is the *value of the last network bit used*. For a `/26` subnet mask, the increment is 64.

Example subnets:

1. `192.168.1.0 - 192.168.1.63`
2. `192.168.1.64 - 192.168.1.127`
3. `192.168.1.128 - 192.168.1.191`
4. `192.168.1.192 - 192.168.1.255`

>To summarise,
>1. Calculate the number of host bits required.
>2. Borrow the required number of host bits.
>3. Find the increment.
>4. Create your subnets using the increment.

### Subnetting Based on Number of Hosts

If you need to divide the network based on the number of hosts, you need to *keep the host bits* and save them accordingly.

To determine how many bits to save:

1. Use powers of 2 to find the number of bits needed:
    - `128 <- 64 <- 32 <- 16 <- 8 <- 4 <- 2 <- 1`
    - By doubling these values: `256 <- 128 <- 64 <- 32 <- 16 <- 8 <- 4 <- 2`
    - For 40 hosts, you need 6 bits (from 2 -> 64 & also 2^6 = 64).
2. In **CIDR notation**,
    - Original subnet mask: `11111111.11111111.11111111.00000000` -> `/24`
    - After saving bits: `11111111.11111111.11111111.11000000` -> `/26`

To determine the range of each subnet, use the increment, which is the value of the last network bit used. For a `/26` subnet mask, the increment is 64.

Example subnets:

1. `192.168.1.0 - 192.168.1.63`
2. `192.168.1.64 - 192.168.1.127`
3. `192.168.1.128 - 192.168.1.191`
4. `192.168.1.192 - 192.168.1.255`

So we would provide the network info to our network admin as such,
1. `192.168.1.0 /26`
2. `192.168.1.64 /26`
3. `192.168.1.128 /26`


>To summarize,
>1. Calculate the number of host bits required.
>2. Save the required number of host bits.
>3. Find the increment.
>4. Create your subnets using the increment.

## Reverse Subnetting

Using reverse subnetting we can find,
1. Network address
2. Broadcast Address
3. Network Range

Given,
- IPv4 addr: `10.200.56.120`
- Subnet Mask: `255.255.254.0`
- Default Gateway: `10.200.56.1`

First lets convert out subnet mask into binary,
`11111111.11111111.11111110.0`, Hence the *CIDR notation* will be `/23`.

The last network bit here will help us find our increment, which in this case is 2.

Now our network starting address will look something like this, `10.200.0.0`

Our overall network ranges will look something like this
1. `10.200.0.0 - 10.200.1.255`
2. `10.200.2.0 - 10.200.3.255`
3. `10.200.4.0 - 10.200.5.255`
and so on...

So Our given IPv4 address has these features,
1. Network Range: `10.200.56.0 - 10.200.57.255`
2. Network address: `10.200.56.0`
3. Broadcast Address: `10.200.57.255`


## Other way to reverse
### Given:
- **IPv4 Address**: `10.200.56.120`
- **Subnet Mask**: `255.255.254.0` (which corresponds to `/23` in CIDR notation)
- **Default Gateway**: `10.200.56.1`

### Step-by-Step Explanation:

1. **Subnet Mask in Binary**:
   - `255.255.254.0` in binary is `11111111.11111111.11111110.00000000`.
   - CIDR notation is `/23`, indicating that the first 23 bits are used for the network, and the remaining 9 bits are for hosts.

2. **Network Address Calculation**:
   - The network address is found by ANDing the IP address with the subnet mask.
   - `10.200.56.120` in binary: `00001010.11001000.00111000.01111000`
   - Subnet mask: `11111111.11111111.11111110.00000000`
   - Network Address (AND operation):
     ```
     00001010.11001000.00111000.01111000
     AND
     11111111.11111111.11111110.00000000
     -----------------------------------
     00001010.11001000.00111000.00000000
     ```
   - The network address is `10.200.56.0`.

3. **Broadcast Address Calculation**:
   - The broadcast address is found by setting all the host bits (the remaining bits after the network bits) to `1`.
   - Network address: `00001010.11001000.00111000.00000000`
   - Broadcast Address:
     ```
     00001010.11001000.00111001.11111111
     ```
   - The broadcast address is `10.200.57.255`.

4. **Network Range**:
   - The range of IP addresses in this subnet goes from the network address to the broadcast address.
   - **Network Range**: `10.200.56.0` to `10.200.57.255`

### Summary of Findings:
- **Network Address**: `10.200.56.0`
- **Broadcast Address**: `10.200.57.255`
- **Network Range**: `10.200.56.0` to `10.200.57.255`



## More questions

Here are two application-based questions on subnetting, along with their answers:

### Question 1
An organization has been assigned the IP address 192.168.1.0/24 and needs to create 4 subnets to accommodate at least 30 hosts in each subnet. What subnet mask should the organization use, and how many usable hosts will each subnet have?

**Answer:**
To create 4 subnets, we need to borrow bits from the host portion of the address. 

1. **Calculate the number of bits needed for subnets:**
   - $$2^n \geq 4$$ (where $$n$$ is the number of bits borrowed)
   - Therefore, $$n = 2$$ (since $$2^2 = 4$$)

2. **New subnet mask:**
   - Original mask is /24 (255.255.255.0).
   - Borrowing 2 bits gives us a new mask of /26 (255.255.255.192).

3. **Calculate usable hosts:**
   - Remaining bits for hosts = $$32 - 26 = 6$$
   - Usable hosts per subnet = $$2^6 - 2 = 62$$ (subtracting 2 for network and broadcast addresses).

Thus, the organization should use a subnet mask of **255.255.255.192** and will have **62 usable hosts** in each subnet.

### Question 2
A company has been allocated the IP address range of 10.0.0.0/8 and wants to create subnets for different departments: HR, Sales, and IT. If each department requires at least 50 hosts, what is the appropriate subnet mask for each department, and how many subnets can be created?

**Answer:**
1. **Calculate the number of bits needed for hosts:**
   - For at least 50 hosts, we need enough bits such that $$2^n \geq 50$$
   - The smallest $$n$$ that satisfies this is $$6$$ (since $$2^6 = 64$$)

2. **New subnet mask:**
   - Original mask is /8.
   - We need to borrow $$6$$ bits for hosts, leaving us with $$32 - (8 + 6) = 18$$ bits for subnets.
   - Therefore, the new subnet mask is /26 (255.255.255.192).

3. **Calculate the number of subnets:**
   - The number of borrowed bits for subnets is $$18$$
   - The number of possible subnets = $$2^{(18-8)} = 2^{10} = 1024$$

Thus, the appropriate subnet mask for each department is **255.255.255.192**, and the company can create **1024 subnets** from the allocated IP address range.

Citations:
[1] https://www.youtube.com/watch?v=n9twjWSnhgE
[2] https://byjusexamprep.com/gate-cse/subnetting-question
[3] https://www.flackbox.com/subnetting-practice-questions
[4] https://www.subnetting.net/Subnetting.aspx?mode=practice
[5] https://www.geeksforgeeks.org/introduction-to-subnetting/
[6] https://learn.microsoft.com/en-us/troubleshoot/windows-client/networking/tcpip-addressing-and-subnetting
[7] https://www.youtube.com/watch?v=5AXVLO4VW4U
[8] https://www-users.cse.umn.edu/~tripathi/Internet-Examples/HTTP-Key-Differences.pdf