# PiHole

PiHole in QubesOS [WIP]


1. Create a Service VM with sys-firewall as NetVM.

2. Disable systemd-resolved.

       systemctl stop systemd-resolved
       
       systemctl disable systemd-resolved
       
3. Update VM-

       sudo apt update && apt upgrade
       
4. Install curl and git if not already installed-

       sudo apt install git curl
       
5. Clone PiHole repository and run-

       git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
       
       cd "Pi-hole/automated install/"
       
       sudo bash basic-install.sh
       
6. Follow the instructions. Set any DNS. Note down the last page password and addresses.

7. Create a new VM and set PiHole VM as its NetVM.

8. Now in PiHole VM terminal-

       sudo nano /rw/config/qubes-ip-change-hook
       
9.  Copy and paste following-

        #!/bin/sh
        
        # This will Flush PR-QBS chain
        iptables -t nat -F PR-QBS

        # Redirects all the DNS traffic to localhost:53
        iptables -t nat -I PR-QBS -i vif+ -p udp --dport 53 -j DNAT --to-destination 127.0.0.1

        # Accepts the traffic coming to localhost
        # from XEN's virtual interfaces on port 53

        iptables -I INPUT -i vif+ -p udp --dport 53 -d 127.0.0.1 -j ACCEPT

        # Enable the traffic coming from the virtual interfaces
        # forwarded to the loopback interface
        # enabling the route_localnet flag on them

        echo 1 > /proc/sys/net/ipv4/conf/default/route_localnet
         
    Press Ctrl+X then Y and Enter.
    
    Make it executable.
    
          sudo chmod +x /rw/config/qubes-ip-change-hook
         
10. Now in PiHole VM terminal-

          sudo nano /etc/dnsmasq.conf
          
   Enter following details-
 
          interface=lo
          
          bind-interfaces
          
          conf-dir=/etc/dnsmasq.d
          
   Save this file.
   
11. Reboot both VM (if both running) and reach PiHole webpage with written down details beforehand. Alter settings as you like, play with it.

Disclaimer- This documentation is for only my help. This is work in progress, so if anyone comes across it use it at your own risk. This is inspired by work 
of Patrizio Tufarolo and my own efforts doing that stuff. I don't know about copyright owned by anyone. If anyone want it to be removed please leave a 
message in issues. I will remove it immediately. Thanks. 
