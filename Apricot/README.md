# Apricot

## Building a product in Apricot
- make all ARCHITECTURE_MODE=aarch64 DEPLOYMENT_MODE=release AUTHENTICATION_MODE=off

## Building a product using the bash file
- ./task.bash all aarch64_none develop off


Notes
- The thresholds should be stored function area 
- Divsufsort in a seperate folder with its own readme file
- Alphabet size is 256

## Download none-elf compiler 

- wget https://developer.arm.com/-/media/Files/downloads/gnu/11.2-2022.02/binrel/gcc-arm-11.2-2022.02-x86_64-aarch64-none-elf.tar.xz
-

## Regression tests
- Create a copy of svn/apricot/development/stable/product
- In hsm there is a python file which generates the c header files.
- Create a similar call in function/apricot_healthtests/src/ for the ent health tests as the NIST health tests
- Implement the thresholds in hsm/python_file (extend the original so the python file generates the thresholds for all the health tests)

# Bill meeting
- Setting up global data structure in read_entropy_region call and using this data structure in the calls to each of the tests
- The regression tests do not seem to like this 
- Should I set up a global data structure or set up a structure for each individual test?

# Outcome 
- Remove the data structure and hard code the alphabet size to 256
- Look into what happens when the binary data is used 

To do

- Svn copy the stable branch and put the long term health tests inside the new branch
- Change divsufsort to call cmake in the makefile rather than a shell script
- Add long term health test data to regression test and test against expected output 

## Running regression tests
- Navigate to product/regression/apricot_lt_healthtests
```
./task.bash all amd64_linux develop n n n n off
``

