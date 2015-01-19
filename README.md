Conkeror Config
===============

My configuration for the Conkeror browser. The important configuration
file is [conkerorrc.js](conkerorrc.js). Brief explanations are in the
corresponding org/markdown files.

# Install XULRunner #

See the official [Installation details](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/XULRunner/Getting_started_with_XULRunner).

# How to install Conkeror #

* Clone Conkeror 

```bash
git clone git://repo.or.cz/conkeror.git
```
* Call `make` in the resulting folder.

# Install the add-ons #

We need to install the add-ons, otherwise our configuration won't
load properly. So we start Conkeror with the default configuation and
install the xpi files from the *xpis* directory.

* Run Conkeror

```bash
xulrunner <CONKEROR_REPO>/application.ini
```

* Go to extensions

Press `Meta-x` (or `Alt-x`) and type `extensions` in the mini buffer. Install the
add-ons from the files. Close Conkeror.


# Get this repository #

* Clone it

```bash
git clone https://github.com/sleepomeno/conkerorrc.git ~/.conkerorrc
```

* Initialize the submodules from the root folder of the repo
folder.

```bash
git submodule update --init
```
> Happy Browsing!





