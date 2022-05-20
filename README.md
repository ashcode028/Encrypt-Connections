# Encrypt Connections

Normally NFSv4 does not use encrypted traffic.Here, we are attempting to encrypt NFSv4 traffic (Network File System) using OpenSSL.
Task1 : generate public and private keys and certificates using openssl
Use this in next task where we encrypt the traffic.

<aside>
💡 If two screenshots are present , left hand side is server and right hand side is client

</aside>

# Task 1

1. Generate an RSA key(private key) for the CA
    
    ![Screenshot from 2022-04-07 23-57-47.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-07_23-57-47.png)
    
    Check its contents using `openssl rsa -in private.org.key -noout -text`
    
    ![Screenshot from 2022-04-07 23-57-30.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-07_23-57-30.png)
    
2. Extract the rsa public key from the private key 
    
    ![Screenshot from 2022-04-08 00-18-28.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_00-18-28.png)
    
3. Create CA certificate
    
    ![Screenshot from 2022-04-08 01-30-30.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_01-30-30.png)
    
4. Server key and certificate
    
    ![Screenshot from 2022-04-08 00-44-28.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_00-44-28.png)
    
    ![Screenshot from 2022-04-08 01-34-20.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_01-34-20.png)
    
5. Sign the server certifcate using CA
    
    ![Screenshot from 2022-04-08 01-35-28.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_01-35-28.png)
    
    See the contents of the certificates using `openssl -x509 -in Myserver.pem -noout -text`
    
    ![Screenshot from 2022-04-08 11-58-10.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_11-58-10.png)
    
6. Client key and certificate
    
    ![Screenshot from 2022-04-08 12-12-35.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_12-12-35.png)
    
7. Sign the client certificate using CA
    
    ![Screenshot from 2022-04-08 12-31-20.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_12-31-20.png)
    
8. Launch server and client using *s_server and s_client*  as mentioned below
`openssl s_server -key Server/server.key -cert Server/Myserver.pem -CAfile ca.pem -accept 12345 
openssl s_client -connect 172.16.12.136:12345 -CAfile ca.pem` 
    
    ![Screenshot from 2022-04-08 12-46-22.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_12-46-22.png)
    
    The server  listens on port 12345 and presents CA signed certificate to any incoming connection.
    
    In the above screenshot, we can clearly see that TLS connection has been established and server is verified as **return code is 0 (OK)**. Thus, client successfully authenticates the server.
    
    The client tries to connect to the server using openssl s_client command by
    specifing certificate of the CA it trusts as shown below
    
    ![Screenshot from 2022-04-08 13-24-58.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_13-24-58.png)
    
9. Both authenticate each other, by presenting there certificates using commands.
`openssl s_server -key Server/server.key -cert Server/Myserver.pem -CAfile ca.pem -accept 12345 -verify 1 -debug > debug_output2 
openssl s_client -connect 172.16.12.136:12345 -CAfile ca.pem -key Client/client.org.key -cert Client/Myclient.pem  > client_output1` 
Arguments with its meaning 
**-key : provide private key
-cert : provide CA signed certificate
-CAfile : provide certificate for the CA
-accept : accept connections on the given port
-connect : connect to the ip and port mentioned 
-verify : enable client verification 
-debug : include debug messages** 
    
    ![Screenshot from 2022-04-08 13-36-15.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_13-36-15.png)
    
    Below is the contents where client has server certificate and server has clients certificate.
    Client verifies the certificate of the server .We can also see that the server also verifies client by giving a list of acceptable client certificate CA names to client.
    
    ![Screenshot from 2022-04-08 13-38-31.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_13-38-31.png)
    
    Below is the screenshot showing the established TLS connection after authenticating each other after mutual Verification is done, **return code is 0 (OK)**.
    
    ![Screenshot from 2022-04-08 13-39-16.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_13-39-16.png)
    
10. -www option to emulate a webserver
Export your client certificate using pkcs command.
    
    ![Screenshot from 2022-04-08 13-58-44.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_13-58-44.png)
    
    Import your client certificate here in certificate manager
    
    ![Screenshot from 2022-04-08 15-11-03.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_15-11-03.png)
    
    Add CA certificate to trusted authority section of  your browser
    
    ![Screenshot from 2022-04-08 14-24-25.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_14-24-25.png)
    
    ![Screenshot from 2022-04-08 15-11-46.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_15-11-46.png)
    
    When we try to add it in the security exception it shows that the certificate is valid not required for adding the exception.
    Server is valid and the client is also verified .
    
    ![Screenshot from 2022-04-08 15-02-45.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_15-02-45.png)
    
    Once we hit the browser ,SSL connection is established
    
    ![Screenshot from 2022-04-08 15-03-08.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_15-03-08.png)
    

### References:

