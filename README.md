# Udacity Linux Server Configuration
A Project to deploy a flask app on Linux Server. I have used Ubuntu and DigitalOcean droplet.

## IP Address
#### Server IP Address : 139.59.28.100
#### Application URL : 139.59.28.100.xip.io
#### SSH Server Access Port : 2200
#### SSH login username : grader

# Steps to configure server
### 1. Create RSA Key Pair
You have to create the RSA Key Pair on your local machine. This key pair will be used to authenticate yourself while securely logging in to the server via SSH. The private key will be kept with you in your local machine, and the public key will be stored in the server.

To generate the RSA Key Pair, run this command
```console
$ ssh-keygen
```
It will ask for a passphrase, you may either leave it empty or enter some passphrase. A passphrase adds an additional layer of security which prevents unauthorized users from logging in.

The whole process would look like this:

```
thecoder@thelappy:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/thecoder/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/thecoder/.ssh/id_rsa.
Your public key has been saved in /home/thecoder/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:HSP7o6B1Yk7wO51mcMdDpZnHvw46RvvjqS5VObjLmQU thecoder@thelappy
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|            .    |
|        . oB .   |
|         +Eo*    |
|    .   So.= o   |
|     o. ..B . .  |
|      B+.*oB.  . |
|     *.=*.Oo.o.  |
|    . o+.++=+o.  |
+----[SHA256]-----+
```
Now, you have a public and private key which you can use to authenticate. The public key is called id_rsa.pub and the respective private key is called id_rsa. The key pair is stored inside the ~/.ssh/ directory.

### 2. Set DigitalOcean Droplet

1. Log in on [DigtalOcean](https://cloud.digitalocean.com/login). If you don't have an account, then create one.
2. Navigate to Dashboard, click on **Create Droplet**
3. Choose Ubuntu 18.04 x64 image from the list of given images.
4. Choose the size, I have chosen the **1GB/1 vCPU/25GB** configuration for this project.
5. In the section **Add Your SSH Keys**, paste the content of your public key, 'id_rsa.pub':
   This step will automatically create the file ~/.ssh/authorized_keys with appropriate permissions and add your public key      to it. It would also add the following rule in the /etc/ssh/sshd_config file automatically:
```
PasswordAuthentication no
```
 This rule essentially disables password authentication on the `root` user, and rather enforces SSH logins only.
 

