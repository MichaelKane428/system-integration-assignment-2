# system-integration-assignment-2

# Configure client
1. Configure the /etc/resolvconf/resolv.conf.d/base file for the client,
see clientbase file for an example

2. Disable eth0

3. Make sure that eth1 is an internal adapter.

4. Configure eth1 with dhcp see the interfaces file for a dhcp example:



DNS Configuration:
1. Check /etc/resolv.conf to ensure the correct nameservers are connected.
If they are not configured use sudo nano /etc/resolvconf/resolv.conf.d/base and place them here to permenantly add them.
TO view an example check my base file in the files edited folder.

2. Install the dns serve using: sudo apt-get install bind9

3. Create a static IP for eth1 making sure to set it as an internal network if using virtual box,
example found in the interfaces file:
sudo nano /etc/network/interfaces

4. reboot the system:
sudo reboot

5. change directory to bind:
cd /etc/bind

6. copy the db.local file into db.yourdomainname.extension:
sudo cp db.local db.example.lan

7. copy the db.127 file into into db.eth1ipv4address:
sudo cp db.127 db.192.168.1.30

8. Edit named.conf.local to create your zones see example in the named.conf.local file:
sudo nano named.conf.local

9. check the file for errors:
named-checkconf

10. add the google nameservers to the forwarders check named.local.options for an example:
sudo nano named.conf.option

11. Using the db.example.lan file as an example, edit your db.yourdomainname.extension file:
sudo nano db.example.lan

12. Using the db.192.168.1.30 file as an example, edit your db.eth1ipv4address file:
sudo nano db.192.168.1.30

13. check your zones:
named-checkzone example.lan db.example.lan

named-checkzone 1.168.192.in-addr.arpa db.192.168.1.30

14. restart bind9
sudo /etc/init.d/bind9 restart

15. add your dns-server address to the base file check my base file for an example.

16 test the nslookups and ping
nslookup example.lan
nslookup 192.168.1.30
ping 192.168.1.30


DHCP Cnfiguration:
1. Install the Dhcp server:
sudo apt-get install isc-dhcp-server

2. edit the isc-dhcp-server file using mine as an example:
sudo nano /etc/default/isc-dhcp-server

3. edit the dhcpd.conf file using mine as an example:
sudo nano /etc/dhcp/dhcpd.conf

4. Restart the dhcp service:
sudo service isc-dhcp-server restart

ENABLE SERVER ROUTING:
1. allow routing of the initial packets. 
sudo iptables -A FORWARD -o eth0 -i eth1 -s 192.168.0.0/24 -m conntrack --ctstate NEW -j ACCEPT

2. When the connection is established allow routing of the remaining packets.
sudo iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

3. allow nating
sudo iptables -t nat -F POSTROUTING
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

4. save the changes:
sudo iptables-save | sudo tee /etc/iptables.sav  //this is not a typo.

5. modify /etc/rc.local file add the following line before exit 0 or see example file.
iptables-restore < /etc/iptables.sav 

6. Enable ip forwarding:
sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"

7. uncomment the following line:
net.ipv4.ipforward=1

8. add the following lines after the above:
net.ipv4.conf.default.forwarding=1
net.ipv4.conf.all.forwarding=1


Enable Client Routing
1. configure routing:
sudo ip route add default via 192.168.1.1