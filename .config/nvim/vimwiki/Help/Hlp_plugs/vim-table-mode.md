# Vim-Table-Mode

## Creating table on-the-fly

To start using the plugin in the on-the-fly mode use `:TableModeToggle` mapped to <kbd>\<Leader\>tm</kbd> by default (which means <kbd>\\</kbd> <kbd>t</kbd> <kbd>m</kbd> if you didn't override the <Leader> by `:let mapleader = ','` to have <kbd>,</kbd> <kbd>t</kbd> <kbd>m</kbd>).

> Tip :
> You can use the following to quickly enable / disable table mode in insert
> mode by using `||` or `__` :
> ```vim
> function! s:isAtStartOfLine(mapping)
>   let text_before_cursor = getline('.')[0 : col('.')-1]
>   let mapping_pattern = '\V' . escape(a:mapping, '\')
>   let comment_pattern = '\V' . escape(substitute(&l:commentstring, '%s.*$', '', ''), '\')
>   return (text_before_cursor =~? '^' . ('\v(' . comment_pattern . '\v)?') . '\s*\v' . mapping_pattern . '\v$')
> endfunction
> 
> inoreabbrev <expr> <bar><bar>
>           \ <SID>isAtStartOfLine('\|\|') ?
>           \ '<c-o>:TableModeEnable<cr><bar><space><bar><left><left>' : '<bar><bar>'
> inoreabbrev <expr> __
>           \ <SID>isAtStartOfLine('__') ?
>           \ '<c-o>:silent! TableModeDisable<cr>' : '__'
> ```

Enter the first line, delimiting columns by the `|` symbol. The plugin reacts by inserting spaces between the text and the separator if you omit them:

    | name | address | phone |

In the second line (without leaving Insert mode), enter `|` twice. The plugin will write a properly formatted horizontal line:

    | name | address | phone |
    |------+---------+-------|

When you enter the subsequent lines, the plugin will automatically adjust the formatting to match the text you’re entering every time you press `|`:

    | name       | address | phone |
    |------------+---------+-------|
    | John Adams |

Go on until the table is ready:

    | name            | address                  | phone      |
    |-----------------+--------------------------+------------|
    | John Adams      | 1600 Pennsylvania Avenue | 0123456789 |
    |-----------------+--------------------------+------------|
    | Sherlock Holmes | 221B Baker Street        | 0987654321 |
    |-----------------+--------------------------+------------|

Then you can return to the first line and above it enter `||`:

    |-----------------+--------------------------+------------|
    | name            | address                  | phone      |
    |-----------------+--------------------------+------------|
    | John Adams      | 1600 Pennsylvania Avenue | 0123456789 |
    |-----------------+--------------------------+------------|
    | Sherlock Holmes | 221B Baker Street        | 0987654321 |
    |-----------------+--------------------------+------------|

Corner separators are adjustable:

For Markdown-compatible tables use

    let g:table_mode_corner='|'


    |-----------------|--------------------------|------------|
    | name            | address                  | phone      |
    |-----------------|--------------------------|------------|
    | John Adams      | 1600 Pennsylvania Avenue | 0123456789 |
    |-----------------|--------------------------|------------|
    | Sherlock Holmes | 221B Baker Street        | 0987654321 |
    |-----------------|--------------------------|------------|

To get ReST-compatible tables use

    let g:table_mode_corner_corner='+'
    let g:table_mode_header_fillchar='='


    +-----------------+--------------------------+------------+
    | name            | address                  | phone      |
    +=================+==========================+============+
    | John Adams      | 1600 Pennsylvania Avenue | 0123456789 |
    +-----------------+--------------------------+------------+
    | Sherlock Holmes | 221B Baker Street        | 0987654321 |
    +-----------------+--------------------------+------------+

Markdown and ReST filetypes have automatically configured corners.

   You can also define in a table header border how it's content should be
   aligned, whether center, right or left by using a `:` character defined by
   `g:table_mode_align_char` option.

If you manipulate the table when table mode is disabled or copy paste a table
from clipboard from outside and it ends up being misaligned, you can realign
it using `:TableModeRealign` or using the default mapping
<kbd>\<Leader\>tr</kbd> defined by `g:table_mode_relign_map` option.

## Formatting existing content into a table

   Table Mode wouldn't justify it's name if it didn't allow formatting
   existing content into a table. And it does as promised. Like table creation
   on the fly as you type, formatting existing content into a table is equally
   simple. You can visually select multiple lines and call `:Tableize` on it,
   or alternatively use the mapping <kbd>\<Leader\>tt</kbd> defined by the
   `g:table_mode_tableize_map` option which would convert CSV (Comma Separated
   Value) data into a table and use `,` defined by `g:table_mode_delimiter`
   option as the delimiter.

   If however you wish to use a different delimiter, you can use the command
   `:Tableize/{pattern}` in a similar fashion as you use tabular (eg.
   `:Tableize/;` uses ';' as the delimiter) or use the mapping <kbd>\<Leader\>T</kbd>
   defined by `g:table_mode_tableize_op_map` option which takes input in the
   cmd-line and uses the `{pattern}` input as the delimiter.

   `:Tableize` also accepts a range and so you can also call it by giving
   lines manually like `:line1,line2Tableize`, but this is not very intuitive.
   You can use the mapping <kbd>\<Leader\>T</kbd> with a `[count]` to apply it to the
   next `[count]` lines in standard vim style.

## Moving around 

   Now you can move between cells using table mode motions <kbd>[|</kbd>,
   <kbd>]|</kbd>, <kbd>{|</kbd> & <kbd>}|</kbd> to move left | right | up |
   down cells respectively. The left | right motions wrap around the table
   and move to the next | previous row after the last | first cell in the
   current row if one exists. 

## Manipulating Table

  - **Cell Text Object** :

      Tableize provides a text object for manipulating table cells. Following
      the vim philosophy the you have <kbd>i|</kbd> & <kbd>a|</kbd> for the
      inner and around (including the immidiate right table separator) the
      table cell.

  - **Delete Row** :

      You can use the <kbd>\<Leader\>tdd</kbd> mapping defined by the option
      `g:table_mode_delete_row_map` to delete the current table row (provided
      you are within a table row), this can be preceeded with a [count] to
      delete multiple rows just like you would with 'dd'.

  - **Delete Column** :

      You can use the <kbd>\<Leader\>tdc</kbd> mapping defined by the option
      `g:table_mode_delete_column_map` to delete the entire current column
      (provided you are within a table row), this can also be preceeded with a
      [count] to delete multiple columns.

  - **Insert Column** :

      You can use the <kbd>\<Leader\>tic</kbd> mapping defined by the option
      `g:table_mode_insert_column_after_map` to insert a column after the
      cursor (provided you are within a table row). Of course you can use the
      <kbd>\<Leader\>tiC</kbd> mapping defined by
      `g:table_mode_insert_column_before_map` to insert a column before the
      cursor. Both can also be preceeded with a [count] to insert multiple
      columns.

## Advanced Usage: Spreadsheet Capabilities

### Table Formulas

  Table Mode now has support for formulas like a spreadsheet. There are 2 ways
  of defining formulas :

  - You can add formulas using `:TableAddFormula` or the mapping <kbd>\<Leader\>tfa</kbd>
    defined by the option `g:table_mode_add_formula_map` from within a table
    cell, which will ask for input on the cmd-line with a `f=` prompt. The
    input formula will be appended to the formula line if one exists or a new
    one will be created with the input formula taking the current cell as the
    target cell. The formula line is evaluated immidiately to reflect the
    results.

  - You can directly add / manipulate formula expressions in the formula line.
    The formula line is a commented line right after the table, beginning with
    'tmf:' (table mode formula). eg) `# tmf: $3=$2*$1`.  You can add multiple
    formulas on the line separated with a ';' eg) `# tmf: $3=$2*$1;$4=$3/3.14`

  You can evaluate the formula line using `:TableEvalFormulaLine` or the
  mapping <kbd>\<Leader\>tfe</kbd> defined by the option `g:table_mode_eval_expr_map`
  from anywhere inside the table or while on the formula line.

  NOTE: You can now use the mapping <kbd>\<Leader\>t?</kbd>

