Best REST in OpenStack - workshop steps
=======================================

### Step 1. 
Select the VM ``devstack-api`` in VirtualBox and press the "**Start**" button
   (green right arrow at the top). Wait a minute for the VM to boot.

### Step 2. 
After the VM boots and the login prompt is seen, open a terminal on the laptop,
   and SSH into the VM. Password is ``ciscolive``.

```
ssh stack@192.168.10.3
```

### Step 3. 
Deploy OpenStack Ocata.  It takes about 13 minutes.

```
cd devstack_odl
./stack.sh
```

### Step 4.
After ``stack.sh`` ends, the OpenStack Horizon GUI can be accessed in a browser at [http://192.168.10.3](http://192.168.20.3). The username is ``admin`` and password is ``password``.

### Step 5. 
In the OpenStack GUI, select the ``demo`` project at the top left (next to the
   OpenStack logo). On the left pane, click on ``Project --> Network --> Network Topology`` and click on the ``Graph`` tab.

### Step 6. 
In the terminal, source the environment variables needed for OpenStack. Ignore the warning.

```
source openrc
```

### Step 7.
Get the github repository for this lab using: 

```
cd ~
git clone https://github.com/vallard/Cisco-Live-Workshop.git
cd Cisco-Live-Workshop/scripts
```

### Step 8. 
Enter the ``python`` interpreter.

```
python
```

### Step 9.
Import the setup library.

```
import api_setup as api
```

### Step 10.

Set a ``prefix`` which is unique and not same as others. It is a good idea to use your first name or last name or something fun. We will use this ``prefix`` to create virtual objects in OpenStack and also to differentiate your HTTP packets from others.

```
prefix = "<somethingFun>"
```
Don't use spaces! (example: ```prefix="spiderman"```)


### Step 11.
Create an OpenStack neutron network, check the OpenStack GUI, analyze HTTP packet.

```
api.create_network(prefix)
```

This will create a file at ```~/packet-captures``` as the script captures this data.  Now we will want to look at it. 

### Step 12.

OPEN A NEW WINDOW!  Make sure in this new window you do not log into the virtual machine.  Copy the packet files: 

```
scp -r stack@192.168.10.3:~/packet-captures . 
```

This will copy the packet you created to the local machine.  Now we can analyze it in Wireshark. 

### Step 13.

Analyze the file in wireshark.  

* Open Wireshark
* In the File -> Open -> labuser/packet-captures you can open and look at the HTTP requests. 

### Step 14.

Create several more resources and examin the output in the OpenStack GUI as well as the Wireshark outputs

```
api.create_subnet(prefix)  # create neutron subnet
api.create_router(prefix)  # create neutron router
api.setup_router(prefix)   # add router gateway and router interface
```

Do steps 12 and 13 again to see the output in wireshark.


### Step 15.

Create a new VM

```
api.boot_vm(prefix)        # boot a virtual machine
```

### Step 16.
Open another terminal on the laptop and SSH into the ``devstack-api`` VM (password is ``ciscolive``). Copy the command in the output of the last step above, paste it in this terminal to SSH into the OpenStack nova virtual machine. Check internet connectivity inside the nova virtual machine.

```
ssh stack@192.168.20.3
sudo ip netns exec <ID seen in the output of step 15 above> ssh cirros@<IP address seen in the output of step 15 above>
```
Type yes, the password is: ```cubswin:)``` 

In the VM of the VM type:

```
ip a
ping -c 5 www.google.com
ping -c 5 www.cisco.com
exit
exit
```

### Step 17.
Back in the first terminal, delete the nova virtual machine and networking components.  After each command check the OpenStack GUI, and analyze HTTP packets. 

```
api.delete_vm(prefix)
api.delete_router(prefix)
api.delete_subnet(prefix)
api.delete_network(prefix)
```

### Step 18.
Create many networks in one REST call: 

```
api.create_many(prefix)
```

### Step 19.
Delete 8 networks, 8 subnets, 8 routers. This takes about 2 minutes to finish. Check the OpenStack GUI.

```
api.delete_many(prefix)
```

### Step 20.
Clean up!

In the VM:

```
rm -rf ~/Cisco-Workshop ~/packet-captures
cd devstack_odl
./clean.sh
```

On the Mac:

```
rm -rf ~/packet-captures
```

Power off the ```devstack-odl``` machine in VirtualBox and close the application.  

Thank you!
