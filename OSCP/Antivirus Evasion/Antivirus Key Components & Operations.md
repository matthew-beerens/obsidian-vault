
#### Known vs Unknown Threats

original antivirus software made use of signatures based on files hashes, binary sequence matches and network activity.

Modern AV solutions use Machine learning engines to find unknown threats by checking unknown files on the system - these engines are based in the cloud thus needing an internet connection on the host to work. No good for internal enterprise networks.

AV limitations are over come with the use of Endpoint Detection and Response (EDR) - this software generates security event telemetry which is forwarded to a Security Information and Event Management (SIEM) system. This system provides a full overview of any past or on going attack.

AV and EDRs are not exclusive and complement each other.

#### Engines and Components

Modern AV is fueled  by signature updates from the vendors signatures database.

A modern antivirus is typically designed around the following components:
- File Engine - scheduled/Realtime file scans
- Memory Engine - inspects each process memory space at runtime
- Network Engine - inspects ingoing and outgoing network traffic
- Disassembler - disassembles malware packers/ciphers and loads into sandbox or emulator - translates machine code to assembly reconstructing the original program
- Emulator/Sandbox
- Browser Plugin - browsers are sandboxed so AVs use browser plugins to get better visibility and detect malicious content that could be executed inside the browser
- Machine Learning Engine - enhances detection of unknown threats using cloud enhanced computing resources and algorithms

These components work simultaneously with the signature database - events are ranked (benign, malicious, unknown).

#### Detection Methods

`signature based detection` -  restricted list technology, filesystem is scanned for know signatures and if found quarantined (can be hash,  set of patterns)

`Heuristic based detections` - relies on various rules and algorithms to determine malicious actions. often done by stepping through steps of binary file or disassemble and decompile to analyze source code. searching for programs calls and patterns.

`Behaviour based detection` - dynamically analyzes behavior of binary file by executing in an emulated environment.

`Machine-Learning detection` - uses ML algorithms to detect unknown threats.