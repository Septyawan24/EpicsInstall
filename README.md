# EPICS + synApps Installation Guide

Personal notes and step-by-step tutorial for installing **EPICS Base** and **synApps** on a Linux system.

---

## 1. Prerequisites

Update system and install required packages:

```bash
sudo apt update && sudo apt -y upgrade
sudo apt install g++ make libreadline-dev re2c libpcre3-dev libxext-dev libraw1394-dev libusb-1.0-0-dev bzip2 libusb-dev libtirpc-dev libnet1-dev libpcap-dev git wget
```
Prepare for EPICS folder:

```bash
cd /opt
sudo mkdir Apps
chown -R <user>:<group> Apps

cd Apps
mkdir epics
cd epics
```

I always put EPICS in opt directory, sometimes with symlink and versioning.

## 2. Get and Compile EPICS-Base

Get latest EPICS Base Release, always check on [EPICS Github](https://github.com/epics-base/epics-base).
```bash
wget https://github.com/epics-base/epics-base/releases/download/R7.0.9/base-7.0.9.tar.gz
```

Extract the file (and rename for easier management).
```bash
tar -xvf base-7.0.9.tar.gz
mv base-7.0.9 base
```

Move to EPICS Base directory, then compile.
```bash
cd base
make -j
```

Wait until everything finish.
 - Always finish the compilation process before setting the environment.
 - I found some machine unable to compile because some environment variable already set (happened when I try to reinstall EPICS after removing entire /opt partition by accident)

## 3. Set Environment

Set environment variable for all user and system.
```bash
cd /etc/profile.d
sudo nano epics-env.sh
```

Copy this environment variable.
```bash
export EPICS_BASE=/opt/epics/base
export EPICS_HOST_ARCH=$(${EPICS_BASE}/startup/EpicsHostArch)
export PATH=${EPICS_BASE}/bin/${EPICS_HOST_ARCH}:${PATH}
```

Reboot the system (or source the file).
- I put the environment variable for entire user in the system.
- you can put the variable in your bash.rc or profile at your home.
- other alternative is to put in /etc/environment.

## 4. Get SYNAPPS

synApps installation is set by synApps assemble script. Check to [EPICS SYNAPP Github](https://github.com/EPICS-synApps) for release.
```bash
wget https://github.com/EPICS-synApps/assemble_synApps/releases/download/R6-3/assemble_synApps
```

## 5. Edit assemble_synApps file

The assemble_synApps script provide some control for your SynApps package. here some point I usually did:
- Change the $EPICS_BASE parameter to epics base directory
- Change the $SYNAPPS_DIR to your liking (mine is *synapps*)
- Select the modules you want to install by comment/uncomment the modules. Ofcourse you can install everything.

## 6. Get SYNAPPS modules

Get the module packages by set the assemble_synApps to executable then run it.
```bash
chmod +x assemble_synApps
./assemble_synApps
```

After all process finish, the package will be in $SYNAPPS_DIR/support (mine is ./synapps/support)

## 7. Compile SYNAPPS Modules

Move to SynApps directory then compile.
```bash
cd  ./synapps/support
make
```
wait until everything done! (hope for no error)

## 8. Experience is The Best Teacher 
Compilation process is not always a smooth ride. here some problem I found and the counter measure.
- Want to use Synapps module, dont forget to include RELEASE from synapps folder to your RELEASE application.
- Lack of package requirement (such as libusb, libtirpc, libpcap etc) in some of modules -> install the package (already put in prerequisites)
- TIRPC module not found in linux_arm in asyn -> manually change $TIRPCS=YES in ./asyn-<version>/configure/CONFIG_SITE
- configure/RELEASE failed to override modules RELEASE file during synapps installation -> believe it or not, I just rerun assemble_synapps script soo many times then it fix it self.
- in Raspberry Pi (or in ARM based, I think), some areaDetector Plugins can't be installed.
- in Raspberry Pi, DXP module can't be install. ARM doesn't have x86 style IO ports which correlated to sys/io.h
