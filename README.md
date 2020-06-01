# ICAP Proof of Concept
A demonstration that the Glasswall Rebuild SKD can be used within an ICAP service to regenerate documents.

## Getting started
The baseline code has been clone from the open source project
https://sourceforge.net/projects/c-icap/

The Rebuild SDK is addded as a submodule. When cloning this project the submodule needs to be fetched using the following two commands
```
git submodule init
git submodule update
```
## Installing

To install, the repo needs to be cloned on to the host server, running Linux.
Running the follow commands will ensure the necessary packages are installed.
```
apt install gcc
apt install make
apt install make-guile
apt install automake
apt install automake1.11
apt install doxygen
```

### Glasswall SDK
To install the Glasswall SDK into the Linux run the top level Makefile
```
make gwinstall
```
This copies the Glasswall SDK into the ```/user/lib``` folder.
Once in place the library needs to be registered to make it accessible.
Create a glasswall.classic.conf file, with the installed location
```
echo "/usr/lib" > glasswall.classic.conf
```
Update the etc directory
```
sudo cp glasswall.classic.conf /etc//ld.so.conf.d
```
Run ldconfig to configure dynamic linker run-time bindings
```
sudo ldconfig
```

Check that the Glasswall library has been installed
```
sudo ldconfig -p | grep glasswall.classic
```

Remove the .conf file
```
rm glasswall.classic.conf
```


### Server

Navigate to the server folder (c-icap\c-icap) and run the configure script, specifying where the server should be installed, through the 'prefix' argument.
```
./configure --prefix=/usr/local/c-icap
```
After running the configuration script has been processed, we can compile and install
```
make 
make install
```

### Modules

Naviagate to the modules folder (c-icap\c-icap-modules) and run the configure script, specifing where the server was installed, in both the 'with-c-icap' and 'prefix' arguments.
```
./configure --with-c-icap=/usr/local/c-icap --prefix=/usr/local/c-icap
```
After running the configuration script has been processed, we can compile and install
```
make 
make install
```

After installation, the configuration files for each module/service exist under the c-icap server configuration directory, the /usr/local/c-icap/etc/ using the location folder specified in the 'configure' commands above.  
These configuration files need to be included into the main c-icap server configuration file, by adding the following 
```
Include gw_test.conf
```

### Testing the Installation

On the host server run the ICAP Server with the following command
```
/usr/local/c-icap/bin/c-icap -N -D -d 10
```

From a separate command prompt, run the client utility sending a file through the ICAP Server.
```
/usr/local/c-icap/bin/c-icap-client -f <full path to source file>  -o <full path to output file>
```

