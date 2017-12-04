# system-integration-assignment-2

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