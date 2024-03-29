> The Setup

Host | adapter | IP/mask
:--: | :--: | :--:
  R1 | `enp0s3` | `172.10.0.2/30`
  R2 | `enp0s3` | `172.10.0.6/30`
  R3 | `enp0s3` | `172.10.0.10/30`
  R4 | `enp0s3`<br>`enp0s8`<br>`enp0s9` | `172.10.0.1/30` <br> `172.10.0.5/30`<br>`172.10.0.9/30`






Task 1: EVPN / Vxlan
```
Create L2 connectivity between host 1, host 2, host 3
Test case: 
Host 1 IP: 10.3.1.1/24
Host 2 IP: 10.3.1.2/24
Host 3 IP: 10.3.1.3/24

All the hosts should be able to ping each other
(Hint / Tip: Hosts will not form BGP with R1, R2, and R3)
 ``` 

Task 2: L3 + GRE Tunnel

```
Create L3 connectivity between all 3 Hosts, Each host will form BGP with its corresponding router and advertise its own loopback ip
Host 1: 10.10.1.1/32
Host 2: 10.10.1.2/32
Host 3: 10.10.1.3/32

Verify the ping between each hosts
- Setup a GRE tunnel between Host 1 and Host 3
- Set 192.168.100.1/24 on Host 1 tunnel interface
- Set 192.168.100.2/24 on Host 3 Tunnel interface
- Test the ping between 192.168.100.1 and 100.2
```


Task 3: VRF

```
All 3 Hosts will form BGP with its corresponding Routers
Host 1 and Host 2 will belong to VRF1
Host 3 will belong to VRF 2

Test case: ( Hosts within the same vrf will be able to ping each other )
Host 1 will be able to ping the loopback IP of Host 2 and vice versa
Host 3 will not be reachable from host 1 and host 2
Move host 2 to VRF 2, and repeat the tests
```






