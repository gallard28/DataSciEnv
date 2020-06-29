# Create a PostgreSQL Server on Digital Ocean 

Author: Grant Allard  
Contact: grant@grantallard.com  


## 1.1. Purpose

This markdown file will explain how to configure a DigitalOcean Droplet to work as a PostgreSQL server. I wrote this so (1) I can remember how to do this and (2) to help others who may be interested. There are several sources on which I drew, but no single source helped me solve all of the challenges into which I ran. 

I chose to use a DigitalOcean Droplet rather than a managed database because it provides me more control over the organization of my server. For example, as of 6/25/2020,  DigitalOcean's managed RDBs do not allow users' to have **superuser** privileges. **Superuser** privileges are important for designing and using schema and for making unique configurations for connecting droplets to other devices.

This approach, depending on use-case and feature selection, can be more cost-effective than using a managed database. 

I made the decision to use DigitalOcean because their pricing is more attractive for a small-scale use-case such as an individual researcher (like myself). They also have a large support community that writes to an audience that is technically-savvy but not expert in cloud computing or networking. Their business model is to offer Infrastructure as a Service (IaaS), although they are beginning to move into the Platform as a Service (PaaS) through some of their cloud offerings. 

Before you begin, I highly recommend you to check out [DigitalOcean] <https://www.digitalocean.com/>

#### 1.1.0.1. Note on Conventions: 

**I will notate objects/places that the user needs to change by placing text between \<>. Be sure to remove both carrots**

## 2.1. Step 1: Create droplet

The first step is to create a droplet on DigitalOcean. I used the Basic Starter package at $5/mo. I did not add a storage volume because it is extra expense for resources that I am not yet able to use. However, I chose to save money by adding this resource later if I need it. 



## 2.2. Step 2: Set Up Unbuntu Serverfe

1) I ssh from my local computer into my new droplet.

```zsh
# ssh into the droplet
ssh root@<droplet IP address>
```

Add user and make user Superuser.  It is a good practice to avoid using the root user for installs or management. 

```zsh
#Add User
adduser <newuser>
#Make user superuser 
usermod -aG sudo <newuser>
```

Next I need to pass the root user's ssh credentials onto the  \<newuser>

```zsh 
#Use rsync, I omitted <> from newuser for maximum clarity. 
rsync --archive --chown=newuser:newuser ~/.ssh /home/newuser
```

Then log out of root user. 

```zsh
logout
```

Log back into server using \<newuser>. Be sure to try a `sudo` command. This is a good opportunity to do a software update on the droplet. 

```zsh
#Log in with new user
ssh < newuser >@< droplet ip address >

#Software update
sudo apt-get update
```

## 2.3. Set Up PostgreSQL on Server

Use the following code to install PostgreSQL.


```zsh 
#Create the file repository configuration: 

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# Create the file repository configuration:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# Update the package lists:
sudo apt-get update

# Install the latest version of PostgreSQL.
sudo apt-get install postgresql
```

Now we need to log into PostgreSQL to assure it is working. 

```zsh
# In Ubuntu, switch to postgres account. 
sudo -i -u postgres

#Open psql 
psql
```

Logout of `psql`. 

```psql
postgres=# \q
```

Now I need to create a user. I will use this user in the future. 
```zsh
#Create User from postgres account on Ubuntu
postgres@ubuntu-s-1vcpu-1gb-nyc1-01:~$ createuser --interactive newuser

#You can also use the following command without the interactive interace: 
createuser newuser -d -l  -P 
-r -s --replication

#If necessary, add other privileges for user such as CreateDB
ALTER USER newuser WITH createdb;
```
While it is not always necessary, I create a test db with my `newuser`. Using a testdb allows me to assure that I don't mess anything up when logging in for the first time.

```zsh
#Create Database, assign to owner
CREATE DATABASE testdb OWNER newuser;

```

Next, I configure the PostgreSQL DB for outside connections. This part of the process involves:

1) Adjusting the firewall
2) Adding 

```zsh
# Configure the firewall
sudo ufw status
```
If the firewall is off, then turn it on by doing the following. I recommend allowing OpenSSH, allow from my public IP address to any port, and allow from droplet's public IP address to any port. 

```zsh
#In a new terminal tab, get IP address.
curl ifconfig.me
```
 
In my ubuntu terminal window: 
```zsh
#OpenSSH
sudo ufw allow OpenSSH

# Local Computer's IP Address 
sudo ufw allow from public_ip_address to any port 5432

#Repeat step for droplet IP address
sudo ufw allow from droplet_public_ip_address to any port 5432

#Enable firewall
sudo ufw enable
````

** Note: One security strategy is to change the port for the PostgreSQL server from 5432 to another port. **

**ANOTHER NOTE:** One other note is that as I change IP addresses, if I want to configure my server, I may have to add IP addresses to the firewall by SSHing in. 

Next, I will configure the PostgreSQL files. 

```zsh
# Go to postgreSQL directory - replace 12 with whatever version of PostgreSQL I installed.

cd /etc/postgresql/12/main
```

I will edit the `pg_hba.conf` file and map the IP addresses I added in the firewall to the databses I want them to access. 

You can use `nano` or `vi` as text editors for this step. You need to add the non-commented code for each db and IP address combo.  If you are using `vi`, then insert using `i`, escape insert usinng the `esc`-key, and save/quit using `:wq`. 

```zsh
# Put your actual configuration here
# ----------------------------------
host testdb  newuser public_ip_address/32 md5
# If you want to allow non-local connections, you need to add more
# "host" records.  In that case you will also need to make PostgreSQL
# listen on a non-local interface via the listen_addresses
# configuration parameter, or via the -i or -h command line switches.
```

99.17.86.157Next, I need to adjust the `postgresql.conf` file. I need to add the following. 

Open file using `vi` or `nano`. 
```zsh
sudo vi postgresql.conf
```

Next, adjust the `listen_addresses` settings:

** NOTE:** You will need the __server__ ipAddress rather than the droplet or local computer. 

```zsh
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

#listen_addresses = 'localhost' # what IP address(es) to listen on;
listen_addresses = 'localhost, postgresql-server-ip-address'
            # comma-separated list of addresses;
```

Then I restart PostgreSQL.

```zsh 
sudo systemctl restart postgresql
```
Now I can connect to the db using PgAdmin4 on my local computer and using Rstudio on my droplet. 










