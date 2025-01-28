# Apricot Window

## Notes
- product/function/apricot_window
- sliding window of the previous 100 blocks

- block_tally number of blocks that have been passed through the hsm's
- Add the number of the block that is problematic to the block_warns array

- make_window_data is initialising the structs (all data to 0)
- add_block, add 1 to block_tally and if it's recieved a warning message will ad the block to the block_warns array
- get_window_check, int* window check is the output of the window check. Function looks through window_size_blocks and sees how many blocks in the last window_size_blocks have failed.

- Assume all blocks before the "system started" have passed
- If region_t.total_size (size of the memory region) is not big enough to contain 

## To do
- Svn copy stable to new branch inside daniel
- Implement the window functions 