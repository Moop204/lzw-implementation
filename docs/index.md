## Overview of Lempel-Ziv-Welch Algorithm

LZW uses patterns in documents to reduce the size of the document, 
replacing patterns with references. Rather than encoding by character, 
it replaces groups of characters of size 1+ with a code. Unlike other 
lossless compression methods it uses a dictionary to match characters to 
codes. This method 
works when encoding and decoding, allowing for a completely lossless 
compression algorithm.  

The algorithm does not require the probability distribution of symbols 
which makes the algorithm usable for any input and saves time by only 
taking one pass to build its dictionary. This means there is no space 
overhead when compressing/decompressing. 

The compression algorithm is best used with documents that contain 
more repetitive patterns. 

## Algorithm 

In both compression and decompression, a dictionary is dynamically built 
which describes the output. 
  
In the dictionary each pattern has a code assigned to it. In the case of 
ASCII the single characters are assigned their ASCII values which range 
from 0-255. Every additional pattern uses a code above that range, starting 
from 256. 

The algorithm uses two variables:
  - P : Accumulation of symbols read that already exist in the dictionary. 
        It is initiated by an empty symbol. 
  - C : Currently read symbol of the document. 

### Compression

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

##### ASCII Table for Relevant Symbols

| Symbol | ASCII | 
| --- | --- | 
| I | 73 | 
| M | 77 | 
| U | 85 |  
| S | 83 | 
| N | 78 | 
| G | 71 |
| V | 86 | 

##### Step by Step Breakdown

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
The concatenation of P and the first symbol of the dictionary value taken is added to the 
dictionary with the new code generated in the same manner as the encoder. 
The value of P is then taken as the value given to the output. 

A special case exists when a new entry is added to the dictionary and is 
immediately used after during compression. This leads to the entry not 
being dynamically built in time with the previous algorithm. In such 
cases the first character of the prefix P is added to the end of P for 
the output. This can be seen with the encoding of ABABABAB

```
result = empty
p = empty 
FOREACH c IN document 
  IF(EXIST(dict[c]))
    current = dict[c] 
  ELSE 
    current = p.p[0]
  result = result.current 
  new_entry = p.current
  dict[new_entry] = addNewCode() 
  p = dict[c]
```

### Example

This example uses the output taken from the previous example. 

<73><77><85><83><73><78><71><86><256><260><257><259><261> 

##### Step by Step Breakdown

| P | C | Output | Code | Symbols | 
| --- | --- | --- | --- | --- | 
| EMPTY | 73 | I | 73 | I | 
| I | 77 | M | 256 | IM | 
| M | 85 | U | 257 | MU | 
| U | 83 | S | 258 | US |
| S | 73 | I | 259 | SI | 
| I | 78 | N | 260 | IN |
| N | 71 | G | 261 | NG | 
| G | 86 | V | 262 | GV | 
| V | 256 | IM | 263 | VI | 
| IM | 260 | IN | 264 | IMI | 
| IN | 257 | MU | 265 | INM | 
| MU | 259 | SI | 266 | MUS | 
| SI | 261 | NG | 267 | SIN | 


## References

[Rosetta Code](https://rosettacode.org/wiki/LZW_compression) 
[Prepressure](https://www.prepressure.com/library/compression-algorithm/lzw)
[GeeksForGeeks](https://www.geeksforgeeks.org/lzw-lempel-ziv-welch-compression-technique/)

