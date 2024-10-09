- Build in the old docker environment ~/old_docker on lithium 

Building sddf echo_server example
- Build in new docker environment
- cd examples/echo_server/
- make BUILD_DIR=build MICROKIT_SDK=/host/sel4devkit-maaxboard-microkit/out/microkit-sdk-1.4.1/ MICROKIT_BOARD=maaxboard
- mkdir network/ipstacks/lwip/src/core/ipv4/ to make folder not made in makefile