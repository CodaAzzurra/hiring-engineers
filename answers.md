# Answers for Daniel Cohen
## DataDog Technical Exercise

Daniel Cohen  
daniel@cohen.net  
January 2019

---

## Prerequisites

_Note: I performed these steps on macOS Mojave, version 10.14.2. Your mileage may vary._

1. Install VirtualBox v6.0.0 and Oracle VM Extension Pack via https://www.virtualbox.org/. Defaults are OK.
2. Install Vagrant v2.2.3 via https://www.vagrantup.com/intro/getting-started/. Defaults are OK.
3. Create the Linux (specifically, Ubuntu 12.04 LTS 64-bit) virtual machine. Open a Terminal window and run:
	``
	osxltdcohe:~ daniel.cohen$ vagrant init hashicorp/precise64
	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.
	osxltdcohe:~ daniel.cohen$
	``
4. Launch the virtual machine via Vagrant. (Note that this may take a few minutes.) In the Terminal:
	``
	osxltdcohe:~ daniel.cohen$ vagrant up
	Bringing machine 'default' up with 'virtualbox' provider...
	==> default: Box 'hashicorp/precise64' could not be found. Attempting to find and install...
	    default: Box Provider: virtualbox
	    default: Box Version: >= 0
	==> default: Loading metadata for box 'hashicorp/precise64'
	    default: URL: https://vagrantcloud.com/hashicorp/precise64
	==> default: Adding box 'hashicorp/precise64' (v1.1.0) for provider: virtualbox
	    default: Downloading: https://vagrantcloud.com/hashicorp/boxes/precise64/versions/1.1.0/providers/virtualbox.box
	    default: Download redirected to host: vagrantcloud-files-production.s3.amazonaws.com
	==> default: Successfully added box 'hashicorp/precise64' (v1.1.0) for 'virtualbox'!
	==> default: Importing base box 'hashicorp/precise64'...
	==> default: Matching MAC address for NAT networking...
	==> default: Checking if box 'hashicorp/precise64' version '1.1.0' is up to date...
	==> default: Setting the name of the VM: danielcohen_default_1547372216992_1264
	.
	.
	.
	==> default: Forwarding ports...
	    default: 22 (guest) => 2222 (host) (adapter 1)
	==> default: Booting VM...
	==> default: Waiting for machine to boot. This may take a few minutes...
	    default: SSH address: 127.0.0.1:2222
	    default: SSH username: vagrant
	    default: SSH auth method: private key
	.
	.
	.
	    default: Guest Additions Version: 4.2.0
	    default: VirtualBox Version: 6.0
	==> default: Mounting shared folders...
	    default: /vagrant => /Users/daniel.cohen
		osxltdcohe:~ daniel.cohen$
	``
5. Sign up for DataDog via https://www.datadoghq.com/.
6. Install the DataDog Ubuntu Agent.
	1. In the Terminal, SSH into the Ubuntu VM via Vagrant.  
		``
		osxltdcohe:~ daniel.cohen$ vagrant ssh
		Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)
		.
		.
		.
		Welcome to your Vagrant-built virtual machine.
		Last login: Fri Sep 14 06:23:18 2012 from 10.0.2.2
		vagrant@precise64:~$
		``
	2. Install _curl_.  
		``
		vagrant@precise64:~$ sudo apt-get install curl
		Reading package lists... Done
		Building dependency tree       
		Reading state information... Done
		The following extra packages will be installed:
		  libcurl3
		The following NEW packages will be installed:
		  curl libcurl3
		0 upgraded, 2 newly installed, 0 to remove and 66 not upgraded.
		Need to get 374 kB of archives.
		After this operation, 910 kB of additional disk space will be used.
		Do you want to continue [Y/n]? y
		Get:1 http://us.archive.ubuntu.com/ubuntu/ precise/main libcurl3 amd64 7.22.0-3ubuntu4 [237 kB]
		Get:2 http://us.archive.ubuntu.com/ubuntu/ precise/main curl amd64 7.22.0-3ubuntu4 [138 kB]
		.
		.
		.
		ldconfig deferred processing now taking place
		vagrant@precise64:~$ 
		``
	3. Install and launch the DataDog Agent.
		``
		vagrant@precise64:~$ DD_API_KEY=b3cff61e756ab51997e27cbdabb0a0bd bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/datadog-agent/master/cmd/agent/install_script.sh)"
		  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
		                                 Dload  Upload   Total   Spent    Left  Speed
		100 11080  100 11080    0     0  29202      0 --:--:-- --:--:-- --:--:-- 96347
		.
		.
		.
		datadog-agent start/running, process 2093
		.
		.
		.
		vagrant@precise64:~$ 
		``
7. In the browser, click Finish to continue to the DataDog Quick Start page.

## Collecting Metrics
### Add tags in the Agent config file and show us a screenshot of your host and its tags on the Host Map page in Datadog.

1. Add tags to the Agent configuration file. Per the command line output from the Agent installation, the Agent confuguration file is `/etc/datadog-agent/datadog.yaml`. Open that file with your favorite editor such as vi.
2. Find the _tags_ section.
3. Uncomment as necessary and add tags as below:  
	`
	tags:
	 - env:localhost
	 - username:danielcohen
	`
4. Restart the Agent.  
	`
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ sudo service datadog-agent restart
	datadog-agent stop/waiting
	datadog-agent start/running, process 4762
	`
5. Check the Host Map page in DataDog for the expected tags.  
	![](/Users/daniel.cohen/Documents/GitHub/hiring-engineers-fork/image/005_tags_small.png)

### Install a database on your machine (MongoDB, MySQL, or PostgreSQL) and then install the respective Datadog integration for that database.

1. Install a PostgreSQL database on the Ubuntu VM. (Note: I will consider these instructions as out of scope. However for reference, I followed loosely the instructions on https://help.ubuntu.com/lts/serverguide/postgresql.html.en.)
2. Find the PostgreSQL integration on https://docs.datadoghq.com/integrations/ and follow the instructions there.
3. Prepare Postgress for DataDog by creating the appropriate PostgreSQL user.  
	``
	vagrant@precise64:/etc/postgresql/9.1/main$ sudo -u postgres psql template1
	Password: 
	psql (9.1.24)
	Type "help" for help.
	template1=# create user datadog with password '********';
	CREATE ROLE
	template1=# grant SELECT ON pg_stat_database to datadog;
	GRANT
	template1=# 
	``
4. In the directory `/etc/datadog-agent/conf.d/postgres.d`, copy `conf.yaml.example` to `conf.yaml`.
	`
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ sudo cp conf.yaml.example conf.yaml
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ ls
	conf.yaml  conf.yaml.example
	`
5. Edit that conf.yaml to add the `datadog` username and password as created above. Also add the tags `env:localhost` and `username:danielcohen` for good measure.
