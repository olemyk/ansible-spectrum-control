IBM Spectrum Control - Ansible Role
=========
[![GitHub Issues](https://img.shields.io/github/issues/olemyk/ansible-spectrum-control.svg)](https://github.com/olemyk/ansible-spectrum-control//issues) 
[![GitHub Stars](https://img.shields.io/github/stars/olemyk/ansible-spectrum-control.svg?label=github%20%E2%98%85)](https://github.com/olemyk/ansible-spectrum-control/) 
[![Role Downloads](https://img.shields.io/ansible/role/d/43987.svg)](https://galaxy.ansible.com/olemyk/ansible_spectrum_control/) 
[![License](https://img.shields.io/github/license/olemyk/ansible-spectrum-control.svg)](LICENSE)
[![Platform](http://img.shields.io/badge/platform-redhat-cc0000.svg?style=flat)](RedHat)
[![Platform](http://img.shields.io/badge/platform-centos-932279.svg?style=flat)](CentOS)


Ansible role for installing and configuring IBM Spectrum Control on Linux.



IBM® Spectrum Control™ provides monitoring, automation and analytics for multiple-vendor storage environments.
For more information and IBM Spectrum Control documentation, check out [KnowledgeCenter](https://www.ibm.com/support/knowledgecenter/en/SS5R93)


Particularly looking for [feedback](https://github.com/olemyk/ansible-spectrum-control/issues) and future [requirements](https://github.com/olemyk/ansible-spectrum-control/issues/new)!


Features
--------

- Install and configure IBM Spectrum Control on RedHat and Centos
- Installation option for Singel Server: Singel Typical and Singel Custom installation
- Upgrade of IBM Spectrum Control
- Integrated DB2 installer with DB2 Ansible Role.


##### The following installation methods are available:
- Install IBM Spectrum Control binary from URL or local


##### Future plans:
- Installing IBM Spectrum Control in a multiple-server environment.
- Cognos Analytics Installation for reporting.
- OS Tuning for DB2 and IBM Spectrum Control.
- User authentication to Active Directory and LDAP-



Requirements
------------

As there's no public repository available, you'll need to download IBM Spectrum Control from the IBM website.

Check the Hardware and Software requirements for installing IBM Spectrum Control. 
[IBM Spectrum Control support site](https://www.ibm.com/support/docview.wss?uid=ibm10719187#Serve)

Check your Version [Hardware](https://www.ibm.com/docs/en/spectrum-control/5.4.5?topic=installing-hardware-requirements)
and [Software](https://www.ibm.com/support/knowledgecenter/en/SS5R93_5.4.5/com.ibm.spectrum.sc.doc/fqz0_r_sw_requirements.html)

- Recommended Memory is 32 GB RAM, but at least 16 GB is recommended as minimum.
- Disk space is from 50-100Gb++
  - The amount needed depends on the following factors:                         
    - The number of resources that are monitored
    - The length of time data is kept
    - How frequently data is collected



Dependencies
------------
None - but can be combined with the Role DB2. 


Role Variables
--------------

Default variables are defined in defaults/main.yml. You'll also find detailed documentation in that file. Define your own host variables in your inventory to override the defaults.


##### Spectrum Control binary
This controls how to send the SC binary to the remote hosts.´

 ```yaml
sc_binary:
    url: A URL to download SC (don´t set this if you want to use a local copy)
    location: A path to save the remote file or to get the file if url wasn't defined 
    dest: Where the role should decompress SC on remote host.
    
sc_license_binary:
     url: "http://10.20.20.20/sc/SC534/IBM_SPM_CTL_ADV_V5.3.4_Lcns_UNIX.tar.gz"
     location: "/download/installer/sc_license.tar.gz"
     dest: "/download/installer/"
```

##### Default unarchive names
 - Default is 'sc_binary and sc_license'
 
 ```yaml
sc_creates: sc_binary
sc_license_creates: sc_license
```

##### Delete Downloaded Binary from IBM Spectrum Control Host after installation
 - Default is 'true'
 
 ```yaml
sc_binary_download_delete: true
 ```

##### Temporary location for Silent installation file.
 - Default is /tmp

```yaml
sc_properties_location: '/tmp'
```

#### Silent Install Properties file

##### Silent install Properties

- **db2_home_directory:** is home directory for your user:
- **license_accept:** If the LICENSE_ACCEPTED has a value other than 'true' the installation  will exit.
- **varSrvName:** Specify the hostname of this local server machine where SC software will be  installed. The hostname should be the fully qualified hostname, FQDN
- **varCommonUsrID:** Specify the common user ID. This user id will be used to install SC software. This user id needs to have administrative privileges for the server and the DB2 software.
- **varCommonUsrPW:** Specify the valid password for the common user ID

 ```yaml
# SC Install - Populate properties
sc_db2_home_directory: '/db2/db2inst1'
sc_properties:
  license_accept: 'true'
  varSrvName: 'lbs-sc.lbs.loc'
  varCommonUsrID: 'db2inst1'
  varCommonUsrPW: 'mypassword'
```

##### Default Silent install Properties

- **language:** Specify the preferred installation language
    - The supported values are:  cs, de, en, es, fr, it, ja, ko, pl, pt_BR, hu, ru, zh_CN, zh_TW
- **user_install_dir:** Specify the directory where the SC software will be installed. In case of upgrade and resuming failed install scenarios, specify the directory where SC software is installed
- **varTPCPortRangeSP:** Specify the range of the ports needed to install the SC software. SC needs 24 ports. 
   - The default port range is 9549 to 9572. SC uses these default
- **VarFullRollback:** Specify the type of rollback you would like the installer to perform in the event of a failure.  
    -   a) 0 = partial rollback(default value)  
    -   b) 1 = full rollback
- **varUseLicenseKeyOnImage:** Specify whether to use the license key file present on the installer image.
    - a) 0 = use license key present on the installer image (default value)
    - b) 1 = provide the location of the license key file
- **varLicenseKeyFile:** Specify the complete path for the license key file. if not using default.

```yaml
sc_properties_default:
  language: 'en'
  user_install_dir: '/opt/IBM/TPC'
  varTPCPortRangeSP: '9549'
  varFullRollback: '0'
  varUseLicenseKeyOnImage: '0'
  varLicenseKeyFile:
```

#### SC Database Advanced Customization
  Modify the silent variables below to further customize the SC Database component installation.

```yaml
sc_properties_custom:
  varDBAdmUsr:
  varDBAdmPW:
  varDBName: 'TPCDB'
  varDBPath:
  varDBLogPath:
```

#### SC Spectrum Control Upgrade
- To upgrade Existing IBM Spectrum Control. 
  - Modify the silent variables below to Upgrade.
  - Set the Variable SC_upgrade: to 'True'

```yaml
sc_upgrade: false
sc_properties_upgrade:
  license_accept: 'false'
  confirm_replication_uninstall: 'false'
  confirm_reports_uninstall: 'false'
  varAlertKeystorePW: 'default'
  varDeviceKeystorePW: 'default'
  varWebKeystorePW: 'default'
```

Example Playbook
----------------

Including an example of how to use the role.
         
### Example 1: Install SC without DB2 Role      

```yaml
- hosts: sc
  gather_facts: True
  roles:
     - olemyk.ansible_spectrum_control
  vars:
    ## Define the sc_binary and set up the URL to download.
    ## Variable 'location' define where should put SC on ansible machine
    ## Variable 'dest' define where should decompress SC on remote host
    ##
    sc_creates: sc
    sc_license_creates: sclicense
    sc_binary:
      url: "http://10.10.10.56/sc/SC534/SPM_CTL_SERVER_V5.3.4_LINUX.tar.gz"
      location: "/download/installer/sc.tar.gz"
      dest: "/download/installer"
    sc_license_binary:
      url: "http://10.10.10.56/sc/SC534/IBM_SPM_CTL_ADV_V5.3.4_Lcns_UNIX.tar.gz"
      location: "/download/installer/sc_license.tar.gz"
      dest: "/download/installer/"
    ########################################
    ### SC Install - Populate properties ###
    sc_db2_home_directory: '/db2/db2inst1'
    sc_properties:
      license_accept: 'true'
      varSrvName: 'lbs-sc.lbs.loc'
      varCommonUsrID: 'db2inst1'
      varCommonUsrPW: 'mypassword'
```

### Example 2: Install SC with IBM DB2 Role      

```yaml   
- hosts: sc
  gather_facts: True
  pre_tasks:
    - name: Create DB2 Directory
      file:
        path: /db2
        state: directory
        mode: 0755
    - name: Create Download Directory
      file:
        path: /download/installer
        state: directory
        mode: 0755
  roles:
     - olemyk.ansible_role_db2
     - olemyk.ansible_spectrum_control
  vars:
    ## Define the db2_binary and setup the URL to download.
    ## Variable location define where should put db2 on ansible machine
    ## Variable dest define where should decompress DB2 on remote host
    db2_binary:
      url: "http://10.10.10.56/sc/SC534/DB2S_11.5.4_MPML.tar.gz"
      location: "/download/installer/db2_11.5.4.tar.gz"
      dest: "/download/installer"
    db2_license_binary:
      url: "http://10.10.10.56/sc/SC534/DB2_DSE_Activation_11.5.zip"
      location: "/download/installer/db2_license.tar.gz"
      dest: "/download/installer"
    ##
    ## On most DB2 compressed binary the tar.gz creates a file named server, but if your compressed file doesn't,
    ## change this variable to the name of the folder that it creates.
    ## For the Spectrum Scale DB2 binary package it uses server_t from 5.4 it creates server_dec
    ## For the Spectrum Scale DB2 License package it uses awse_o frrom 5.4 it creates std_vpc
    ##
    db2_creates: 'server_dec'
    db2_license_creates: 'std_vpc'
    ##
    ## DB2 Instance Configure.
    ## Password is plaintext and then it Hashed with ansible. example is "mypassword"
    ##
    db2_instances:
      - instance: "DB2INST"
        name: "db2inst1"
        group_name: "db2iadm1"
        fenced_username: "db2fenc1"
        fenced_group_name: "db2fadm1"
        home_directory: "/db2/db2inst1"
        fenced_home_directory: "/db2/db2fenc1"
        password: "mypassword"
        fenced_password: "mypassword"
        uid: 11999
        gid: 11987
        fenced_uid: 11888
        fenced_gid: 11876
        options:
          autostart: "YES"
          start_during_install: "YES"
        dbm_params:
          intra_parallel: "YES"
          numdb: "15"
          SVCENAME: db2c_db2inst1
          db2fcmcomm: "TCPIP4"
    resp:
      prod: "DB2_SERVER_EDITION"
      file: "/opt/ibm/db2/V11.1"
      lic_agreement: "ACCEPT" # ACCEPT or DECLINE
      install_type: "TYPICAL" # TYPICAL, COMPACT, CUSTOM
      install_tsamp: "NO"
    ##
    ## IBM Spectrum Control Variables
    ## Define the sc_binary and setup the URL to download.
    ## Variable 'location' define where should put SC on ansible machine
    ## Variable 'dest' define where should decompress SC on remote host
    ##
    #sc_creates: sc
    #sc_license_creates: sclicense
    #sc_binary_download_delete: true
    sc_binary:
      url: "http://10.10.10.56/sc/SC534/SPM_CTL_SERVER_V5.3.4_LINUX.tar.gz"
      location: "/download/installer/sc.tar.gz"
      dest: "/download/installer"
    sc_license_binary:
      url: "http://10.10.10.56/sc/SC534/IBM_SPM_CTL_ADV_V5.3.4_Lcns_UNIX.tar.gz"
      location: "/download/installer/sc_license.tar.gz"
      dest: "/download/installer/"
    ########################################
    ### SC Install - Populate properties ###
    sc_db2_home_directory: '/db2/db2inst1'
    sc_properties:
      license_accept: 'true'
      varSrvName: 'lbs-sc.lbs.loc'
      varCommonUsrID: 'db2inst1'
      varCommonUsrPW: 'mypassword'
```


### Example 3: Upgrade IBM Spectrum Control

```yaml
- hosts: sc
  gather_facts: True
  roles:
     - olemyk.ansible_spectrum_control
  vars:
    ## Defines the sc_binary and set up the URL to download.
    ## Variable 'location' define where should put SC on ansible machine
    ## Variable 'dest' define where should decompress SC on remote host
    #sc_binary_download_delete: true
    #sc_creates: sc
    #sc_license_creates: sclicense
    sc_binary:
      url: "http://10.10.10.56/sc/SC545/SPM_CTL_SERVER_V5.4.5_LINUX.tar.gz"
      location: "/download/installer/sc.tar.gz"
      dest: "/download/installer"
    sc_license_binary:
      url: "http://10.10.10.56/sc/SC545/IBM_SPM_CTL_STD_V5.4.5_Lcns_UNIX.tar.gz"
      location: "/download/installer/sc_license.tar.gz"
      dest: "/download/installer/"
    ######################################
    ### IBM Spectrum Control Upgrade                    
    ### Can't be combined with install
    sc_upgrade: true
    sc_upgrade_version: 5.3.4
    sc_upgrade_user_install_dir: '/opt/IBM/TPC'
    sc_properties_upgrade:
      license_accept: 'true'
      confirm_replication_uninstall: 'false'
      confirm_reports_uninstall: 'false'
      varAlertKeystorePW: 'default'
      varDeviceKeystorePW: 'default'
      varWebKeystorePW: 'default'
```

#### Example 4: - Most rich example with all variables and IBM DB2 and Spectrum Control installer.
```yaml
- hosts: sc
  gather_facts: True
  pre_tasks:
    - name: Create DB2 Directory
      file:
        path: /db2
        state: directory
        mode: 0755
    - name: Create Download Directory
      file:
        path: /download/installer
        state: directory
        mode: 0755
  roles:
     - olemyk.ansible_role_db2
     - olemyk.ansible_spectrum_control
  vars:
    ##
    ## Defines the db2_binary and se tup the URL to download.
    ## Variable location define where should put db2 on ansible machine
    ## Variable dest define where should decompress DB2 on remote host
    ##
    db2_binary:
      url: "http://10.10.10.56/sc/SC534/DB2S_11.5.4_MPML.tar.gz"
      location: "/download/installer/db2_11.5.4.tar.gz"
      dest: "/download/installer"
    db2_license_binary:
      url: "http://10.10.10.56/sc/SC534/DB2_DSE_Activation_11.5.zip"
      location: "/download/installer/db2_license.tar.gz"
      dest: "/download/installer"
    ##
    ## On most DB2 compressed binary the tar.gz creates a file named server, but if your compressed file doesn't,
    ## change this variable to the name of the folder that it creates.
    ## For the Spectrum Scale DB2 binary package it uses server_t from 5.4 it creates server_dec
    ## For the Spectrum Scale DB2 License package it uses awse_o frrom 5.4 it creates std_vpc
    ##
    db2_creates: 'server_dec'
    db2_license_creates: 'std_vpc'
    ##
    ## DB2 Instance Configure.
    ## Password is plaintext and then it Hashed with ansible. example is "mypassword"
    ## 
    ##
    db2_instances:
      - instance: "DB2INST"
        name: "db2inst1"
        group_name: "db2iadm1"
        fenced_username: "db2fenc1"
        fenced_group_name: "db2fadm1"
        home_directory: "/db2/db2inst1"
        fenced_home_directory: "/db2/db2fenc1"
        password: "mypassword"
        fenced_password: "mypassword"
        uid: 11999
        gid: 11987
        fenced_uid: 11888
        fenced_gid: 11876
        options:
          autostart: "YES"
          start_during_install: "YES"
        dbm_params:
          intra_parallel: "YES"
          numdb: "15"
          SVCENAME: db2c_db2inst1
          db2fcmcomm: "TCPIP4"
    resp:
      prod: "DB2_SERVER_EDITION"
      file: "/opt/ibm/db2/V11.1"
      lic_agreement: "ACCEPT" # ACCEPT or DECLINE
      install_type: "TYPICAL" # TYPICAL, COMPACT, CUSTOM
      install_tsamp: "NO"
    ##
    ## IBM Spectrum Control Variables
    ##
    ## Define the sc_binary and set up the URL to download.
    ## Variable 'location' define where should put SC on ansible machine
    ## Variable 'dest' define where should decompress SC on remote host
    ##
    #sc_binary_download_delete: true
    sc_creates: sc
    sc_license_creates: sclicense
    sc_binary:
      url: "http://10.10.10.56/sc/SC545/SPM_CTL_SERVER_V5.4.5_LINUX.tar.gz"
      location: "/download/installer/sc.tar.gz"
      dest: "/download/installer"
    sc_license_binary:
      url: "http://10.10.10.56/sc/SC545/IBM_SPM_CTL_STD_V5.4.5_Lcns_UNIX.tar.gz"
      location: "/download/installer/sc_license.tar.gz"
      dest: "/download/installer/"
    ########################################
    ### SC Install - Populate properties ###
    sc_db2_home_directory: '/db2/db2inst1'
    sc_properties:
      license_accept: 'true'
      varSrvName: 'lbs-sc.lbs.loc'
      varCommonUsrID: 'db2inst1'
      varCommonUsrPW: 'mypassword'
    ######################################
    ### SC Upgrade                     ###
    ### Can't be combined with install ###
    sc_upgrade: false
    sc_upgrade_version: 5.3.4
    sc_upgrade_user_install_dir: '/opt/IBM/TPC'
    sc_properties_upgrade:
      license_accept: 'true'
      confirm_replication_uninstall: 'false'
      confirm_reports_uninstall: 'false'
      varAlertKeystorePW: 'default'
      varDeviceKeystorePW: 'default'
      varWebKeystorePW: 'default'
```


Tested on
---------

**Release: v0.1.1**
- DB2 Version V11.5
- IBM Spectrum Control 5.4.
- Redhat 8.5

**Release: v0.1.0**
- DB2 Version V11.1
- IBM Spectrum Control 5.3.1, 5.3.4, 5.3.5.1, 5.3.6
- Centos 7.6, 7.7 and 8.1 
- Redhat 7.6 and 7.7


Troubleshoot
------------


If error output when installing: check the lax*-out.txt" & lax*-err.txt files in /tmp on the SC host.

Check that the version in your download folder is correct

```shell
cat /download/installer/SC/version.txt
```

License
-------

Copyright 2021 Ole Kristian Myklebust, released under the [MIT license](LICENSE)