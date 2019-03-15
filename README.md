Ansible Role: Installs and customizes ISPConfig 3 on Ubuntu Bionic
=========

The inoxio.ispconfig3 role will help you to install ISPConfig on your Ubuntu 18.04 machine.
It follows the instructions of the howtoforge.com [tutorial](https://www.howtoforge.com/tutorial/perfect-server-ubuntu-18.04-with-apache-php-myqsl-pureftpd-bind-postfix-doveot-and-ispconfig/)
and allows customizations which will be explained in the following segments.
It also implements Molecule as a test environment and contains a few tips if you want use Molecule for yourself.

Requirements
------------

- Machine with Ubuntu 18.04


Role Variables
--------------

Mail, PHPMyAdmin and Roundcube will set mostly the admin passwords of the named application.
Mail itself will also need an email with which it can send mails to.

ISPConfig is here the most important part it contains a lot of different variables. 
Most of them can be translated into the autoinstall.php from ISPConfig.
It is also important to notice that the mysql_root_password will be used for the setup 
of the MariaDB module.  
This happens to the ssl_cert part too, this will be used as the SSL cert is generated.
 
* `mail`:
    This will set all needed variables to setup a new mailing list. It will
    automatically create a mailing list with the given Parameters.
    * `admin_email`: 
        Mail which will send/receive updates from Mailman.
    * `admin_password`: 
        Password for Mailman admin.
    * `base_domain`: 
        Base domain for Mailman.
    * `mailing_list_name`:
        Sets the mailman List. It is recommended to set it as Mailman (see example playbook).
        * Default: `Mailman`
* `phpmyadmin`: 
    * `admin_password`: Password for Admin Login in PHPMyadmin. 
* `roundcube`: 
    * `admin_password`: Password for Admin Login in Roundcube. 
* `ISPConfig`: This contains the most configurations for your ISPConfig setup. 
Please have a look at the example provided by ISPConfig itself. 
You can find it [here](https://git.ISPConfig.org/ISPConfig/ISPConfig3/blob/master/docs/autoinstall_samples/autoinstall.ini.sample).
    * `language`: Set the language your ISPConfig should have.
        * Default: `en`
    * `install_mode`: Set the mode you want to have if you just want to install it regularly it is recommended to use the default.
        * Default: `standard`
    * `hostname`: This is used for your FQDN where you can set your sitename.
    * `mysql_hostname`: Location where the MySQL Database is running, if you just use this role the default is sufficient.
        * Default: `localhost`
    * `mysql_root_user`: Name of the root user. In most cases this will be just 'root'. If you customize your
    ISPConfig set it as you wish. It will also set the name of the root user in the MariaDB Setup. 
        * Default: `root`
    * `mysql_root_password`: Password of the root user, please choose a secure one. Even if your database isn't exposed.
    * `mysql_database`: Database which will be initialized in MariaDB setup and used from ISPConfig. 
        * Default: `dbISPConfig`
    * `mysql_port`: Port which is used by MariaDB, it is also used for the database initialization.  
        * Default: `3306`
    * `mysql_charset`: Charset of the database.  
        * Default: `utf8`
    * `http_server`: Sets the HTTP server which is running behind the ISPConfig site.
        * Default: `apache`
    * `ISPConfig_port`: Port for the Website to use.
        * Default: `8080`
    * `ISPConfig_use_ssl`:  Use SSL to connect to ISPConfig.
        * Default: `y`
    * `ISPConfig_admin_password`: Password for the ISPConfig Admin Login.
    * `ssl_cert_country`: Your country. 
    * `ssl_cert_state`: Your state.
    * `ssl_cert_locality`: Your city.
    * `ssl_cert_organisation`: Your company.
    * `ssl_cert_organisation_unit`: Your department.
    * `ssl_cert_common_name`: FQDN of your site.
    * `mysql_ISPConfig_user`: User which will be created inside the MySQL database.
        * Default: `ISPConfig`
    * `mysql_ISPConfig_password`: Login for MySQL.
        
    * `join_multiserver_setup`: Joins multiple servers. 
        * Default: `n`
    * `mysql_master_hostname`: Location of the master MySQL database, for most cases 'localhost' is sufficient.
        * Default: `localhost`
    * `mysql_master_root_password`: Password for the root user of the master data. 
    * `configure_mail`: Mailman setup for ISPConfig.
        * Default: `y`
    * `configure_jailkit`: Jailkit setup for ISPConfig.
        * Default: `y`
    * `configure_ftp`: PureFTP setup for ISPConfig.
        * Default: `y`
    * `configure_dns`: DNS setup for ISPConfig.
        * Default: `y`
    * `configure_nginx`: NGINX setup for ISPConfig.
        * Default: `n`
    * `configure_apache`: Apache setup for ISPConfig.
        * Default: `y`
    * `configure_firewall`: Firewall setup for ISPConfig.
        * Default: `y`
    * `install_ISPConfig_web_interface`: Webinterface setup for ISPConfig.
        * Default: `y`
    * `do_backup`: Backup setup for ISPConfig.
        * Default: `yes`
    * `mysql_master_database`: Database which contains the information for ISPConfig.
        * Default: `dbISPConfig`
    * `reconfigure_permissions_in_master_database`: Sets the permissions needed for ISPConfig in the database. 
        * Default: `no`
    * `reconfigure_services`: Reconfigures all services.
        * Default: `yes`
    * `create_new_ISPConfig_ssl_cert`: Creates new certificate (will not be needed). 
        * Default: `no`
    * `reconfigure_crontab`: Configures cronjob.
        * Default: `yes`



Dependencies
------------

This role has no external dependencies, everything needed is inside this role.

Start and Test
--------------

Everything here is taken from https://Molecule.readthedocs.io/en/latest/. It's convenient to have everything in 
one place.

This will help you to run Ansible roles on a real virtual machine without caring about the state of the VM.  

To start the role and run the test environment you will require Molecule. 
The following will explain how to setup everything and run the tests. 

1. Install virtualenv
    * Install virtualenv `sudo apt install virtualenv`
    * Create virtualenv inside your folder `virtualenv --no-site-packages .venv ` 
    This will contain all installed packages. This is the cleanest way to prevent your system from package cluttering.
2. Start virtualenv 
    * You will run this command every time you restart your console, or enter a new session.
    * `source  .venv/bin/activate`
3. Install the Molecule package
    * `pip3 install Molecule ansible`
    * You will need to install the Python API for your driver which you will use, in the case of this role it will be Vagrant.
    * Run this command if you see the `(.venv)`  at the beginning of your console line.
    * `pip install python-vagrant`
    
These are the requirements you have to fulfill before starting Molecule. In the following it will be explained 
how you can setup roles for testing. 

#####Run Test

Running tests is fairly easy, just go into your venv as seen in step 2 above and run: 

`Molecule test`

This will automatically setup your driver (Docker or Vagrant in most cases) and run different test scenarios 
such as idempotence tests or your own written tests which can test all kind of things.


#####New Role
`Molecule init role -r new-role -d vagrant` 

This will initialize a new Vagrant role with the name `new-role` and 
it will contain the standard setup for Vagrant. As alternative you can set your driver to Docker or other 
supported methods of virtualization. 

#####Upgrade Molecule
Note: Molecule is relatively new so there will be updates, that is why it is recommended to run this from time to time.

`pip install --upgrade Molecule`

#####Example Molecule and Playbook

Here you are going to see an example how the `Molecule.yml` and the `Playbook.yml` is structured 
for this test scenario.

`Molecule.yml`

    ---
    dependency:
      name: galaxy
    driver:
      name: vagrant
      provider:
        name: virtualbox
    lint:
      name: yamllint
    platforms:
      - name: instance
        box: ubuntu/bionic64
        instance_raw_config_args:
          - "vm.hostname = 'web.my.net'"
        memory: 4096
    provisioner:
      name: ansible
      lint:
        name: ansible-lint
        enabled: false
    scenario:
      name: default
      test_sequence:
        # - lint
        - destroy
        # - dependency
        - syntax
        - create
        - prepare
        - converge
        - idempotence
        # - side_effect
        - verify
        - destroy
    verifier:
      name: testinfra
      lint:
        name: flake8
        
 Here you can see the used `Molecule.yml` for this project you can see the used driver
 and its settings, such as memory. Important Note: It was required for 
 a task in the role to set the hostname so you can see that it is set with the help
 of `instance_raw_config_args`. 
 You can use different boxes directly from Vagrant or load them from a link. 
 For this type of change please consult the documentation of Molecule. 
 
 
 If you take a look at the `Playbook.yml` you will see it is similar to the normal
 role call but the name. See the full example below in the example playbook.
 
 `Playbook.yml`
    
    ---
    - name: Converge
      hosts: all
      roles:
         - role: inoxio.ISPConfig3
             ***
             ***

Example Playbook
----------------
This shows an example how you could configure your playbook.

        - role: inoxio.ISPConfig3
          mail:
            admin_email: email@your-company.com
            admin_password: password123
            base_domain: your-company.com
          phpmyadmin:
            admin_password: password123
          roundcube:
            admin_password: password123
          ISPConfig:
            hostname: ispconfig.your-company.com
            mysql_root_user: root
            mysql_root_password: secret
            ISPConfig_admin_password: secret
            mysql_ISPConfig_user: ISPConfig
            mysql_ISPConfig_password: ISPConfig
            mysql_master_root_password: root
            ssl_cert_country: DE
            ssl_cert_state: Sunny State
            ssl_cert_locality: Hill Valley
            ssl_cert_organisation: Your Conpany Inc
            ssl_cert_organisation_unit: Software
            ssl_cert_common_name: ispconfig.your-company.com
             
     
Everything else mentioned in role variables can be found in the defaults/main.yml.  
All settings for the ISPConfig role are taken from the config file for the Apache2 setup.
See [this link](https://git.ISPConfig.org/ISPConfig/ISPConfig3/blob/master/docs/autoinstall_samples/autoinstall.ini.sample) 
for more information. 
You can find example settings under autoinstall.php for ISPConfig.


Author Information
------------------

This section will contain a conclusion about Molecule because this was the first setup of it. 

The first run with Molecule was with Docker as the driver. With this used method there were roadblocks really fast. 
Because of the nature of Docker and the complexity of this role. 
This started with services which aren't loaded inside a container and went on to PID which are different. 
So we had come to the conclusion that it isn't pleasant to adapt your code to your testing framework. 

This left the choice of using Vagrant as the driver. This will help us with the problem of no full existing
virtual environment and running everything smoothly. 

This setup went without any major problems and tested all the wanted keypoints. 

If you want so include Molecule in your role please follow this little list: 
* Make sure you start with a blank slate and create your role in Molecule so you can test it every now and then
* If you want to use Docker make sure your role isn't too complex and you don't have to adapt your role to the Docker
