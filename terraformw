#!/bin/bash 

# based on https://tfswitch.warrensbox.com/Continuous-Integration/

echo "Installing tfswitch locally"

# Get the installer on to your machine
wget -N -c https://raw.githubusercontent.com/warrensbox/terraform-switcher/release/install.sh

# Make installer executable
chmod 755 install.sh

# Install tfswitch in a location you have permission
./install.sh -b $(pwd)/.bin

# set custom bin path
CUSTOMBIN=$(pwd)/.bin

#Add custom bin path to PATH environment
export PATH=$CUSTOMBIN:$PATH

$CUSTOMBIN/tfswitch -b $CUSTOMBIN/terraform

terraform $*