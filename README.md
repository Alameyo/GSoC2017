# GSoC2017
Paweł Ścibiorski 
Certificate management support for Spark

Hello, project on which I was working during this summer is Certificates Manager for Spark. Spark is XMPP Instant Messanger client written in Java. It is long lived project which have huge code base and had many contributors across many years. As another one contributor I become interested in security used in it. At beginning it apeared that Spark make use of Transpor Layer Security protocol to encrypt data going to and from server. 

It lacked however support for information for user about used public key certificates used by TLS as well as options for adding and removing them. I found also problems with existing imlepentation of the TLS in Spark which I fixed. 

I created GUI for users in which he can add, remove and check certificates. That GUI also have set of buttons which allow to apply some global policies on accepting different certificates. Although not all options are perfectly safe it is common to left some choice to the end user. If someone want to be more cherrypicking about certificates then instead of using global option he can move it to exceptions. Central part of that interface is certificate table which allow to see content of the KeyStores which hold certificates. 

![Spark_certificate_panel](https://github.com/Alameyo/GSoC2017/blob/master/Spark_Certificates_Panel.png)

Clicing on it (or on button "show certificate") will cause popup of more detailed dialog with choosen certificate.
Here user can see all possible informations about certificate such as it's issuer, it's signature, validity period and various number of extensions. There is huge possible number of extensions, many of them are in use only by particualr private companies so not always it is easy to find description for them or information how to use them. In such case user will get information that given certificate extension isn't known to Spark and also Object ID of that extension will be listed in certificate dialog.
Next problem that come furing work was existing TrustManager, it is class handling approving or rejecting certificates 
