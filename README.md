# EPICS + synApps Installation Guide

Personal notes and step-by-step tutorial for installing **EPICS Base** and **synApps** on a Linux system.

---

## 1. Prerequisites

Update system and install required packages:

```bash
sudo apt update && sudo apt -y upgrade
sudo apt install g++ make libreadline-dev re2c libpcre3-dev libxext-dev libraw1394-dev libusb-1.0-0-dev bzip2 libusb-dev libtirpc-dev git wget
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

## 2. Get and Install EPICS-Base

Get latest EPICS Base Release, always check on [EPICS Github](https://github.com/epics-base/epics-base).
```bash
wget https://github.com/epics-base/epics-base/releases/download/R7.0.9/base-7.0.9.tar.gz
```

Extract the file (and rename for easier management).
```bash
tar -xvf base-7.0.9.tar.gz
mv base-7.0.9 base
```

Move to EPICS Base directory, then make
```bash
cd base
make -j
```

Wait until everything finish.

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

## 4. Get SYNAPPS

synApps installation is set by synApps assemble script. Check to [EPICS SYNAPP Github](https://github.com/EPICS-synApps) for release.
```bash
wget https://github.com/EPICS-synApps/assemble_synApps/releases/download/R6-3/assemble_synApps
```

