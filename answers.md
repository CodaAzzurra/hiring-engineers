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
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/001_signup_small.png)
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
		vagrant@precise64:~$ DD_API_KEY=******** bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/datadog-agent/master/cmd/agent/install_script.sh)"
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
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/004_quick_start_small.png)

## Collecting Metrics
### Add tags in the Agent config file and show us a screenshot of your host and its tags on the Host Map page in Datadog.

1. Add tags to the Agent configuration file. Per the command line output from the Agent installation, the Agent confuguration file is _/etc/datadog-agent/datadog.yaml_. Open that file with your favorite editor such as vi.
2. Find the _tags_ section. Uncomment as necessary and add tags as below:  
	``
	tags:
     \- env:localhost
     \- username:danielcohen
	``
3. Restart the Agent.  
	`
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ sudo service datadog-agent restart
	datadog-agent stop/waiting
	datadog-agent start/running, process 4762
	`
4. Check the Host Map page in DataDog for the expected tags.  
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/005_tags_small.png)

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
4. In the directory _/etc/datadog-agent/conf.d/postgres.d_, copy _conf.yaml.example_ to _conf.yaml_.
	`
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ sudo cp conf.yaml.example conf.yaml
	vagrant@precise64:/etc/datadog-agent/conf.d/postgres.d$ ls
	conf.yaml  conf.yaml.example
	`
5. Verify the permissions.  
	``
	vagrant@precise64:/vagrant$ psql -h localhost -U datadog postgres -c \
	> "select * from pg_stat_database LIMIT(1);" \
	> && echo -e "\e[0;32mPostgres connection - OK\e[0m" \
	> || echo -e "\e[0;31mCannot connect to Postgres\e[0m"
	Password for user datadog: 
	Postgres connection - OK
	``
	``
	 datid |  datname  | numbackends | xact_commit | xact_rollback | blks_read | blks_hit | tup_returned | tup_fetched | tup_inserted | tup_updated | tup_deleted | conflicts |          stats_reset          
	-------+-----------+-------------+-------------+---------------+-----------+----------+--------------+-------------+--------------+-------------+-------------+-----------+-------------------------------
	     1 | template1 |           0 |         166 |             1 |       280 |     7063 |        51273 |        2478 |           27 |           3 |           0 |         0 | 2019-01-13 12:43:28.642741+00
	(1 row)
	(END)
	``
6. In _/etc/postgresql/9.1/main/postgresql.conf_, uncomment or set the lines as below. The values may vary to match your environment.
	``
	logging_collector = on
	log_directory = '/var/log/postgresql'
	log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
	log_file_mode = 0644
	log_line_prefix = '%m [%p] %d %a %u %h %c '
	log_statement = 'all'
	``
7. Edit _/etc/datadog-agent/conf.d/postgres.d/conf.yaml_ to add the _datadog_ username and password as created above.
8. Also in _/etc/datadog-agent/conf.d/postgres.d/conf.yaml_, enable log collection from PostgreSQL.  
	``
	logs:
	      \- type: file
	        path: /var/log/postgresql/postgresql*.log
	        source: postgresql
	        sourcecategory: database
	        service: postgresql
	        #To handle multi line that starts with yyyy-mm-dd use the following pattern
	        log_processing_rules:
	            \- type: multi_line
	              pattern: \d{4}\-(0?[1-9]|1[012])\-(0?[1-9]|[12][0-9]|3[01])
	              name: new_log_start_with_date
	``
9. In _/etc/datadog-agent/datadog.yaml_, ensure log collection is enable with the line `logs_enabled: true`.
10. Restart the Agent.
11. Verify that _postgresql_ shows in the Host Map.  
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/006_postgres_small.png)
12. Verify that the logs show in the Log Explorer.  
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/007_logs_small.png)

### Create a custom Agent check that submits a metric named my_metric with a random value between 0 and 1000.

1. Create a Python file _/etc/datadog-agent/check.d/my\_check.py_.
	* [my_check.py](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/python/my_check.py "my_check.py")
2. Create the corresponding configuration file _/etc/datadog-agent/conf.d/my\_check.yaml_.
	* [my_check.yaml](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/config/my_check.yaml "my_check.yaml")
3. Restart the Agent and check the status.  
	``
	vagrant@precise64:/vagrant$ sudo datadog-agent status
	Getting the status from the agent.
	.
	.
	.
    my_check (1.0.0)
    \----------------
      Instance ID: my_check:5bbfe9f3938f1c8d [OK]
      Total Runs: 17
      Metric Samples: Last Run: 1, Total: 17
      Events: Last Run: 0, Total: 0
      Service Checks: Last Run: 0, Total: 0
      Average Execution Time : 0s
	.
	.
	.
	``
4. Verify _my\_metric_ in the Metrics Explorer.  
	![](https://github.com/CodaAzzurra/hiring-engineers/blob/solutions-engineer/image/008_my_check_small.png)

### Change your check's collection interval so that it only submits the metric once every 45 seconds.

* Modify _/etc/datadog-agent/conf.d/my\_check.yaml_ to set the _min_collection_interval_ to 45.

### Bonus Question Can you change the collection interval without modifying the Python check file you created?

* Yes, see above. We can change the collection interval by modifying the _min_collection_interval_ in the Agent Check configuration file.

## Visualizing Data

### Utilize the Datadog API to create a Timeboard
Utilize the Datadog API to create a Timeboard that contains:

* Your custom metric scoped over your host.
* Any metric from the Integration on your Database with the anomaly function applied.
* Your custom metric with the rollup function applied to sum up all the points for the past hour into one bucket

