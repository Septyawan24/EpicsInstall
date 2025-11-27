# CSS Phobeus Installation Guide

Personal notes and step-by-step tutorial for installing **CSS Phobeus** on a Linux system.

---
## 1. Prerequisites
Make sure you already install EPICS and JAVA.
```bash
sudo apt install openjdk-21-jdk
```
I always use the newest latest LTS JDK. but the version 21 will work fine.

prepare the installation folder.

```bash
cd /opt
sudo mkdir Apps
chown -R <user>:<group> Apps

cd Apps
mkdir Phobeus
cd Phobeus
```

I always put Phobeus in opt directory, near my EPICS base installation.

## 2. Get CSS Phobeus

Get latest Phobeus Release, always check on [Phobeus Github]([https://github.com/epics-base/epics-base](https://github.com/ControlSystemStudio/phoebus/releases)).
```bash
wget https://github.com/ControlSystemStudio/phoebus/releases/download/v5.0.2/phoebus-5.0.2-linux.tar.gz
```

Extract the file (and rename for easier management).
```bash
tar -xvf phoebus-5.0.2-linux.tar.gz
mv product-5.0.2 phobeus
```

## 3. Run Phobeus

It run from Shell script. so make it executable.
```bash
cd /phobeus
chmod +x phobeus.sh
```

Run it.
```bash
./phobeus.sh
```

---
# CSS Phobeus Installation Guide On ARM
Suprise! Suprise!

Remember when we dowloaded Phobeus it only said Linux without any architecture like on Mac? and guest what, it shipped with X86_64 java setting/component. so it won't run directly after we get it.
but here I am, choosing the hot spicy Linux "sambel pindang" instead of comforting gourmet food.

I just need to get some java component works with ARM. you can always build everything from source, but I don't have time for that.

## 1. Get and Install Liberica JDK
Get the Liberica JDK from [BellSoft Website](https://bell-sw.com/pages/downloads/#jdk-21-lts)

```bash
wget https://download.bell-sw.com/java/21.0.9+15/bellsoft-jdk21.0.9+15-linux-amd64-full.deb
sudo apt install ./bellsoft-jdk21.0.9+15-linux-amd64-full.deb
```

## 2. Change Shell for Phobeus
Edit the phobeus.sh to match the parameter for JAVA.

```bash
#!/bin/sh
#
# Phoebus launcher for Linux or Mac OS X

# When deploying, change "TOP"
# to the absolute installation path
# TOP="."
TOP="$( cd "$(dirname "$0")" ; pwd -P )"

# Ideally, assert that Java is found
# export JAVA_HOME=/opt/jdk-9
# export PATH="$JAVA_HOME/bin:$PATH"

if [ -d "${TOP}/target" ]
then
  TOP="$TOP/target"
fi

if [ -d "${TOP}/update" ]
then
  echo "Installing update..."
  cd ${TOP}
  rm -rf doc lib
  mv update/* .
  rmdir update
  echo "Updated."
fi

JAR=`echo ${TOP}/product-*.jar`

# Path to Liberica Full JDK (with JavaFX)
JAVA="/usr/lib/jvm/bellsoft-java21-full-aarch64/bin/java"

OPT="-server 4918"

filter1="-help"
filter2="-main"

firstarg=$1;

if test "${firstarg#*$filter1}" != "$firstarg"; then
  $JAVA -Dfile.encoding=UTF-8 -jar $JAR $OPT "$@"
elif test "${firstarg#*$filter2}" != "$firstarg"; then
  $JAVA -Dfile.encoding=UTF-8 -jar $JAR $OPT "$@"
else
  $JAVA -Dfile.encoding=UTF-8 -jar $JAR $OPT "$@" &
fi

```


