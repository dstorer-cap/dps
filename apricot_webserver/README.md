# Apricot webserver

- Build in sddf/examples/echo_server

## Building 

- Clone picolibc  in sddf/examples/echo_server

```
git clone git@github.com:sel4devkit/sel4devkit-maaxboard-microkit-picolibc.git
```

- Build picolibc

- make MICROKIT_SDK=/home/dstorer/sel4devkit-maaxboard-microkit/out/microkit-sdk-1.4.1 MICROKIT_BOARD=maaxboard

## Running

Instructions for running 
- Boot example on maaxboard
- Observer IP address on terminal in this format:
```
LWIP|NOTICE: DHCP request for client0 returned IP address: 10.0.0.102
```

- Connect to web server using curl:
```
curl --http0.9 --max-time 10 "http://10.0.0.102:1236" -o output.html
curl --http0.9 --max-time 10 "http://192.168.0.129:1236" -o output.html
```