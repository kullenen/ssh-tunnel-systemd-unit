# How to create systemd service for ssh tunneling

## Requirements

**(on local machine)**

		$ sudo apt install autossh

## Example

For example you want to get access from remote server with ip = a.b.c.d to your home computer. Assume you have nginx server on remote computer and fastcgi listening port 9001 on local computer. And suppose nginx can forward requests on port 9002.

### Prepare ssh keys

**(on local machine)**

1. Create the RSA Key Pair

    If there is no ssh public key in ~ / .ssh / id_rsa, you need to create it:

		$ ssh-keygen -t rsa

2. Copy key to remote machine

		$ ssh-copy-id myuser@a.b.c.d

### Create unit file

**(on local machine)**

Create file **my-fastcgi-tunnel.service** with contents:

        [Unit]
        Description=My FastCGI tunnel
        After=network.target
        
        [Service]
        Environment="AUTOSSH_GATETIME=0"
        ExecStart=/usr/bin/autossh -N -R 9002:localhost:9001 a.b.c.d
        User=myuser
        
        [Install]
        WantedBy=multi-user.target

### Setup service

**(on local machine)**

1. Put **my-fastcgi-tunnel.service** into directory /etc/systemd/system/ :

		$ mv my-fastcgi-tunnel.service /etc/systemd/system/

2. Register service:

		$ systemctl daemon-reload

3. Enable service:

		$ systemctl enable my-fastcgi-tunnel.service


4. Run service:

		$ systemctl start my-fastcgi-tunnel.service
