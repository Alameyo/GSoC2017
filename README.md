# GSoC2017

## Paweł Ścibiorski 

# Certificate management support for Spark

Hello, project on which I was working during this summer is Certificates Manager for Spark. Spark is XMPP Instant Messanger client written in Java. It is long lived project which have huge code base and had many contributors across many years. As another one contributor I become interested in security used in it. At beginning it apeared that Spark make use of Transpor Layer Security protocol to encrypt data going to and from server. 

It lacked however support for information for user about used public key certificates used by TLS as well as options for adding and removing them. I found also problems with existing imlepentation of the TLS in Spark which I fixed. 

### Certificate Settings Panel

I created GUI for users in which he can add, remove and check certificates. That GUI also have set of buttons which allow to apply some global policies on accepting different certificates. Although not all options are perfectly safe it is common to left some choice to the end user. If someone want to be more cherrypicking about certificates then instead of using global option he can move it to exceptions. Central part of that interface is certificate table which allow to see content of the KeyStores which hold certificates. 

![Spark_certificate_panel](https://github.com/Alameyo/GSoC2017/blob/master/Spark_Certificates_Panel.png)

### Certificate Dialog

Clicing on it (or on button "show certificate") will cause popup of more detailed dialog with choosen certificate.

Here user can see all possible informations about certificate such as it's issuer, it's signature, validity period and various number of extensions. There is huge possible number of extensions, many of them are in use only by particualr private companies so not always it is easy to find description for them, information how to read them and their use. In such case user will get information that given certificate extension isn't known to Spark and Object ID of that extension will be listed in certificate dialog in unsupported extension's area.

![Spark_certificate_dialog](https://github.com/Alameyo/GSoC2017/blob/master/Spark_Certificate_Dialog.png)

### Trust Managers

Next problem that come furing work was existing Trust Manager, it is class handling approving or rejecting certificates coming from remote server. Existing trust manager was accepting all certificates, regardless of their validity or source. That was terrible policy so I have made 2 new Trust Managers. First one is trust manager which also doesn't do any checks on validity of the certificates but assure that certification path can be build using certificate chain given by server and it ends on trusted certificate in Spark's KeyStore. This Trust Manager is used for exempted certificates. Second Trust Manager is used by all other certificates. It do every important security checks according to the global policies from Certificate Settings Panel. In case when certificate is invalid or certification path cannot be build then connection is rejected.

### Mutual Authentication

It is rare in use but TLS protocol definies also optionally Authentication of the both sides of the connection. For client side authentication I created additional KeyStore which hold client's private key together with client's certificate. During setting up TLS connection I send this certificate (or more certificates as client can have number of them) using Key Manager class. Coresponding server's Trust Manager will decide to accept or not this certificates. This panel allow user to add and remove private keys and certificates to identity KeyStore.

![Spark_Mutual_Auth_panel](https://github.com/Alameyo/GSoC2017/blob/master/Spark_Mutual_Auth_Panel.png)

### Creating CSR and Self Signed certificates

Additional utility which is allowed by Mutual Authentication Panel is creation of the Certificate Sign Request and Self Signed Certificates. First one contain public key and other data which can be brought to the Certificate Authority. CA can sign this with it's private key and issue a certificate. That issued certificate can be uploaded using Mutual Authentication Panel to identify client. 

Second option, to create Self Signed Certificate isn't perfectly safe and shouldn't be realy used for daily connections, neverthless it is often used as kind of proxy for testing purposes. This option will create certificate signed by own private key.

## Code:
During developement code were often merged with Spark's master code base in number of pull request. As help system of JIRA issues were also used. Here are links to the code and issues:
[SPARK-1926, SPARK-1927, SPARK-1928, SPARK-1929, SPARK-1938 and SPARK-1937](https://github.com/igniterealtime/Spark/pull/344) JIRA: [SPARK -1926](https://issues.igniterealtime.org/browse/SPARK-1926) [SPARK-1927](https://issues.igniterealtime.org/browse/SPARK-1927) [SPARK-1928](https://issues.igniterealtime.org/browse/SPARK-1928), [SPARK-1929](https://issues.igniterealtime.org/browse/SPARK-1929),[SPARK-1937](https://issues.igniterealtime.org/browse/SPARK-1937), [SPARK-1938](https://issues.igniterealtime.org/browse/SPARK-1938), [SPARK-1940](https://issues.igniterealtime.org/browse/SPARK-1940)
[SPARK-1932, SPARK-1930, SPARK-1935, SPARK-1933](https://github.com/igniterealtime/Spark/pull/345) JIRA: [SPARK-1932](https://issues.igniterealtime.org/browse/SPARK-1932), [SPARK-1930](https://issues.igniterealtime.org/browse/SPARK-1930), [SPARK-1935](https://issues.igniterealtime.org/browse/SPARK-1935), [SPARK-1933](https://issues.igniterealtime.org/browse/SPARK-1933)
