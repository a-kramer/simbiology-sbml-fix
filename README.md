# Simbiology sbml Fix

# Fix for MATLAB&trade; SBML

MATLAB&reg;'s Simbiology&reg; toolbox generates sbml code that doesn't work very well and doesn't look very nice (and it cannot be imported into Copasi).

This `R` script fixes most of the issues. The resulting `sbml` file can be imported into Copasi without warnings and runs there.

Both scripts replace spaces in item names with underscores. They do not check for other weird characters in names (like slashes or similar).

## Script (in R) 

The function `sbml_fix()` in the file [sbml_fix.R](sbml_fix.R) adds default units to the `sbml` file and fixes all ids. It does all of the work here.

## Usage

```R
source("sbml_fix.R")
sbml_fix("D1_LTP_time_window_SBML.xml",
          volumeUnit=c("liter"),substanceUnit=c("n","mol"),timeUnit=c("m","s"),
          lengthUnit=c("u","m"),areaUnit=c("m","m",2))
```

The first argument (the file-name) is mandatory. The units themselves are optional and have default values:

|unit for| default value |
|-------:|:--------------|
| volume    | liter |
| substance | nmol |
| time | ms |
| area | µm² |
| length | µm |

These units are not the defaults from the sbml documentation, they are the defaults we used in this project.
The latter two: area, and length, are not important for our purposes. 
The default unit for concentrations is derived from the above: substance/volume (so nmol/l)

As shown, the units are given as character vectors of one, two, or
three components: `c(prefix,unit,exponent)`, e.g.: `c("m","m")` for
millimeters (the prefix and exponent are both optional). The entries can also
be given in long form: `c("kilo","meter")`.  

The prefix and unit _cannot_ be mixed together, like
`substance="nmol"`, because parsing this seems difficult to me. 

The last component is an exponent, it will be converted to a character
even if specified as a number.

Even though the prefix and exponent are both optional, the prefix must
be present if the exponent is present, example: `areaUnit=c("","m",2)`
for square-meters; this way it is always clear what to do with a
2-vector (prefix, unit).

The output is a file named `"Fixed_${ORIGINAL_FILENAME}"`

### Rscript File `sbml_fix_via_R.sh`

This is a wrapper file that can be called from the shell (bash, etc.) which then sources and calls the above R script. Usage:
```bash
[shell stuff] $ ./sbml_fix_via_R.sh D1_LTP_time_window_SBML.xml
```
This will use the default units from the table above. This file exists only for convenience and can be a starting point for automated scripting.

## Real Shell Script

The shell script [sbml_fix.sh](sbml_fix.sh) is similar to the R script but does not add the default units. It only fixes the really long `id`s that Simbiology&reg; generates.

Warning: it fixes everything _in place_. So, if this script stops working correctly at some point, it may destroy the file in question.

Because this file is difficult to use for windows users, the functionality has been ported to the `R` script. This shell script is older.
