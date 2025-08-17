---
date: '2025-08-16T18:38:10+05:30'
draft: false
title: 'Debian to RPM Conversion: Minimal Setup For Compatibility'
summary: "A quick guide on converting Debian package into RPM that may not be a native conversion, but just enough to get it run on your RPM-based distributions."
tags:
- RPM
- Debian
params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true
---

# Overview
There are so many Linux distributions out there, each using different package managers, init systems, and more. 
Some packages are built for specific distributions, with the most popular formats being `.rpm` and `.deb`. 
Unfortunately, you might only find support for the one your system doesn’t use. 
You could face a situation where your package manager is different, but the software you want to install is only available in another format. 
In such cases, your **best options are usually `snap` or `flatpak`**. 
In the worst-case scenario, you’ll need to resort to building from source, using containers, or converting the package yourself. 
Unfortunately, this guide is for the worst case scenario where you have no support for other package and you end up deciding to convert it yourself. 
(_and maybe you can publish them in `copr` or other third party package repositories, so that others don't end up doing the hard way like you did_)

# Getting Started!
We are going to be using Fedora Linux here (_which is an RPM-based distro_), and make sure you have the certain tools installed.
- **`rpmdevtools`**: A package providing various utilities for RPM packaging. It includes scripts, create `.spec` files, and more.
- **`rpmbuild`**: A text file that defines how the package is built, installed and configured.
- **`tar`**: Used to create archives of your source code, usually needed for the `SOURCES/` directory. (_iirc, `tar` will be installed by default_)
- **`ar`**: A tool used to create and extract archives. Which we definitely need to extract the Debian packages.
- **`make`, `gcc`**: Helps you build the binaries before packaging them.

**Install**:
```sh
sudo dnf install rpm-build rpmdevtools make gcc
```

## Setup RPM Build Environment
```sh
rpmdev-setuptree
```
It creates the environment in your home directory:
```sh
# directory structure
~/rpmbuild/
├── BUILD
├── RPMS
├── SOURCES
├── SPECS
├── SRPMS
```

- **BUILD/**: A `chroot` environment where the actual package will be built here and the data will be deleted once its completed.
- **RPMS/**: This is where the `.rpm` package will be built and stored.
- **SOURCES/**: For storing the source code in a `.tar` archive.
- **SPECS/**: Storing `.specs` file for each packaging works.
- **SRPMS/**: This is where the `.src.rpm` package wil be build and stored(_Source RPM_).

## Converting Debian Package
I'm going to use [hayase](https://hayase.watch/) an application for streaming anime via torrents, as an example for converting it from `.deb` to `.rpm` package.
> Note: This might not be 100% total conversion, but it will give you a package right away to start using it without any problem at. Although you might not be able to use all the features.
### Extract The Debian Package
- Download the package
```sh
# make sure to download the application you want and don't follow along the code by just copy pasting the commands...XD
wget https://github.com/hayase-app/ui/releases/download/v6.4.23/linux-hayase-6.4.23-linux.deb
```
- Extract them inside `SOURCES/` (_its easier to convert them inside this directory rather than making it somewhere else_)
```sh
mkdir hayase-6.4.23
```

```sh
mv linux-hayase-6.4.23-linux.deb hayase-6.4.23/
```
```sh
cd hayase-6.4.23
ar x linux-hayase-6.4.23-linux.deb
```
> **Note**: `ar` tool is used to extract the Debian package.

```sh
iamyaash@fedora:~/rpmbuild/SOURCES/linux-hayase-6.4.23-linux$ ll
total 67M
-rw-r--r--. 1 iamyaash iamyaash 3.3K Aug 15 15:59 control.tar.gz
-rw-r--r--. 1 iamyaash iamyaash  67M Aug 15 15:59 data.tar.xz
-rw-r--r--. 1 iamyaash iamyaash    4 Aug 15 15:59 debian-binary
```
- **`control.tar.gz`**: Contains the scriptlets (_`postrm, prerm, postinst, preinst`_), `md5sums`, `control` and more.
- **`data.tar.xz`**: Contains the actual source code that runs the software.
- **`debian-binary`**: Contains just the text of the packaging format version.

### Archive The Final Build

After extracting the "Debian Package", it will have `data.tar.xz`, `control.tar.gz` & `debian-binary`.

As the file names suggest, `data.tar.xz` contains the final build of the software. In simple terms, `data.tar.xz` is the software that is archived. 

Let's extract the data, move them inside a directory and archive it in `gz` format. Because, we need them to be archived in order to package it. 

1. **Extract `data.tar.xz`:**
```sh
tar -xvJf data.tar.xz
```
```sh
iamyaash@fedora:~/rpmbuild/SOURCES/linux-hayase-6.4.23-linux$ ll
total 144M
-rw-r--r--. 1 iamyaash iamyaash 3.3K Aug 15 15:59 control.tar.gz
-rw-r--r--. 1 iamyaash iamyaash  67M Aug 15 15:59 data.tar.xz
-rw-r--r--. 1 iamyaash iamyaash    4 Aug 15 15:59 debian-binary
drwxr-xr-x. 1 iamyaash iamyaash   12 Aug 15 02:34 opt/
drwxr-xr-x. 1 iamyaash iamyaash   10 Aug 15 02:34 usr/
```
The `data.tar.xz` contains the final build of the software and we need to place them under a directory and archive it.

2. **Move the build files into a directory named `<name><version>/`:**
```sh
mkdir hayase-6.4.23/
mv opt/ usr/ hayase-6.4.23/
```

This would be directory structure after moving `opt/` `usr/` inside `hayase-6.4.23/`,
```sh
iamyaash@fedora:~/rpmbuild/SOURCES/linux-hayase-6.4.23-linux$ ll hayase-6.4.23/
total 4.0K
drwxr-xr-x. 1 iamyaash iamyaash   12 Aug 15 02:34 opt/
drwxr-xr-x. 1 iamyaash iamyaash   10 Aug 15 02:34 usr/
```

3. **Archive the whole directory into `tar.gz` format.**
```sh
tar -cvzf hayase-6.4.23.tar.gz hayase-6.4.23
```

4. **Move the archive into the parent direcory `SOURCES/`:**
```sh
iamyaash@fedora:~/rpmbuild/SOURCES$ ll
total 95M
drwxr-xr-x. 1 iamyaash iamyaash   52 Aug 15 16:04 hayase-6.4.16/
-rw-r--r--. 1 iamyaash iamyaash  95M Aug 15 16:05 hayase-6.4.23.tar.gz
```

## RPM Packaging 
Finally, we archived the source code, placed it under `SOURCES/` and we have completed preparing the source code part.
Now, its time to focus on writing installation instruction in a `.spec` file.
### Write Installation Instructions
- Create a `.spec` file under `SPECS/` directory:
    ```sh
    touch ~/rpmbuild/SPECS/hayase.spec
    # name it however you want, but keep it concise
    ```
- Modify the `.spec` as per the software you need to package. Installation instructions might vary based on the project and its technologies, ensure you write proper instruction and not copy-paste what I'm writing here.

**Sample `.spec` file**:
```spec
Name:           hello
Version:        1.0
Release:        1%{?dist}
Summary:        Simple Hello World program
License:        MIT
URL:            http://example.com/
Source0:        hello-1.0.tar.gz

BuildRequires:  gcc

%description
A simple Hello World program packaged as an RPM example.

%prep
%setup -q

%build
gcc %{optflags} -o hello hello.c

%install
mkdir -p %{buildroot}/usr/bin
install -m 0755 hello %{buildroot}/usr/bin/hello

%files
/usr/bin/hello

%changelog
* Tue Jul 01 2025 Yashwanth Rathakrishnan <you@example.com> - 1.0-1
- Initial package
```