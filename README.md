# ANGRYsearch
Linux file search, instant results as you type

Attempt at making Linux version of Everything Search Engine, or MasterSeeker, or Hddb File Search, because no one else bothered.
Everyone seems to be damn content with linux file searches which are slow, populating results as they go, cli based only, heavily integrated with a file manager, limited to home directory, or are trying to be everything with full-text content search.

![demonstration gif](http://i.imgur.com/nQO5yVM.gif)

Done in python 3, using PyQt5/PySide for GUI

### What you should know:

* unfortunately search results are bound to the beginning of the words present in the name
* so a search for the word - "finite" would not include the results that have in the path word - "infinite"
* previous versions used data from the linux "locate" command, now the data are gathered by python itself
* it's slower, but allows to differentiate between file vs directory, and more in the future

### Install scandir for much faster indexing of your drives:

scandir will be part of python 3.5 by default but we are not there yet, so heres how to install it

* get python-pip, its a package manager for python
  * for ubuntu based distros: `sudo apt-get install python3-pip`
  * for fedora and such: `yum -y install python3-pip`
* then install scandir through pip
  * `sudo pip install scandir`
  * maybe `sudo pip3 install scandir`
* if you want to test if its installed correctly
* write `python` or `python3` in to terminal, whichever gets you python 3
* you will enter python environment informing you of the version
* write `import scandir`
* you should just get moved to a new line, if you get error something is wrong
* ctrl+D to exit python

### How to make it work on your system:

* Arch has [AUR package](https://aur.archlinux.org/packages/angrysearch/), includes scandir installation

for other distros:

* dependencies - `python-pyqt5`, `libxkbcommon-x11`, `sudo`, `xdg-utils`
* most of these you very likely have, except PyQt5, so get it
* for example for ubuntu based ditros: `sudo apt-get install python3-pyqt5`


* now download the latest release of ANGRYsearch, unpack it, go in to the containing directory
* **if you just want to test it, you can run it right away**
  * `python angrysearch.py` or `python3 angrysearch.py`
* remember to remove the database that gets created in /var/lib/angrysearch/ once you are done testing

* for normal installation on your system for every day use we need to place the files where they belong
* we will be using `install` command because in a single line it can copy, create directories and set permissions
* copy angrysearch.py in to /usr/share/angrysearch/angrysearch.py and make it executable
  * `install -Dm755 angrysearch.py "/usr/share/angrysearch/angrysearch.py"`
* copy the icon - angrysearch.svg in to /usr/share/pixmaps/
  * `install -Dm644 angrysearch.svg "/usr/share/pixmaps/angrysearch.svg"`
* time to check your python version, since ANGRYsearch is done in python 3
  * `python --version` if the answer is Python 2.7.6 or similar check python3
  * `python3 --version` this should give you Python 3.4.0 if the plain `python` failed you, if thats the case we need to edit angrysearch.desktop file
    * open angrysearch.desktop in editor of your choice, find the line: `Exec=python /usr/share/angrysearch/angrysearch.py`
    * change it to `Exec=python3 /usr/share/angrysearch/angrysearch.py`
* copy the desktop file - angrysearch.desktop in to /usr/share/applications/
  * `install -Dm644 angrysearch.desktop "/usr/share/applications/angrysearch.desktop"`


after this you should be able to run ANGRYsearch from your application launcher

### How it works:

* on update it crawls through your file system and creates database in /var/lib/angrysearch/
* then its available for search, returning 500 results per query
* config file location: `~/.config/angrysearch/angrysearch.conf`
* [file manager](http://i.imgur.com/KDjbqOW.png) with which to open results can be set there
* on double-click or enter the `file_manager` gets executed with path as parameter
  * if its a directory it gets path directly to that directory
  * if its a file, path by default leads to the containing directory, not the file
* this behavior can be changed in the config by setting `file_manager_receives_file_path` to `true`
* now whatever program you have set as the file manager will get executed with the full path
* there are exceptions ignoring this setting because they can select/highlight the file
* currently it's dolhpin, nemo, nautilus, doublecmd
* without any changes to the config, `xdg-open` is used to detect the default file manager
* `number_of_results` sets how many items are retrieved from the database per key press, 500 default

### KDE visual issues:

Since introducing the bold text highlighting searched phrase in the results,
KDE is causing some trouble with the correct text positioning, at least in my virtual box test.
Will try to solve this eventually, but for now just a way to fix it individually on KDE user's machines

* as root open in editor of your choice: `/usr/share/angrysearch/angrysearch.py`
* path might be different if you just downloaded the package and run it from wherever
* search it for `textRect`
* you will find this line of code: `#textRect.adjust(0, 0, 0, 0)`
* delete `#` to allow that line to influence the program
* change the second zero to value `-3` or whatever looks correct to you
* after you are done it should be `textRect.adjust(0, -3, 0, 0)`
* save and run ANGRYsearch to check if it helped

![KDE text irregularity example](http://i.imgur.com/7XysGGY.gif)
