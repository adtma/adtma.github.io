---
title: 'Compiling and Running cGENIE on the Unity Cluster'
date: 2025-12-10
categories:
  - blog
tags:
  - cGenie
  - hpc
---

This note summarizes the steps I used to compile and run cGENIE on the OSU [Unity cluster](https://osu.teamdynamix.com/TDClient/1929/ASC/KB/ArticleDet?ID=61538). Overall the process is straightforward because most of the heavy dependencies (NetCDF and HDF5) are already installed as cluster modules. The hardest part is not actually compiling NetCDF or HDF5, but dealing with library mismatches between the login node and the compute nodes. In my case, the login node had OpenSSL 1.0 libraries, but the compute nodes did not, which caused NetCDF to fail at runtime. The workaround was to compile my own OpenSSL 1.0 and create compatibility symlinks.

## 1. Load the compiler and dependency modules

Unity already provides appropriate modules for GCC, NetCDF, and HDF5. These work for both compiling and running cGENIE as long as the environment is exported correctly on compute nodes.

I added the following to my `.bashrc` so the modules load automatically:

```bash
module load gnu/9.1.0
module load netcdf-serial/4.7.4
module load hdf5-serial/1.12.0
conda activate pydatu

export PATH=$HOME/local/netcdf/4.6.1/bin:$HOME/local/git/2.9.5/bin:$PATH
export INSTALL_DIR=$HOME/opt/openssl-1.0.2u
export LD_LIBRARY_PATH="$INSTALL_DIR/lib:$LD_LIBRARY_PATH"
export PATH="$INSTALL_DIR/bin:$PATH"
```

The important part is `LD_LIBRARY_PATH`. Compute nodes do not always inherit your login environment, so having these exports in `.bashrc` or explicitly in a SLURM script prevents runtime errors related to library loading.

## 2. OpenSSL issue and manual installation

When compiling cGENIE, everything linked correctly on the login node, but the executable failed on compute nodes with errors like:

```
libssl.so.10 => not found
libcrypto.so.10 => not found
```

This happened because the NetCDF module was built against OpenSSL 1.0, which existed on the login node but not on the compute nodes. Since I could not load an OpenSSL 1.0 module, I compiled it manually in my home directory.

### Steps used to compile OpenSSL 1.0.2u

```bash
wget https://www.openssl.org/source/old/1.0.2/openssl-1.0.2u.tar.gz
tar xzf openssl-1.0.2u.tar.gz
cd openssl-1.0.2u

./config --prefix=$HOME/opt/openssl-1.0.2u --openssldir=$HOME/opt/openssl-1.0.2u shared
make -j4
make install
```

This installs the libraries under:

```
$HOME/opt/openssl-1.0.2u/lib
```

### Creating symlinks to satisfy NetCDF

NetCDF expects RHEL-style names with `.so.10`, while OpenSSL installs `.so.1.0.0`. To make the compute nodes happy:

```bash
cd $HOME/opt/openssl-1.0.2u/lib
ln -s libssl.so.1.0.0 libssl.so.10
ln -s libcrypto.so.1.0.0 libcrypto.so.10
```

After adding `LD_LIBRARY_PATH` pointing to this directory, NetCDF and cGENIE both resolved the correct SSL libraries on compute nodes.

I hope this is helpful, please reach out or leave comments below if you have questions.
