Open the POM file of your Maven project and add the following details:
1.	There is no available working Maven plugin for Tomcat 9 so we need to use the latest stable version, which is tomcat7-maven-plugin. Add the following Maven plugin details for Tomcat 7 deployment under the <plugins>section of the <build>:
<plugin> 
  <groupId>org.apache.tomcat.maven</groupId> 
  <artifactId>tomcat7-maven-plugin</artifactId> 
  <version>2.2</version> 
  <configuration> 
    <url>https://spring5server:8443/manager/text</url> 
    <path>/ch01</path> 
    <keystoreFile>C:MyFilesDevelopmentServersTomcat9.0 
    confspring5server.keystore</keystoreFile> 
    <keystorePass>packt@@</keystorePass> 
    <update>true</update> 
    <username>packt</username> 
    <password>packt</password> 
  </configuration> 
</plugin>
2.	Right-click on the project and click on Run As | Maven Build... and execute the following goal: clean install tomcat7:deploy


Server shud be running. In this run it as tomcat7:deploy

ssl: secure socket layer
tls: transport layer security
ssl : old version, tls is a new version
provides confidentiality(noone can reach data), integrity(can find data has been modified), authenticity(can know data from wrong sender) (CIA): 


SSL handshake: Client sends server request to tell that it can communicate securely through these protocols..AES is one of the encryption mehod it can use and version of ssl it can use.
Server will choose the protocol it can communicate (cipher_suite) and responds with the version and protocol.
So now will start communicating securely
Server key exchange: server sends a digital certificate that is domain name with public key

Certificate has domain name like google.com and public key and signature which says it has been signed by Certificate authority and is legitimate
If we encrypt something with a public key, it can only be decryptd with a private key. Server has private key, public key can be distributed

So now can send data to server in plain text

But we can modify it for server and client to use symmetric private key for encrypting, decrytping. Client sends a random number to server encrypting it using server's public key, so only server private key can decrypt it


setting up HTTP/2 in Tomcat 9:
1.	Check if you have installed JDK 1.8 in your system. Tomcat 9 only runs with the latest JDK 1.8 without error logs.
2.	If you have downloaded the zipped version, unzip the folder to the filesystem of the development machine. If you have the EXE or MSI version, double-click the installer and follow the installation wizards. The following details must be taken into consideration:
1.	You can retain the default server startup port (8005), HTTP connector port (8080), and AJP port (8009) or configure according to your own settings.
2.	Provide the manager-gui with the username as packt and its password as packt.
3.	After the installation process, start the server and check whether the main page is loaded using the URL http://localhost:8080/.
4.	If Tomcat 9 is running without errors, it is now time to configure HTTP/2 protocol. Since HTTP/2 uses clear-text type request transactions, it is required that we configure Transport Layer Security (TLS) to use HTTP/2 since many browsers such as Firefox and Chrome do not support clear text. For TLS to work, we need a certificate from OpenSSL. For Windows machines, you can get it from https://slproweb.com/products/Win32OpenSSL.html.
5.	Install the OpenSSL (for example, Win64OpenSSL-1_1_0c.exe) by following the installation wizards. This will be used to generate our certificate signing request (CSR), SSL certificates, and private keys.
6.	Create an environment variable OPENSSL_HOME for your operating system. Register it into the $PATH the %OPENSSL_HOME%/bin.
7.	Generate your private key and SSL certificate by running the following command: openssl req -newkey rsa:2048 -nodes -keyout spring5packt.key -x509 -days 3650 -out spring5packt.crt.
8.	In our setup, the file spring5packt.key is the private key and must be strictly unreachable to clients, but by the server only. The other file, spring5packt.crt, is the SSL certificate that we will be registering both in the server keystore and JRE keystore. This certificate is only valid for 10 years (3,650 days).
9.	In Step 8, you will be asked to enter CSR information such as:
Country name (two-letter code) [AU]:PH 
State or province name (full name) [Some-State]: Metro Manila 
Locality name (for example, city):Makati City 
Organization name (for example, company) [Internet Widgits  
Pty Ltd]:Packt Publishing 
Organizational unit name (for example, section): Spring 5.0 Cookbook 
Common name (for example, server FQDN or your name): <must be domain-name> spring5server
E-mail address: sherwin.tragura@alibatabusiness.com 
10.	Generate a keystore that will be validated, both by your applications and server. JDK 1.8.112 provides keytool.exe that will be run to create keystores. Using the files in Step 8, run the following command:
keytool -import -alias spring5server -file spring5packt.crt -keystore spring5server.keystore
11.	If this is your first time, you will be asked to create a password of no less than six letters. Otherwise, you will be asked to enter your password. You will be asked if you want to trust the certificate. The message Certificate reply was installed in keystore means you have successfully done the process.
12.	Java JRE must know the certificate in order to allow all the execution of your deployed Spring 5 applications. To register the created certificate into the JRE cacerts, run the following command:
keytool -import -alias spring5server -file spring5packt.crt -keystore "<Java1.8_folder>\Java1.8.112\jre\lib\security\cacerts" -storepass changeit
  
13.	The default password is changeit. You will be asked to confirm if the certificate is trusted and you just type Y or yes. The message Certificate reply was installed in keystore means you have successfully finished the process.
14.	Copy the three files, namely spring5packt.crt, spring5packt.key, and spring5server.keystore to Tomcat's conf folder and JRE's security folder (<installation_folder>\Java1.8.112\jre\lib\security).
15.	Open Tomcat's conf\server.xml and uncomment the <Connector> with port 8443. Its final configuration must be:
<Connector port="8443" 
protocol="org.apache.coyote.http11.Http11AprProtocol" 
maxThreads="150" SSLEnabled="true"> 
    <UpgradeProtocol 
    className="org.apache.coyote.http2.Http2Protocol"/> 
    <SSLHostConfig honorCipherOrder="false"> 
        <Certificate certificateKeyFile="conf/spring5packt.key" 
        certificateFile="conf/spring5packt.crt" 
        keyAlias="spring5server" type="RSA" /> 
    </SSLHostConfig> 
</Connector> 
16.	Save the server.xml.
17.	Open C:\Windows\System32\drivers\etc\hosts file and add the following line at the end:
 127.0.0.1 spring5server
18.	Restart the server. Validate the setup through running https://localhost:8443. At first your browser must fire a message; Your connection is not secure. Just click Advanced and accept the certificate
19.	You will now be running HTTP/2.







