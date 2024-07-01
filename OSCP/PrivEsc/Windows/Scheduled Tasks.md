
Automated/Scheduled tasks are executed with the windows `Task Scheduler`. This could be clean up tasks or update management.

Tasks are defined with one or more `triggers`. These cause actions to be executed once met. These triggers could include: `a time, at startup, on log on or a windows event`.

An action specifies which program or script to execute. There are also other configurations for a tasks in the `Conditions, settings and general` menu tabs of a tasks property.

The most important information we need to know about a tasks:
- which user account does this task get executed as? (will it lead to PrivEsc?)
- What triggers are specified? (when? maybe it has already triggered and not useful)
- What actions are executed? (clues to how we can utilize this as an exploit)

#### Methodology

`schtasks /query /fo LIST /v` - list the scheduled tasks on the system:
- TaskName
- Next Run TIme
- Author
- Task To Run

`icacls <PATH_TO_BINARY` - check permissions on an interesting program run by an interesting task to see if we can exploit it in some way

`Use of of the methodologies from the services section of this windows folder` - you may be able to replace the binary, hijack in some way or something similar.