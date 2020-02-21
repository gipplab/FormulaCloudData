
# Mathematical Objetcs of Interest

This repository contains the results of the distributional analysis of [Mathematical Objects of Interest (MOI)](https://arxiv.org/pdf/2002.02712.pdf)
for the datasets [arXMLiv 08/2018](https://sigmathling.kwarc.info/resources/arxmliv-dataset-082018/) and 
[zbMATH](https://zbmath.org/). 

## Please cite as:
A. Greiner-Petter, M. Schubotz, F. Müller, C. Breitinger, H. S. Cohl, A. Aizawa, and B. Gipp,
*"Discovering Mathematical Objects of Interest - A Study of Mathematical Notations."* 
**In:** Proceedings of The Web Conference 2020 (WWW’20), April 20–24, 2020, Taipei, Taiwan.
**DOI:** [10.1145/3366423.3380218](https://doi.org/10.1145/3366423.3380218)

The [preprint](https://arxiv.org/pdf/2002.02712.pdf) of the paper can be found on arXiv [https://arxiv.org/abs/2002.02712](https://arxiv.org/abs/2002.02712).

```tex
@InProceedings{GreinerPetter2020,
  author    = {Andr{\'{e}} Greiner{-}Petter and
               Moritz Schubotz and
               Fabian M\"{u}ller and
               Corinna Breitinger and
               Howard S.~Cohl and
               Akiko Aizawa and
               Bela Gipp},
  title     = {Discovering Mathematical Objects of Interest - A Study of Mathematical Notations},
  booktitle = {Proceedings of The Web Conference 2020 (WWW '20), April 20--24, 2020, Taipei, Taiwan},
  doi       = {10.1145/3366423.3380218}
}
```

## Download the Data

For downloading the data either use `wget` or `curl` or go to the [releases of this GitHub repository](https://github.com/ag-gipp/FormulaCloudData/releases) and download it manually.

##### arXMLiv-Large
Unzipped data requires 61GB of free disk space. 
You can download single parts if you do not need the entire dataset.
If you are not interested in high-complex expressions, one can use the small version of the dataset.
```shell script
...
```

##### arXMLiv-Small
Unzipped data requires 6.2GB of free disk space. 
This dataset contains only expressions that appear at least twice in one document. 
Hence, many complex expressions do not appear in this data. 
``` sh
user@pc:~/zbmath$ wget https://github.com/ag-gipp/FormulaCloudData/releases/download/2.0-arxiv/arxmliv-distributions.zip
user@pc:~/zbmath$ unzip arxmliv-distributions.zip
```

##### zbMATH
Unzipped data requires 1.1GB free disk space.
```shell script
user@pc:~/zbmath$ wget https://github.com/ag-gipp/FormulaCloudData/releases/download/1.0-zb/zbmath-distributions.zip
user@pc:~/zbmath$ unzip zbmath-distributions.zip
```

## Explore the Data

Each dataset contains multiple numbered files without file extensions. You simply can peek into one of the files to explore the general structure. Each entry contains the string representation (SR) of the unique expression in the dataset, the complexity value (C), the total term frequency (TF) in the dataset, and the document frequency (DF) of the expression. All files are CSV files (separated by colons). For example, if you look at the first line of the file `1` in zbMATH you would see the following
```shell script
user@pc:~/zbmath$ head -1 1
"mfrac(mi:d,mrow(mn:1,mo:+,mi:d))";3;1;1
```

If you want so search for specific expressions, say the mass-energy equivalence, we recommend to use `grep`. Here is an example to search for the entry in zbMATH:
```shell script
user@pc:~/zbmath$ grep '"mrow(mi:E,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))"' *
12:"mrow(mi:E,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;63;49
```
As we can see, `E=mc^2` is in file `12`, has a complexity of 4, a total term frequency of 63, and a document frequency of 49.

With `grep` you can also use simple regular expressions to search for patterns. Let's check if the dataset contains expressions that substitutes `E` on the left-hand side by something else.
``` sh
user@pc:~/zbmath$ grep '"mrow(.*,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))"' *
1:"mrow(msub(mi:E,mn:0),mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;1;1
11:"mrow(msup(mi:β,mrow(mo:-,mn:1)),mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;1;1
12:"mrow(mi:E,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;63;49
2:"mrow(mi:ℰ,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;1;1
9:"mrow(mi:e,mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;3;3
9:"mrow(mrow(mi:h,mo:ivt,mi:ν),mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))";4;1;1
```
We can see there are actually 6 distinguished left-hand sides in zbMATH:
1) `E_0 = mc^2`
2) `\beta^{-1} = mc^2`
3) `E = mc^2`
4) `\varepsilon = mc^2`
5) `e = mc^2`
6) `hv = mc^2`

If you have `parallel` installed, you can speed up the process. For example:
```shell script
find . -type f | parallel 'grep "mrow(mi:E,mo:=,mrow(mi:m.*;[[:digit:]]*;..*;[3456789]$" {}' 2>/dev/null | awk '{print $1}'

find . -type f | xargs -n 1 -P 32 gawk 'match($0, /^"mrow\(mi:E,mo:=,mrow\(mi:m.*;[[:digit:]]+;[[:digit:]]+;[[:digit:]][[:digit:]]+$/, arr) {print $1}'
```

### Convert String to MathML
The repository also contains a converter (including source code) to convert the string representation back to the MathML representation.
Simply use
```shell script
user@pc/~: java -jar converter.jar "mrow(msub(mi:E,mn:0),mo:=,mrow(mi:m,mo:ivt,msup(mi:c,mn:2)))"
<mrow><msub><mi>E</mi><mn>0</mn></msub><mo>=</mo><mrow><mi>m</mi><mo>⁢</mo><msup><mi>c</mi><mn>2</mn></msup></mrow></mrow>
```
