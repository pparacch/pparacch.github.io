---
layout: post
date: 2017-04-09
title: "Plumbing - Updating R in few simple steps on Windows"
description: "R maintenance and plumbing #rnewversion #rmanteinance"
---
Maintaining R on a Windows based computer is not a simple and straightforward task.

When a new version of R is published you should go through a set of steps like

* download the new version of R and install it locally on the machine,
* reinstall all of the needed packages for the new version of R, and
* if using RStudio, set properly the version of R to be used, especially when having multiple versions installed on the same machine

Some plumbing work to be done....

## A silver bullet: `installr`

There is a great package on CRAN, __[installr](https://CRAN.R-project.org/package=installr)__, that makes it possible to automate the updating of R and other supported applications. In order to be able to use it, the package needs to be installed locally on the machine.

### How to install `installr`

Open RStudio or the R console and type

```
install.packages("installr", dependencies = TRUE)

#If notified that the package is not available for R version ..
#check the setting of the CRAN mirror used for downloading the
#package
```

### How to update to the newest R version

Updating R to the newest version is as simple as using the `updateR()` function from the `installr` package.

  __Note!!__ When __updating R__ remember to __use the R Console__. If using RStudio you are advised to cancel the installation and use the R console.  

```
#load the installr library
library(installr)
updateR()
```

Behind the scene the function checks if a new version is available and, if available, notify and ask the user to install it (with the option to see what´s new).

<img src="{{site.url}}/assets/img/updateR/01_updateR_Rgui.PNG" width="500" />

<img src="{{site.url}}/assets/img/updateR/02_updateR_confirm_Rgui.PNG" width="500" />

If the user decides to install it, the latest version of R is downloaded and the installation is started. As part of this process the user is asked the language and where to install the new version of R.

The function helps managing the existing packages. The user is asked to copy the existing packages into the new installation and update them, and if interested in removing them. More info about what´s going on can be found using `?updateR`.

<img src="{{site.url}}/assets/img/updateR/03_movingPackages_Rgui.PNG" width="500" />

<img src="{{site.url}}/assets/img/updateR/04_updatePackages_Rgui.PNG" width="500" />

<img src="{{site.url}}/assets/img/updateR/05_removingOldPackages_Rgui.PNG" width="500" />

When finished managing the packages the user is asked to start a new R console for the new version of R. Note that the new version of R 3.3.3 is used.

<img src="{{site.url}}/assets/img/updateR/06_newVersion_Rgui.PNG" width="500" />

__Note!!__ The old version and the newest version of R are both locally installed on the machine.

For completion, start __RStudio__ and __verify that the newest version of R is used__ (see `Tools -> Global Options -> General - R Version`).

<img src="{{site.url}}/assets/img/updateR/07_RStudio_newR.PNG" width="500" />

### And it is more than that...

`installr` can be used for installing other tools - start a R console and try it out `installr()`. More information about functions provided by the package can be found in its [vignette](https://cran.r-project.org/web/packages/installr/installr.pdf).
