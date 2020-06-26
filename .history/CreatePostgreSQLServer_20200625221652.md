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

```
ssh root@<droplet IP address>
```

Add user and make user Superuser.  It is a good practice to avoid using the root user for installs or management. 

```
adduser <newuser>
usermod -aG sudo <newuser>
```

Next I need to pass the root user's ssh credentials onto the  \<newuser>

```
rsync --archive --chown=<newuser></newuser>:<newuser> ~/.ssh /home/<newuser>
```
Then log out of root user. Log back into server using \<newuser>. Be sure to try a `sudo` command. This is a good opportunity to do a software update on the droplet. 

```
ssh < newuser >@< droplet ip address >

#Software update
sudo apt-get update
```

## 2.3. Set Up PostgreSQL on Server






