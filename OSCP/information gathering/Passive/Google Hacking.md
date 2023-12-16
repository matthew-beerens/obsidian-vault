
exploit-db has many useful google dorks.

DorkSearch portal provides pre-build subset of queries and a builder tool for searching.

operators:
- site - limits searches to a single domain (used to gain a rough idea of a companies web presence)
- filetype - limits search results to the specified file type
- ext - helpful to discern which programming languages might be in use
- intitle - limit search results to titles containing string
- insite - search for a string contained in a website

adding a `-` to an operator, for example: `-filetype:html` will limit results to exclude html file types

### useful dorks

`intitle:"index of" "parent directory"` - finds pages that contain "index of" in the title and the words "parent directory" on the page. This output can reveal interesting files and sensitive information (this output refers to directory listing pages).

`insite:@<companyemail> -site:www.company.com` - search for employee emails not directly listed on the company site. useful for finding social media profiles.




