# to install package

pip install pkgname

# to uninstall
## before uninstalling a package check if there are any other packages that need it
```
pip show <pkgname_that_you_want_to_uninstall>
in its output it shows like this.. check that REQUIRED-BY IS EMPTY..
....
Location: C:\Users\sarad\AppData\Roaming\Python\Python313\site-packages
Requires: numpy, python-dateutil, pytz, tzdata
Required-by:
```
##  then uninstall it
pip uninstall <pkgname>

# to identify all pkgs installed in current virtual environment
pip freeze > requirements.txt

# to install all packages in requirements.txt
pip install -r requirements.txt

