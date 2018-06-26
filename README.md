# KillProcess
A script that takes in Port number from the command line and kills any process running on that port.

#!/usr/bin/env python3.6

import argparse
import os
import subprocess

parser = argparse.ArgumentParser(description="Kills process running on port")
parser.add_argument("port_number",help="Enter line number.")

port = parser.parse_args().port_number

try:
    result = subprocess.run(['lsof','-n','-i4TCP:%s'%port],stdout=subprocess.PIPE,stderr=subprocess.PIPE)
    output = result.stdout.decode()
except subprocess.CalledProcessError:
    print(f"No process listening on port {port}")
else:
    listening = None
    for l in output.splitlines():
        if "LISTEN" in l :
            listening = l
            break
        if listening:
            pid = int(listening.split()[1])
            os.kill(pid,9)
            print(f"Process {} has been successfully killed")
        else:
            print(f"No process listening on port {port}")
