**Two Tier Architecture**

![alt text](https://github.com/ioanan11/two_tier_architecture/blob/main/Screenshot%202021-09-03%20093523.png)


1. App Instance

-launch instance
-configure instance details as done normally: 

	number of instances: 1 (unless stated otherwise)
	network: vpc-07e(default)
	subnet: 0429...Student
	auto-assign public ip: enable

-configure security group:

	select existing security group
	security group name: sre_ioana_app
	description: sre_ioana_app (type ssh, port 22, my IP)
	later other ports can be added (e.g. port 3000, custom TCP)
	rule for http to be accessible from anywhere

-launch instance

-to set the app machine up we need a provision file containing:

	!#/bin/bash
	sudo apt-get update -y
	sudo apt-get upgrade -y
	sudo apt-get install nginx -y
	sudo apt-get install python-software-properties -y
	curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
	sudo apt-get install nodejs -y

-use sudo chmod +x provision_app.sh and run it ./provision_app.sh

(or install all dependencies manually)

-copy app to cloud using command: 

	scp -i ~/.ssh/sre_key.pem app ubuntu@<IP>:/home/ubuntu/

-copy nginx config file using the same command

	scp -i ~/.ssh/sre_key.pem default ubuntu@<IP>:/home/ubuntu/




2. DB Instance

-set up instance on AWS the same way

-edit inbound rules:

	port range 22, protocol tcp, source ip
	port range 27017, protocol tcp, source ip ip/32

-provision db file should contain:

	!#/bin/bash
	sudo apt-get update -y
	sudo apt-get upgrade -y
	wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
	echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
	sudo apt-get update -y
	sudo apt-get install -y mongodb-org
	sudo systemctl start mongod
	sudo systemctl enable mongod

-IP needs to be changed in /etc/mongod.conf to 0.0.0.0

-sudo systemctl restart mongod


Finally, to start the app:

	node seeds/seed.js
	npm start

**How to build AMIs**

Select instance > Actions > Image and templates > Create image



























^G Help       ^O Write Out  ^W Where Is   ^K Cut        ^T Execute    ^C Location
^X Exit       ^R Read File  ^\ Replace    ^U Paste      ^J Justify    ^/ Go To Line
