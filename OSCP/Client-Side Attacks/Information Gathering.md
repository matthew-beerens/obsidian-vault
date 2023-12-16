
We need  to gather information about the target - including personal information - such as name, software in use and operating system from a non-routable network.
#### Enumerating Target Software

One approach  is to inspect `metadata tags` of publicly available documents associated with the target organization. These tags are organized by `tag groups`.

These tags can include information about:` author, creation date, name and version of software used, operating system and more`. In some cases this information is stored directly in the metadata.

If we use old documents for this enumeration our information may be out-data and inaccurate. Creation and modification data can give us information of the quality of the metadata.

#### Metadata analysis

`site:example.com filetype:pdf` - To find these documents pertaining to an organization(branch) we can utilize google dorking and then analyze the metadata of the document. If we want to narrow down the search to a specific branch or location we can add key words.

`ffuf -w /usr/share/seclists/Discovery/Web-Content/big.txt:FUZZ -u http://ip/FUZZ.pdf` - we can utilize directory bruteforcing to find files on a webserver - this is noisy

`exiftool -a -u  file.pdf` - Once we have a file  we can display its metadata utilizing this tool. `-a` displays duplicate tags and `-u` displays unknown tags.