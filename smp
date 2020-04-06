#!/bin/bash

# Copyright 2019 UwUnyaa
# 
# This file is part of smt.
# 
# smt is free software: you can redistribute it and/or modify it under the
# terms of the GNU General Public License as published by the Free Software
# Foundation, either version 3 of the License, or (at your option) any later
# version.
# 
# smt is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
# FOR A PARTICULAR PURPOSE. See the GNU General Public License for more
# details.
# 
# You should have received a copy of the GNU General Public License along with
# smt. If not, see <http://www.gnu.org/licenses/>.

# smt - Disables or enables SMT on the fly.
#
# This script must be run as root, as it writes to sysfs.
#
# usage:
# 	smt [on|off]

if (( EUID != 0 )); then
    echo "script must be run as root"
    exit 1
fi

case $1 in
    "on")
        enable_smt=1
        ;;
    "off")
        enable_smt=0
        ;;
    *)
        echo "Usage:"
        echo "	$(basename "$0") [on|off]"
        exit 1
        ;;
esac

set_cpu_online_state () {
    n_cpu=$1
    state=$2
    echo "$state" > "/sys/devices/system/cpu/cpu${n_cpu}/online"
}

if (( enable_smt == 0 )); then
    smt_cores=$(cat /sys/devices/system/cpu/cpu*/topology/thread_siblings_list |\
                    cut -d - -f 2 |\
                    sort -u)
    for n_cpu in $smt_cores; do
        set_cpu_online_state "$n_cpu" 0
    done
else
    num_cpus=$(nproc --all)
    # skip cpu0, as it cannot be disabled on x86, and the online file doesn't
    # exist
    for (( n_cpu=1; n_cpu<num_cpus; n_cpu+=1 )); do
        set_cpu_online_state "$n_cpu" 1
    done
fi
