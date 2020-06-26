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
```psql
postgres=# \q
```



