---
date: '2025-08-16T18:38:10+05:30'
draft: false
title: 'Debian to RPM Conversion: Minimal Setup For Compatibility - Manual'
summary: "A quick guide on converting Debian package into RPM that may not be a native conversion, but just enough to get it run on your RPM-based distributions. Note: Explains the manual way to convert and package."
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

---

## RPM Packaging 
Finally, we archived the source code, placed it under `SOURCES/` and we have completed preparing the source code part.
Now, its time to focus on writing installation instruction in a `.spec` file.
### Installation Instructions
- Create a `.spec` file under `SPECS/` directory:
    ```sh
    touch ~/rpmbuild/SPECS/hayase.spec
    # name it however you want, but keep it concise
    ```

- Modify the `.spec` file according to the software you want to package. Keep in mind that installation steps may differ depending on the project and the technologies it uses. Make sure to write accurate instructions instead of directly copying the examples provided here.

- The good news is that we don’t need to start everything from scratch. We already have a template `.spec` file as a base, and we can build on top of the existing structure.

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

### Extract Instruction Files In Debian Package

The essential installation instructions are already available in the Debian package. Our task is simply to adapt and convert those instructions into the RPM packaging format, following its standards.

- Extract `control.tar.gz`
```sh
tar -xvzf control.tar.gz
```
It contains the scriptlets, control file, and the signature.
```sh
$ tar --exclude="/*" -tzf control.tar.gz 
./
./postrm
./control
./postinst
./md5sums
```
> Note: These files are essential to write the build instructions. We are just going to copy them and convert those to RPM standards.

> _Tip: Open a browser, search the equivalent of the listed files you have and **place them accordingly in the `.spec`** file._

### Write Instructions In Spec File (Convertion)
> **Use `cat`, then copy the contents, convert them to RPM standard if necessary and paste them in `.spec` file under the respective macros**. 
- `postrm` in Debian == `postun` in RPM
```spec
%postun
#!/bin/bash

# Delete the link to the binary
if command -v update-alternatives >/dev/null 2>&1; then
    update-alternatives --remove hayase %{_bindir}/hayase || true
else
    rm -f %{_bindir}/hayase
fi
```

- `postinst` in Debian == `post` in RPM
```spec
%post
#!/bin/bash

if type update-alternatives 2>/dev/null >&1; then
    # Remove previous link if it doesn't use update-alternatives
    if [ -L '/usr/bin/hayase' -a -e '/usr/bin/hayase' -a "`readlink '/usr/bin/hayase'`" != '/etc/alternatives/hayase' ]; then
        rm -f '/usr/bin/hayase'
    fi
    update-alternatives --install '/usr/bin/hayase' 'hayase' '/opt/Hayase/hayase' 100 || ln -sf '/opt/Hayase/hayase' '/usr/bin/hayase'
else
    ln -sf '/opt/Hayase/hayase' '/usr/bin/hayase'
fi

# Check if user namespaces are supported by the kernel and working with a quick test:
if ! { [[ -L /proc/self/ns/user ]] && unshare --user true; }; then
    # Use SUID chrome-sandbox only on systems without user namespaces:
    chmod 4755 '/opt/Hayase/chrome-sandbox' || true
else
    chmod 0755 '/opt/Hayase/chrome-sandbox' || true
fi

if hash update-mime-database 2>/dev/null; then
    update-mime-database /usr/share/mime || true
fi

if hash update-desktop-database 2>/dev/null; then
    update-desktop-database /usr/share/applications || true
fi
```

- `control` in Debian => First few lines with different naming convention in `.spec` file
```control
Package: hayase
Version: 6.4.23
License: unknown
Vendor: ThaUnknown
Architecture: amd64
Maintainer: ThaUnknown
Installed-Size: 238840
Depends: libgtk-3-0, libnotify4, libnss3, libxss1, libxtst6, xdg-utils, libatspi2.0-0, libuuid1, libsecret-1-0
Recommends: libappindicator3-1
Section: default
Priority: optional
Homepage: https://hayase.app
Description: 
  Hayase - Torrent streaming made simple
```
```spec
Name:		hayase
Version:	6.4.23
Release:    1%{?dist}
Summary:	Hayase - Torrent streaming made simple
License:	BSL-1.1
URL:		https://hayase.app
Source0:	hayase-6.4.23.tar.gz
Packager:   Yashwanth Rathakrishnan <yaasharc@gmail.com>
Vendor:     ThaUnknown
Requires:   gtk3, libnotify, nss, libXScrnSaver, libXtst, xdg-utils, at-spi2-core, libuuid, libsecret
Suggests: 	libappindicator-gtk3
BuildArch:  x86_64
%description
Hayase - Torrent streaming made simple
```

- Here, you can notice the differences in naming conventions such as 
    - `Name` == `Package`, 
    - `%description` == `Description`,
    - `URL` == `Homepage`,
    - `BuildArch` == `Architecture` and more.

- You don't need to worry about the `md5sums`; they are just signatures generated by the packager. You can ignore them since you are only trying to package the software minimally.

### Write Instruction for Installation (RPM Standards)

RPM `.spec` snippets needs sections, that fulfills the instructions.
```spec
%prep
%setup #verbose output
# %setup -q
```
- `%prep` section:
    - `%setup` extracts the source archive with verbose output.
    - `%setup -q` for quiet extraction.

