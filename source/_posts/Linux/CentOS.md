
#Add
firewall-cmd --permanent --zone=public --add-port=80/tcp
#Remove
firewall-cmd --permanent --zone=public --remove-port=80/tcp
#Reload
firewall-cmd --reload


# service firewalld restart
systemctl start firewalld.service
systemctl status firewalld.service
systemctl restart firewalld.service
systemctl stop firewalld.service
systemctl disable firewalld.service
firewall-cmd --list-all 



systemctl restart iptables.service
systemctl enable iptables.service

