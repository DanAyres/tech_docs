# Setup R kernel

```shell
install.packages('IRkernel', contriburl="file:///root/r_packages/")
IRkernel::installspec(user = FALSE)
```
# Install offline packages

A list of R packages can be found [here](https://cran.r-project.org/)

```shell
library(tools)
write_PACKAGES("/root/r_packages/")
install.packages("ggplot2", contriburl="file:///path/to/packages/")
```


remove.packages('IRkernel')
install.packages('devtools', contriburl="file:///root/r_packages/")