### Formula Expressions

  Expressions are of the format `$target = formula`.

  - The `target` can be of 2 forms :

      - `$n`: This matches the table column number `n`. So the `formula` would
        be evaluated for each cell in that column and the result would be placed
        in it. You can use negative indice to represent column relative to the
        last, -1 being the last.

      - `$n,m`: This matches the table cell n,m (row, column). So in this case
        the formula would be evaluated and the result will be placed in this
        cell. You can also use negative values to refer to cells relative to
        the size, -1 being the last (row or column).

  - The `formula` can be a simple mathematical expression involving cells
    which are also defined by the same format as that of the target cell.  You
    can use all native vim functions within the formula. Apart from that table
    mode also provides 2 special functions `Sum` and `Average`. Both these
    functions take a range as input. A range can be of two forms :

      - `r1:r2`: This represents cells in the current column from row `r1`
        through `r2`. If `r2` is negative it represents `r2` rows above the
        current row (of the target cell).

      - `r1,c1:r2,c2`: This represents cells in the table from cell r1,c1
        through cell r2,c2 (row, column).

  - Examples :
      - `$2 = $1 * $1`
      - `$2 = pow($1, 5)` NOTE: Remember to put space between the $1, and 5
        here otherwise it will be treated like a table cell.
      - `$2 = $1 / $1,3`
      - `$1,2 = $1,1 * $1,1`
      - `$5,1 = Sum(1:-1)`
      - `$5,1 = float2nr(Sum(1:-1))`
      - `$5,3 = Sum(1,2:5,2)`
      - `$5,3 = Sum(1,2:5,2)/$5,1`
      - `$5,3 = Average(1,2:5,2)/$5,1`