```spec
%install
rm -rf %{buildroot}
mkdir -p %{buildroot}
cp -a opt %{buildroot}/opt
cp -a usr %{buildroot}/usr
ls -lR %{buildroot}
```

- `%install` section:
    - Removes any existing buildroot directory.
    - Creates the buildroot directory
    - Copies `opt/` & `usr/` (_arhived previously_) directories from the build sources into the buildroot.
    - List recursively the contents of the buildroot for verification.
> RPM follows a different packaging style, so it builds the package under `BUILD/` directory and that's why we are writing instructions like this. I know it will take some time to understand, but it's just that it copies the sources somewhere and package it effectively rather than packaing in already existing directory that might cause conflicts later possibly because of other files existing in the directory.

- `%check` section:
    - Empty here, typically used for running tests. You can leave it empty if no tests are specified


```spec
%files
/opt/Hayase/*
/usr/share/applications/hayase.desktop
/usr/share/icons/hicolor/16x16/apps/hayase.png
/usr/share/icons/hicolor/32x32/apps/hayase.png
/usr/share/icons/hicolor/48x48/apps/hayase.png
/usr/share/icons/hicolor/64x64/apps/hayase.png
/usr/share/icons/hicolor/128x128/apps/hayase.png
/usr/share/icons/hicolor/256x256/apps/hayase.png
/usr/share/icons/hicolor/512x512/apps/hayase.png
%doc /usr/share/doc/hayase/changelog.gz
%license LICENSE
```

- `%files` section:
    - Lists documentation and license files that will be included in the package.
    - `%doc` - indicates the documentation for the package.
    - `%license` - `LICENSE` of the package.

---
#### My Version of Spec File
```spec
Name:		hayase
Version:	6.4.15
Release:    1%{?dist}
Summary:	Hayase - Torrent streaming made simple
License:	BSL-1.1
URL:		https://hayase.app
Source0:	hayase-6.4.15.tar.gz
Packager:   Yashwanth Rathakrishnan <yaasharc@gmail.com>
Vendor:     ThaUnknown
Requires:   gtk3, libnotify, nss, libXScrnSaver, libXtst, xdg-utils, at-spi2-core, libuuid, libsecret
Suggests: 	libappindicator-gtk3
BuildArch:  x86_64

%global debug_package %{nil}

%description
Hayase - Torrent streaming made simple

%prep
%setup #verbose output
# %setup -q

%install
rm -rf %{buildroot}
mkdir -p %{buildroot}
cp -a opt %{buildroot}/opt
cp -a usr %{buildroot}/usr
ls -lR %{buildroot}

%check

%files
/opt/Hayase/*
/usr/share/applications/hayase.desktop
/usr/share/icons/hicolor/16x16/apps/hayase.png
/usr/share/icons/hicolor/32x32/apps/hayase.png
/usr/share/icons/hicolor/48x48/apps/hayase.png
/usr/share/icons/hicolor/64x64/apps/hayase.png
/usr/share/icons/hicolor/128x128/apps/hayase.png
/usr/share/icons/hicolor/256x256/apps/hayase.png
/usr/share/icons/hicolor/512x512/apps/hayase.png
%doc /usr/share/doc/hayase/changelog.gz
%license LICENSE

%post
#!/bin/bash

if type update-alternatives 2>/dev/null >&1; then
    # Remove previous link if it doesn't use update-alternatives
    if [ -L '/usr/bin/hayase' -a -e '/usr/bin/hayase' -a "`readlink '/usr/bin/hayase'`" != '/etc/alternatives/hayase' ]; then
        rm -f '/usr/bin/hayase'
    fi
    update-alternatives --install '/usr/bin/hayase' 'hayase' '/opt/Hayase/hayase' 100 || ln -sf '/opt/Hayase/hayase' '/usr/bin/hayase'
else
    ln -sf '/opt/Hayase/hayase' '/usr/bin/hayase'
fi

# Check if user namespaces are supported by the kernel and working with a quick test:
if ! { [[ -L /proc/self/ns/user ]] && unshare --user true; }; then
    # Use SUID chrome-sandbox only on systems without user namespaces:
    chmod 4755 '/opt/Hayase/chrome-sandbox' || true
else
    chmod 0755 '/opt/Hayase/chrome-sandbox' || true
fi

if hash update-mime-database 2>/dev/null; then
    update-mime-database /usr/share/mime || true
fi

if hash update-desktop-database 2>/dev/null; then
    update-desktop-database /usr/share/applications || true
fi


%postun
#!/bin/bash

# Delete the link to the binary
if command -v update-alternatives >/dev/null 2>&1; then
    update-alternatives --remove hayase %{_bindir}/hayase || true
else
    rm -f %{_bindir}/hayase
fi

%changelog
* Sun Jul 20 2025 Yashwanth Rathakrishnan <yaasharc@gmail.com> 6.4.15-1
- packaged upstream version 6.4.15 converted from DEB for Copr
- fixed scriptlet failed, exit status 2, by adding /sbin/update-alternatives
```
---
### Summary
I wanted to share this post, mainly because it helped me learn more about packaging and some of the fundamental knowledge about why they are being packaged like that. I mean there are some tools like `alien` which helps you convert Debian pacakge to `.rpm` but I just wanted to do it the manual way and learn how it's done behind the scene. I think it makes sense right? _If you can handle the manual way, you can definitely handle the automatic way_. By now, you would have a basic idea on how to convert packages, and some basic understand as well. 

Thanks for reading!!