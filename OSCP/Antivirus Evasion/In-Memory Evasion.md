
#### In-Memory Injection (PE injection)

popular technique used to bypass antivirus products. Focuses on manipulation of volatile memory. No files are written to disk - which is commonly focused by AV products.

#### Remote Process Memory Injection

attempts to inject payload into valid non-malicious PE. This is commonly done using set of Windows APIs.
1. use the `OpenProcess` function to obtain valid `HANDLE` to target process we have permission to access.
2. allocate memory in the process context by calling `VirtualAllocEx`
3. copy the malicious payload to the newly allocated memory using `WriteProcessMemory`
4. execute payload in memory in a separate thread using `CreateRemoteThread`

#### Process Hollowing

1. launch non-malicious process in a suspended state.
2. after launch, image of process is removed from memory and replaced with malicious executable image
3. process then resumed and malicious code executed

#### Inline hooking

modifying memory and introducing a hook (instruction that redirects the code execution) into a function to make it point to our malicious code.

after executing the flow will return back to the modified function and resume execution - appearing as if only the original code had executed. 

Hooking is often used by rootkits.

rootkits provide dedicated and persistent access to the target system through modification of system components in user space, kernel or lower OS protections rings.
