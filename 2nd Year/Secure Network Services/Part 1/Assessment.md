# Setting up switches

Blue for configuration
	Connect to console
Red bench outlet connects to other ports on the switch for others in the group
On central panel connect cabinet to switch

# Configuring Vlans

```putty
switch>enable
switch#config
switch(config)#vlan 10                         #creates vlan 10
switch(config-vlan)#name vlan 10               #names the vlan
switch(config-vlan)#exit
switch(config)#vlan 20                         #creates vlan 20
switch(config-vlan)#name vlan 20               #names the vlan
switch(config-vlan)#exit
switch(config)#interface (name of the port connected to)             #goes in interface
switch(config-if)#switchport mode access            #puts port into access mode
switch(config-if)#switchport access vlan 10         #puts the port into vlan 10
switch(config-if)#exit
switch(config)#interface (name of other port connected)             #goes in interface
switch(config-if)#switchport mode access            #puts port into access mode
switch(config-if)#switchport access vlan 20         #puts the port into vlan 20
switch(config-if)#exit
switch(config)#interface (port)
switch(config-if)#switchport mode trunk                    #connects trunk
switch(config-if)#switchport trunk allowed vlan 1-20       #allows vlans 1-20
switch(config-if)#switchport trunk native vlan 1       #uses vlan 1 to connect
```