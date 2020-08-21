## Overview of Lempel-Ziv-Welch Algorithm

<button type="button">TEST BUTTON IGNORE</button>
<iframe src="https://www.google.com/webhp?igu=1"></iframe>

LZW uses patterns in documents to reduce the size of the document, 
replacing patterns with references. Rather than encoding by character, 
it replaces groups of characters of size 1+ with a code. 


### Features
* Universal coding 
* Dynamically created dictionary
* Lossless

The algorithm does not require the probability distribution of symbols 
which makes the algorithm usable for any input and saves time by only 
taking one pass to build its dictionary. This means there is no space 
overhead when transmitting required to compress/decompress. 

## Algorithm 
In both compression and decompression, a dictionary is dynamically built 
which describes the output. 
  
In the dictionary each pattern has a code assigned to it. In the case of 
ASCII the single characters are assigned their ASCII values which range 
from 0-255. Every additional pattern uses a code above that range, starting 
from 256. 

### Compression
Compression uses two variables:
  - P : Accumulation of symbols read that already exist in the dictionary. 
        initiated by an empty symbol 
  - C : Currently read symbol 

The document is read symbol by symbol. At each symbol, concatenate P and C 
to get the current pattern. If this pattern exists in the dictionary then 
no output is needed and P is set as the concatenation. If the pattern does 
not exist a new entry in the dictionary is needed and the appropriate code 
is assigned to it. The code of the previous pattern P is added as a symbol 
in the encoded document. A new pattern is now being found as P is assigned 
C. At the end of the document whatever is left in P will be within the 
dictionary and its code is appended to the output. 

```
p = empty
dict = empty 
compression = empty 
FOREACH c IN document
  concat = p.c  
  IF concat IN dict 
    p = concat 
  ELSE 
    dict[concat] = addNewCode()
    compression = compression.dict[p] 
    p = c
compression = compression.dict[p]

```

#### Example 
This is applied for the word IMUSINGVIMINMUSING 

| Symbol | ASCII | 
| --- | --- | 
| I | 73 | 
| M | 77 | 
| U | 85 |  
| S | 83 | 
| N | 78 | 
| G | 71 |
| V | 86 | 

| P | C | Output | Code | Symbols | 
| --- | --- | --- | --- | --- | 
| EMPTY | I | | 73 | I |
| I | M | 73 | 256 | IM | 
| M | U | 77 | 257 | MU | 
| U | S | 85 | 258 | US | 
| S | I | 83 | 259 | SI | 
| I | N | 73 | 260 | IN | 
| N | G | 78 | 261 | NG | 
| G | V | 71 | 262 | GV | 
| V | I | 86 | 263 | VI | 
| I | M |    |     |    | 
| IM | I | 256 | 264 | IMI | 
| I | N |    |     |    | 
| IN | M | 260 | 265 | INM | 
| M | U |    |     |    | 
| MU | S | 257 | 266 | MUS | 
| S | I |    |     |    | 
| SI | N | 259 | 267 | SIN | 
| N | G | 261 |     |    | 

As such this produces the code: <br> 
<73><77><85><83><73><78><71><86><256><260><257><259><261> 

This is made up of only 13 symbols compared to the original 18. 

## Decompression 
This process is very similar to compression. By traversing through the 
encoded string and building the dictionary the symbols are correctly 
defined before they are needed.  

For ASCII strings we know the first 255 values and they are included in  
the dictionary in the beginning. 

In this case P starts empty like before. For each symbol its dictionary 
value is taken as output as the dictionary maps the code to symbols. 
The concatenation of P and the dictionary entry taken is added to the 
dictionary with the new code generated in the same manner as the encoder. 
The value of P is then taken as the value given to the output. 

WIP MISSING A CASE 



### Example 

You can use the [editor on GitHub](https://github.com/Moop204/lzw-implementation/edit/master/docs/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

## References

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/Moop204/lzw-implementation/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
