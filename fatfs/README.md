# FatFs example

- Link the xhci driver with the fatfs code from the lionos codebase
- Use fatfs cleanup branch of lionos codebase
- Fatfs code in components/fs/fat
- Add lions os code base to api folder 
- Use lionsos example rather than try to mash the code with the original fatfs example
- Operation functions are defined in fatfs_op.c 

Update
- Don't use the asyn version of fatfs defined in lionsos, use the sync version.