Building web server example
```
cd lionsos/examples/webserver
export MICROKIT_SDK=/host/lionsos/examples/webserver/microkit-sdk-1.4.1/
export MICROKIT_BOARD=maaxboard
export NFS_SERVER=172.16.1.21
export NFS_DIRECTORY=/mnt/webserver
export WEBSITE_DIR=/mnt/webserver_data/website
```

Notes
- Application builds and runs however no ip address supplied


Bitty web_server 
- Currently built with sddf echo_server example (~/sddf/examples/echo_server/board_maaxboard)

Commands
nc 10.0.0.102 1236
sudo /usr/sbin/uhubctl -l "1-12" -p 1 -a cycle

Sockets
- Use bitty web server to parse the requests coming in. Use lwip to set up the sockets and call into these functions in the bitty web server. 
- Rewrite WebServer.c to parse the data and return the output to the lwip protection domain
- Notify the web server protection domain when there is a message to be parsed 
- Find out where in the lwip protection domain reads the message coming in 
- The function where message are read in echo server example is in utlization_socket.c and called utilization_recv_callback
- The function where the message is parsed in bitty web server is in function WS_RunServer in WebServer.c
- WS_Tick is the control function of the web server 

Code analysis
- WS_TICK loops through the connections and starts a webserver for each connection 

To do 
- Work out whether to write message into shared data or have both web server and lwip in the same protection domain

Instructions for running 
- Boot example on maaxboard
- Observer IP address on terminal in this format:
```
LWIP|NOTICE: DHCP request for client0 returned IP address: 10.0.0.102
```

- Connect to web server using curl:
```
curl --http0.9 --max-time 10 "http://10.0.0.102:1236" -o output.html
```

Code Explanation 
- The interface between the lwip/socket functions and bitty webserver is the run_webserver function in utilization_socket.c 