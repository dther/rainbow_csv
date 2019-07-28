![rainbow logo](https://i.imgur.com/cJwEvyM.png)

## Installation
Use your favorite package manager.  

Vundle: `Plugin 'mechatroner/rainbow_csv'`  
VimPlug: `Plug 'mechatroner/rainbow_csv'`  
dein: `call dein#add('mechatroner/rainbow_csv')`  

No additional steps required - Rainbow CSV will work out of the box.  

## Overview
Main features:  
* Highlight CSV columns in different rainbow colors. 
* Provide info about column under the cursor
* Provide _SELECT_ and _UPDATE_ queries in RBQL: SQL-like transprogramming query language.
* Consistency check for csv files (CSVLint)
* Align and Shrink CSV fields (add/remove trailing spaces in fields)

There are 3 ways to enable csv columns highlighting:
1. CSV autodetection based on file content and/or extension  
2. Manual CSV delimiter selection with _:RainbowDelim_ command with cursor over the delimiter  
3. Manual CSV delimiter selection with _:RainbowMultiDelim_ for multi-character delimiters  
4. Execute `:set ft=csv` or `:set ft=tsv` for CSV/TSV dialects. For other separators use _:RainbowDelim_  

To run an RBQL query either press _F5_ or enter the query in vim command line e.g. _:Select a1, a2_  
As soon as you finish entering "select" (or update) and press whitespace, the plugin will show column names in the status line.  

The core functionality of the plugin is written in pure vimscript, no additional libraries required.  

### Demonstration of rainbow_csv highlighting and RBQL queries 


![demo_screencast](https://i.imgur.com/Tnk9KZv.gif)


The demo table is _demo/movies.tsv_. There are also some other test datasets in _python/test\_datasets_.
In this demo python expressions were used, but JavaScript is also available.


# Plugin description

### Associating file extensions with CSV dialects
In most cases the built-in autodetection algorithm should correctly detect correct CSV dialect for all CSV tables that you open in Vim, but if you have disabled the autodetection algorithm or don't want to rely on it for some reason, you can manually associate file extensions with available csv dialects.  
Example: to associate ".dat" extension with "csv_pipe" dialect and ".csv" extension with "csv_semicolon" add the folowing lines to your .vimrc:  
```
autocmd BufNewFile,BufRead *.csv   set filetype=csv_semicolon
autocmd BufNewFile,BufRead *.dat   set filetype=csv_pipe
```
The following named csv dialects are available for extension association: "csv", "tsv", "csv_semicolon", "csv_pipe", "csv_whitespace"


### Rainbow highlighting for non-table files
You can use rainbow highlighting and RBQL even for non-csv/tsv files.  
E.g. you can highlight records in log files, one-line xmls and other delimited records.  
You can even highlight function arguments in your programming language using comma or comma+whitespaces as a delimiter for _:RainbowDelim_ or _:RainbowMultiDelim_ commands.  
And you can always turn off the rainbow highlighting using _:NoRainbowDelim_ command.  

Here is an example of how to extract some fields from a bunch of uniform single-line xmls:  

![demo_xml_screencast](https://i.imgur.com/HlzBWOV.gif)


### Key Mappings
Key mappings are only assigned for files with active rainbow highlighting

|Key                       | Action                                             |
|--------------------------|----------------------------------------------------|
|**F5**                    | Start query editing for the current csv file       |
|**F5**                    | Execute currently edited query                     |


### Commands

#### :RainbowDelim

Mark current file as a table and highlight it's columns in rainbow colors. Character under the cursor will be used as a delimiter. The delimiter will be saved in a config file for future vim sessions.

You can also use this command for non-csv files, e.g. to highlight function arguments  
in source code in different colors. To return back to original syntax highlighting run _:NoRainbowDelim_

#### :RainbowMultiDelim

Same as _:RainbowDelim_, but works with multicharacter separators.
Visually select the multicharacter separator (e.g. `~#~`) and run _:RainbowMultiDelim_ command.

#### :NoRainbowDelim

Disable rainbow columns highlighting for the current file.

#### :CSVLint

The linter checks the following:  
* consistency of double quotes usage in CSV rows  
* consistency of number of fields per CSV row  

#### :RainbowAlign

Align CSV columns with whitespaces.  
Don't run this command if you treat leading and trailing whitespaces in fields as part of the data.  

#### :RainbowShrink

Remove leading and trailing whitespaces from all fields. Opposite to RainbowAlign

#### :Select ...

Allows to enter RBQL select query as vim command.
e.g. _:Select a1, a2 order by a1_

#### :Update ...

Allows to enter RBQL update query as vim command.
e.g. _:Update a1 = a1 + " " + a2_

#### :RainbowName \<name\>

Assign any name to the current table. You can use this name in join operation instead of the table path. E.g.
```
JOIN customers ON a1 == b1
``` 
intead of:
```
JOIN /path/to/my/customers/table ON a1 == b1
```

### Configuration

#### g:disable_rainbow_hover
Set to `1` to stop showing info about the column under the cursor in Vim command line  
Example:  
```
let g:disable_rainbow_hover = 1
```

#### g:disable_rainbow_csv_autodetect
Set to `1` to disable CSV autodetection mechanism  
Example:  
```
let g:disable_rainbow_csv_autodetect = 1
```
Manual delimiter selection would still be possible. 
You can also manually associate specific file extensions with 'csv' or 'tsv' filetypes  

#### g:rcsv_max_columns
Default: _30_

Autodetection will fail if buffer has more than _g:rcsv\_max\_columns_ columns.  
You can increase or decrease this limit.

#### g:rcsv_colorpairs
List of color name pairs to customize rainbow highlighting.  
Each entry in the list is a pair of two colors: the first color is for terminal mode, the second one is for GUI mode.  
Example:
```
let g:rcsv_colorpairs = [['red', 'red'], ['blue', 'blue'], ['green', 'green'], ['magenta', 'magenta'], ['NONE', 'NONE'], ['darkred', 'darkred'], ['darkblue', 'darkblue'], ['darkgreen', 'darkgreen'], ['darkmagenta', 'darkmagenta'], ['darkcyan', 'darkcyan']]
```

#### g:rbql_backend_language
Default: _python_

Scripting language to use in RBQL expression. Either 'js' or 'python'  
To use JavaScript add _let g:rbql_backend_language = 'js'_ to .vimrc  

#### g:rbql_output_format
Default: _input_  
Allowed values: _tsv_, _csv_, _input_

Format of RBQL result set tables.

* input: same format as the input table
* tsv: doesn't allow quoted tabs inside fields. 
* csv: is Excel-compatible and allows quoted commas.

Essentially format is a pair: delimiter + quoting policy.  
This setting for example can be used to convert files between tsv and csv format:
* To convert _csv_ to _tsv_: **1.** open csv file. **2.** `:let g:rbql_output_format='tsv'` **3.** `:Select *`
* To convert _tsv_ to _csv_: **1.** open tsv file. **2.** `:let g:rbql_output_format='csv'` **3.** `:Select *`


### Optional "Header" file feature

Rainbow csv allows you to create a special "header" file for any of your spreadsheet table files. It must have the same name as the table file but with ".header" suffix (e.g. for "table.tsv" table the header file is "table.tsv.header"). The only purpose of header file is to provide csv column names.

### Installation

Install with your favorite plugin manager.  
If you want to use RBQL with JavaScript expressions, make sure you have Node.js installed


# RBQL (RainBow Query Language) Description

RBQL is a technology which provides SQL-like language that supports _SELECT_ and _UPDATE_ queries with Python or JavaScript expressions.  

[Official Site](https://rbql.org/)

### Main Features

* Use Python or JavaScript expressions inside _SELECT_, _UPDATE_, _WHERE_ and _ORDER BY_ statements
* Result set of any query immediately becomes a first-class table on it's own
* Supports input tables with inconsistent number of fields per record
* Output records appear in the same order as in input unless _ORDER BY_ is provided
* Each record has a unique NR (line number) identifier
* Supports all main SQL keywords
* Provides some new useful query modes which traditional SQL engines do not have
* Supports both _TOP_ and _LIMIT_ keywords
* Supports user-defined functions (UDF)
* Works out of the box, no external dependencies

#### Limitations:

* RBQL doesn't support nested queries, but they can be emulated with 2 or more consecutive queries.

### Supported SQL Keywords (Keywords are case insensitive)

* SELECT
* UPDATE
* WHERE
* ORDER BY ... [ DESC | ASC ]
* [ LEFT | INNER ] JOIN
* DISTINCT
* GROUP BY
* TOP _N_
* LIMIT _N_

All keywords have the same meaning as in SQL queries. You can check them [online](https://www.w3schools.com/sql/default.asp)  


### Special variables

| Variable Name            | Variable Type | Variable Description                 |
|--------------------------|---------------|--------------------------------------|
| _a1_, _a2_,..., _a{N}_   |string         | Value of i-th column                 |
| _b1_, _b2_,..., _b{N}_   |string         | Value of i-th column in join table B |
| _NR_                     |integer        | Line number (1-based)                |
| _NF_                     |integer        | Number of fields in line             |


### UPDATE statement

_UPDATE_ query produces a new table where original values are replaced according to the UPDATE expression, so it can also be considered a special type of SELECT query. This prevents accidental data loss from poorly written queries.  
_UPDATE SET_ is synonym to _UPDATE_, because in RBQL there is no need to specify the source table.  


### Aggregate functions and queries

RBQL supports the following aggregate functions, which can also be used with _GROUP BY_ keyword:  
_COUNT()_, _MIN()_, _MAX()_, _SUM()_, _AVG()_, _VARIANCE()_, _MEDIAN()_, _FOLD()  

Additionally RBQL supports _DISTINCT COUNT_ keyword which is like _DISTINCT_, but adds a new column to the "distinct" result set: number of occurrences of the entry, similar to _uniq -c_ unix command.  
`SELECT DISTINCT COUNT a1` is equivalent to `SELECT a1, COUNT(a1) GROUP BY a1`  

#### Limitations

* Aggregate function are CASE SENSITIVE and must be CAPITALIZED.
* Aggregate functions inside Python (or JS) expressions are not supported. Although you can use expressions inside aggregate functions.
  E.g. `MAX(float(a1) / 1000)` - valid; `MAX(a1) / 1000` - invalid


### JOIN statements

Join table B can be referenced either by it's file path or by it's name - an arbitary string which user should provide before executing the JOIN query.  
RBQL supports _STRICT LEFT JOIN_ which is like _LEFT JOIN_, but generates an error if any key in left table "A" doesn't have exactly one matching key in the right table "B".  

#### Limitations

* _JOIN_ statements must have the following form: _<JOIN\_KEYWORD> (/path/to/table.tsv | table_name ) ON ai == bj_  


### SELECT EXCEPT statement

SELECT EXCEPT can be used to select everything except specific columns. E.g. to select everything but columns 2 and 4, run: `SELECT * EXCEPT a2, a4`  
Traditional SQL engines do not support this query mode.


### FOLD() and UNFOLD()

#### FOLD() 
FOLD is an aggregate function which accumulates all values into a list.  
By default it would return the list joined by pipe `|` character, but you can provide a callback function to change this behavior.  
FOLD is very similar to GROUP_CONCAT function in MySQL  
Example (Python): `select a2, FOLD(a1, lambda v: ';'.join(sorted(v))) group by a2`  
Example (JavaScript):  `select a2, FOLD(a1, v => v.sort().join(';')) group by a2`  

#### UNFOLD() 
UNFOLD() is a function-like query mode which will do the opposite to FOLD().  
UNFOLD() accepts a list as an argument and will repeat the output record multiple times - one time for each value from the list argument.  
Example: `SELECT a1, UNFOLD(a2.split(';'))`  
Traditional SQL engines can't operate with lists (arrays) and do not support FOLD() and UNFOLD()  


### User Defined Functions (UDF)

RBQL supports User Defined Functions  
You can define custom functions and/or import libraries in two special files:  
* `~/.rbql_init_source.py` - for Python
* `~/.rbql_init_source.js` - for JavaScript


## Examples of RBQL queries

#### With Python expressions

* `select top 100 a1, int(a2) * 10, len(a4) where a1 == "Buy" order by int(a2)`
* `select * order by random.random()` - random sort, this is an equivalent of bash command _sort -R_
* `select top 20 len(a1) / 10, a2 where a2 in ["car", "plane", "boat"]` - use Python's "in" to emulate SQL's "in"
* `select len(a1) / 10, a2 where a2 in ["car", "plane", "boat"] limit 20`
* `update set a3 = 'US' where a3.find('of America') != -1`
* `select * where NR <= 10` - this is an equivalent of bash command "head -n 10", NR is 1-based')
* `select a1, a4` - this is an equivalent of bash command "cut -f 1,4"
* `select * order by int(a2) desc` - this is an equivalent of bash command "sort -k2,2 -r -n"
* `select NR, *` - enumerate lines, NR is 1-based
* `select * where re.match(".*ab.*", a1) is not None` - select entries where first column has "ab" pattern
* `select a1, b1, b2 inner join ./countries.txt on a2 == b1 order by a1, a3` - an example of join query
* `select distinct count len(a1) where a2 != 'US'`
* `select MAX(a1), MIN(a1) where a2 != 'US' group by a2, a3`

#### With JavaScript expressions

* `select top 100 a1, a2 * 10, a4.length where a1 == "Buy" order by parseInt(a2)`
* `select * order by Math.random()` - random sort, this is an equivalent of bash command _sort -R_
* `select top 20 a1.length / 10, a2 where ["car", "plane", "boat"].indexOf(a2) > -1`
* `select a1.length / 10, a2 where ["car", "plane", "boat"].indexOf(a2) > -1 limit 20`
* `update set a3 = 'US' where a3.indexOf('of America') != -1`
* `select * where NR <= 10` - this is an equivalent of bash command "head -n 10", NR is 1-based')
* `select a1, a4` - this is an equivalent of bash command "cut -f 1,4"
* `select * order by parseInt(a2) desc` - this is an equivalent of bash command "sort -k2,2 -r -n"
* `select NR, *` - enumerate lines, NR is 1-based
* `select a1, b1, b2 inner join ./countries.txt on a2 == b1 order by a1, a3` - an example of join query
* `select distinct count a1.length where a2 != 'US'`
* `select MAX(a1), MIN(a1) where a2 != 'US' group by a2, a3`


### FAQ

#### How does RBQL work?

RBQL parses SQL-like user query, creates a new python or javascript worker module, then imports and executes it.

Explanation of simplified Python version of RBQL algorithm by example.
1. User enters the following query, which is stored as a string _Q_:
```
    SELECT a3, int(a4) + 100, len(a2) WHERE a1 != 'SELL'
```
2. RBQL replaces all `a{i}` substrings in the query string _Q_ with `a[{i - 1}]` substrings. The result is the following string:
```
    Q = "SELECT a[2], int(a[3]) + 100, len(a[1]) WHERE a[0] != 'SELL'"
```

3. RBQL searches for "SELECT" and "WHERE" keywords in the query string _Q_, throws the keywords away, and puts everything after these keywords into two variables _S_ - select part and _W_ - where part, so we will get:
```
    S = "a[2], int(a[3]) + 100, len(a[1])"
    W = "a[0] != 'SELL'"
```

4. RBQL has static template script which looks like this:
```
    for line in sys.stdin:
        a = line.rstrip('\n').split('\t')
        if %%%W_Expression%%%:
            out_fields = [%%%S_Expression%%%]
            print '\t'.join([str(v) for v in out_fields])
```

5. RBQL replaces `%%%W_Expression%%%` with _W_ and `%%%S_Expression%%%` with _S_ so we get the following script:
```
    for line in sys.stdin:
        a = line.rstrip('\n').split('\t')
        if a[0] != 'SELL':
            out_fields = [a[2], int(a[3]) + 100, len(a[1])]
            print '\t'.join([str(v) for v in out_fields])
```

6. RBQL runs the patched script against user's data file: 
```
    ./tmp_script.py < data.tsv > result.tsv
```
Result set of the original query (`SELECT a3, int(a4) + 100, len(a2) WHERE a1 != 'SELL'`) is in the "result.tsv" file.
It is clear that this simplified version can only work with tab-separated files.


#### Is this technology reliable?

It should be: RBQL scripts have only 1000 - 2000 lines combined (depending on how you count them) and there are no external dependencies.
There is no complex logic, even query parsing functions are very simple. If something goes wrong RBQL will show an error instead of producing incorrect output, also there are currently 5 different warning types.



### Standalone CLI Apps

You can also use two standalone RBQL apps with JavaScript and Python backends:

#### rbql-js
Installation: `$ npm install -g rbql`  
Usage: `$ rbql-js --query "select a1, a2 order by a1" < input.tsv`  

#### rbql-py
Installation: `$ pip install rbql`  
Usage: `$ rbql-py --query "select a1, a2 order by a1" < input.tsv`  



# Generic info

## Comparison of Rainbow CSV technology with traditional graphical column alignment

#### Advantages

* WYSIWYG  
* Familiar editing environment of your favorite text editor  
* Zero-cost abstraction: Syntax highlighting is essentially free, while graphical column alignment can be computationally expensive  
* High information density: Rainbow CSV shows more data per screen because it doesn't insert column-aligning whitespaces.
* Works with non-table and semi-tabular files (text files that contain both table(s) and non-table data like text)
* Ability to visually associate two same-colored columns from two different windows. This is not possible with graphical column alignment  

#### Disadvantages

* Rainbow CSV technology may be less effective for CSV files with many (> 10) columns
* Current Rainbow CSV implementations do not support newlines inside double-quoted csv fields. Adding multiline fields support is technically possible under certain conditions but would impair other Rainbow CSV features and advantages.

## References


#### Rainbow CSV in other editors:

* Rainbow CSV extension in [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)
* rainbow-csv package in [Atom](https://atom.io/packages/rainbow-csv)
* rainbow_csv plugin in [Sublime Text](https://packagecontrol.io/packages/rainbow_csv)
* rainbow_csv plugin in [gedit](https://github.com/mechatroner/gtk_gedit_rainbow_csv) - doesn't support quoted commas in csv
* rainbow_csv_4_nedit in [NEdit](https://github.com/DmitTrix/rainbow_csv_4_nedit)
* CSV highlighting in [Nano](https://github.com/scopatz/nanorc)


#### RBQL

* rbql-js CLI App for Node.js - [npm](https://www.npmjs.com/package/rbql)  
* rbql-py CLI App in [python](https://pypi.org/project/rbql/)  


#### RBQL alternatives:

* [csvkit](https://csvkit.readthedocs.io/en/1.0.2/)
* [sqawk](https://github.com/dbohdan/sqawk)
* [q](https://github.com/harelba/q)


#### Related vim plugins:
Rainbow CSV name and original implementation was significantly influenced by [rainbow_parentheses](https://github.com/kien/rainbow_parentheses.vim) Vim plugin.  

There also exists an old vim syntax file [csv_color](https://vim.sourceforge.io/scripts/script.php?script_id=518) which, despite it's name, can highlight only *.tsv files.  
And, of course, there is [csv.vim](https://github.com/chrisbra/csv.vim)
