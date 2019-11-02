# Packages
## How to start a package
- Ref http://web.mit.edu/insong/www/pdf/rpackage_instructions.pdf section 3
- Make sure your enviromnet is empty.

```
getwd()
ls()
library(devtools)
library(roxygen2)
```
```
package.skeleton("test9", code_files = "test_funcs.R")
```
- Delete the files created in the man folder
- Delete the NAMESPACE file
- Update the DESCRIPTION file with the info
```
roxygenize("test9")
```
```
build(name_package)
install(name_package)
test8::get_date(xfile = "tenant_meta.csv")
```
