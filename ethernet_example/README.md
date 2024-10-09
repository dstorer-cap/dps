- Build in the old docker environment ~/old_docker on lithium 

Building sddf echo_server example
- Build in new docker environment
- cd examples/echo_server/
- make BUILD_DIR=build MICROKIT_SDK=/host/sel4devkit-maaxboard-microkit/out/microkit-sdk-1.4.1/ MICROKIT_BOARD=maaxboard
- mkdir network/ipstacks/lwip/src/core/ipv4/ to make folder not made in makefile

Instructions for running
1. Observe and note the IP address displayed by the line
```
DHCP request finished, IP address for netif <DEV> is: <IP-ADDR>
```
2. On the working machine, connect to the MaaXBoard using:
```bash
nc <IP-ADDR> 1236 # 1236 is the port used in this example
``` 
On successful connect, the console should read `100 IPBENCH v1.0`.
