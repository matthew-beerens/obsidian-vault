

#### Delivery Method

Malicious macros are well known - email providers and spam filters often filter out all MS Office documents. 

We can just send malicious documents as emails and anti-phishing training stresses the danger of enabling macros in an emailed office document.

`we need a pretext` and to provide the document `another way` like a download link.

If we successfully deliver the document via email of download link - this file will be tagged with `Mark of the Web (MOTW)`. Documents marked with this will be opened in a `protected view`. Which disables editing and modification settings in the document and blocks execution of macros or embedded objects.

Hitting `Enable Editing` the protected view is disabled - So our goal is to convince the target to click `Enable Editing`. We can achieve this by possibly blurring the rest of the document and instructing them to click the button to unlock it.

There are other MS office program that lack this feature than can be utilized like Microsoft publisher - but it less often installed.

More recently we will also need to overcome the new announcement security risk which blocks macros from running - which involves a more tedious process to allow macros to run. This means that after this change, we must convince the user to unblock the file via the checkbox before our malicious macro can be executed

`MOTW is not added to FAT32 formatted devices`

`We can avoid MOTW being attached to files by putting them in a file container like .7zip folder`

