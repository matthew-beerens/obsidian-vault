Special permissions of binary that allows user to run binary as file owner (could be root)

If binary is misconfigured this can be used to elevate shell to higher privileges otherwise it is limited to execution of that binary

Needs to be executable

Methodology
=
1. `file binaryname` learn about the binary - could have imported or missing binary that could be utilized 
2. `strings` - static analysis might reveal information about other binaries
	1. might be able to edit the binary, delete the binary and replace our own binary
	2. turn binary into a escalated shell
	3. `cp /bin/bash binary_name`