[https://gist.github.com/Soarez/9688998](https://gist.github.com/Soarez/9688998)

[https://linuxconfig.org/testing-https-client-using-openssl-to-simulate-a-server](https://linuxconfig.org/testing-https-client-using-openssl-to-simulate-a-server)

Manpages of openssl

# Task 2

<aside>
💡 SERVER IP : 172.16.12.137/24, username: ashita
CLIENT IP : 172.16.12.138/24, username: ashita2

</aside>

1. Install nfs-kernel-server on server vm and nfs-common on client VM.
    
    ![Screenshot from 2022-04-08 19-55-12.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_19-55-12.png)
    
2. Steps to export the file 
    1. Create a folder to share in your home directory and in nfs root directory i.e srv/nfs4, then
    2. Bind the home directory folder to the folder in the nfs root directory
        
        ![Screenshot from 2022-04-08 21-06-56.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-06-56.png)
        
    3. To make the bind permant add in fstab file as shown below
    `/root/music /srv/nfs4/music bind 0 0`
    4.  Add the entries in /etc/exports file to export the mounted nfs directories
        
        ![Screenshot from 2022-04-08 21-25-33.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-25-33.png)
        
    5. Then run `exportfs -ra`  to export, and `exportfs -v` to see active exports.Add a firewall rule to allow all incoming connections from client ip for nfs
        
        ![Screenshot from 2022-04-08 21-08-40.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-08-40.png)
        
    6. Now check ufw staus
        
        ![Screenshot from 2022-04-08 21-08-57.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-08-57.png)
        
3. Steps to create the file in the exported directory
    1. mkdir your local directory and mount the nfs using `sudo mount -t nfs -o vers=4 172.16.12.137:/myfolder /myfolder`
        
        ![Screenshot from 2022-04-08 21-12-07.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-12-07.png)
        
    2. Once we we complete the mount , we can check it using `df -h`  to see mounted disks, the last entry is your mounted shared folder.
        
        ![Screenshot from 2022-04-08 21-12-26.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-12-26.png)
        
    3. Once we saw the entry we can add the entry in fstab to make the changes permanant, here the IP address added would be of the server.
        
        ![Screenshot from 2022-04-08 21-14-07.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-14-07.png)
        
    4. Now try to create a file in that folder, it would show permission denied, check the permissions of the folder and its contents using `ls -la` we can see clearly that user ashita2 has the permission to create not even root(i.e even sudo would fail as shown).So once we try to create the file using the `-u`  where we specify the require user 
        
        ![Screenshot from 2022-04-08 21-17-04.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-17-04.png)
        
        Server side contents of myfolder, clearly shows its working
        
        ![Screenshot from 2022-04-08 21-43-11.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-43-11.png)
        
4. Steps to create stunnel4
    1. Install stunnel4 on both client and server
        
        ![Screenshot from 2022-04-08 21-50-33.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_21-50-33.png)
        
    2. Enable stunnel automatic startup by configuring the file  `sudo nano /etc/default/stunnel4`
        
        ![Screenshot from 2022-04-08 22-01-08.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-01-08.png)
        
5. Server side 
    1. Create a Self-Signed SSL Certificate and Key for the server
        
        ![Screenshot from 2022-04-08 22-12-55.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-12-55.png)
        
    2. The key and certificate are directly generated in */etc/stunnel* folder as shown above. Once the server key is created change its permission using `chmod` for better security.
    3. Create server stunnel configuration file
        
        ![Screenshot from 2022-04-08 22-21-10.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-21-10.png)
        
        ![Screenshot from 2022-04-08 22-19-52.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-19-52.png)
        
    4. Once the config file is created , restart the stunnel service and check is it listening on the mentioned port (using `netstat -plunt)`
        
        We can see clearly , stunnel is listening on port 6379
        
        ![Screenshot from 2022-04-08 22-27-32.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-27-32.png)
        
    5. Change the firewall rules to accept connections from 6379, we can also disable firewall if required
        
        ![Screenshot from 2022-04-08 22-38-32.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-38-32.png)
        
    6. Send server certificate to client and copy it to the required folder(/etc/stunnel) as shown below 
        
        ![Screenshot from 2022-04-08 22-48-13.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_22-48-13.png)
        
6. Client side 
    1. Create client stunnel configuration file 
        
        ![Screenshot from 2022-04-08 23-58-01.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_23-58-01.png)
        
    2. restart stunnel, and check if its listening on the port you wanted 
        
        ![Screenshot from 2022-04-09 00-12-47.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-09_00-12-47.png)
        
7. Now again on server side , Add an entry to /etc/exports so that it can we export to client
    
    ![Screenshot from 2022-04-08 23-01-18.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_23-01-18.png)
    
    ![Screenshot from 2022-04-08 23-16-15.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_23-16-15.png)
    
8. From client side ,Mount the folder again, now create any file , after mounting directly without any arguments. 
capture the traffic from server side using tcpdump 
    
    ![Screenshot from 2022-04-08 23-52-33.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_23-52-33.png)
    
9. We can see the encrypted traffic after creating the tunnel

![Screenshot from 2022-04-08 23-56-20.png](Encrypt%20Connections%2066b3854dffe442d4b4c0be122b0ecbab/Screenshot_from_2022-04-08_23-56-20.png)

### References

[https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-18-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-18-04)

[https://linuxize.com/post/how-to-install-and-configure-an-nfs-server-on-ubuntu-18-04/](https://linuxize.com/post/how-to-install-and-configure-an-nfs-server-on-ubuntu-18-04/)

[https://www.digitalocean.com/community/tutorials/how-to-encrypt-traffic-to-redis-with-stunnel-on-ubuntu-16-04#what-is-stunnel](https://www.digitalocean.com/community/tutorials/how-to-encrypt-traffic-to-redis-with-stunnel-on-ubuntu-16-04#what-is-stunnel)

[https://www.digitalocean.com/community/tutorials/how-to-set-up-an-ssl-tunnel-using-stunnel-on-ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-ssl-tunnel-using-stunnel-on-ubuntu)

[https://www.linuxjournal.com/content/encrypting-nfsv4-stunnel-tls](https://www.linuxjournal.com/content/encrypting-nfsv4-stunnel-tls)
