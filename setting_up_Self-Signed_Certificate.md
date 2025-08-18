## Setup Self-Signed Certificate

On this demo, we will be craeting a self-signed certificate on our ```nginx``` that is installed on on our ```ubuntu``` machine.
<br>

First, Let's create ad directory on for our certificate under the ```/etc/nginx``` and we will name it as ```ssl```.
```
mkdir /etc/nginx/ssl
```
<br>

Second, Let's create a file for our SSL called ```ssl_data_info.txt```. on our ```ssl``` directory.
```
touch /etc/ngins/ssl/ssl_data_info.txt
```
<br>

Third, copt and paste the following on the ```ssl_data_info.txt```.
```
[req]
default_bits       = 2048
prompt      = no
default_keyfile    = localhost.key
distinguished_name = dn
req_extensions     = req_ext
x509_extensions    = v3_ca

[ dn ]
C = PH
ST = NCR
L = Manila
O = localhost
OU = Development
CN = localhost

[req_ext]
subjectAltName = @alt_names

[v3_ca]
subjectAltName = @alt_names

[alt_names]
DNS.1   = localhost
DNS.2   = 127.0.0.1
```
<br>

## OpenSSL

By default, ```OpenSSL``` is installed on ```ubuntu```. We can verify that using the command:
```
openssl version
```
<br>

If not yet installed, we can install it using the following commands:
```
apt install openssl -y
```
<br>

Once we verify that ```OpenSSL``` is installed on our machine, Let's execute this command:
```
openssl req -x509 -nodes -days 3652 -newkey rsa:2048 -keyout localhost.key -out localhost.crt -config ssl_data_info.txt
```
<br>

*Explaination:*
<br>

| Commands |  Description                       |
| :-------- | :-------------------------------- |
| `openssl req`      | 	Starts a certificate request (CSR) process.. |
| `-x509`      | 	Tells OpenSSL to generate a self-signed certificate instead of a certificate signing request. |
| `-nodes`      | Skips encryption of the private key (no passphrase). Useful for automated setups. |
| `-days 3652`      | 	Sets the certificate validity to 3652 days (~10 years). |
| `-newkey rsa:2048`      | 	Generates a new RSA key of 2048 bits. |
| `-keyout localhost.key`      | 	Specifies the output file for the private key. |
| `-out localhost.crt`      | 	Specifies the output file for the certificate. |
| `-config <config_file>`      | 	Uses a configuration file to provide certificate details in this case, is the ```ssl_data_info.txt``` |
<br>

Once the generated is finished, we will then need to navigate and edit the ```/etc/nginx/sites-available/default``` file to use SSL and add the following:
```
 listen 443 ssl http2;
        listen [::]:443 ssl http2;

        ssl_certificate /etc/nginx/ssl/localhost.crt;
        ssl_certificate_key /etc/nginx/ssl/localhost.key;
        
        ssl_protocols TLSv1.2 TLSv1.1 TLSv1;
```
<br>

To verify that that there is no errors when adding the lines, we can use the command:
```
nginx -t
```
<br>

restart our ```nginx```.
```
systemctl restart nginx
```
<br>

We can use our browser to verify or use the ```curl``` command to check.
```
curl -Ik https://locathost
```
<br>

You should be able to get a similar output:
<br>
<img width="356" height="166" alt="image" src="https://github.com/user-attachments/assets/836aebf8-b2fa-4f48-a3db-8146aacd5c57" />
<br>







