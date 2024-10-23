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

Sockets
- Use bitty web server to parse the requests coming in. Use lwip to set up the sockets and call into these functions in the bitty web server. 