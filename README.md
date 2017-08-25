### Google Summer of Code 2017

## Paweł Ścibiorski 

Hello, the project on which I was working during this summer is Certificates Manager for Spark. Spark is a XMPP Instant Messanger client written in Java. It is a long lived project which has a huge code base and had many contributors across many years. As another contributor I become interested in security used in it. In the beginning it apeared that Spark makes use of the Transpor Layer Security protocol to encrypt data going to and from server. 

Nevertheless it lacked information about the used TLS public certificates and functionality allowing for adding and removing them. I also found problems with the existing implementation of TLS in Spark, which I fixed. 

### Certificate Settings Panel

I created a GUI for users in which they can add, remove and check certificates. That GUI also has a set of buttons which allows a user to apply some global policies on accepting different certificates. Although not all options are perfectly safe it is common to leave some choice to the end user. If someone wants to be more picky about certificates then instead of using global options, he can move specific to an exceptions-list. A central part of that interface is the certificate table, which allow to see the content of the KeyStores which hold certificates. 

![Spark_certificate_panel](https://github.com/Alameyo/GSoC2017/blob/master/SparkCertificatesPanel.png?raw=true)

### Certificate Dialog

Clicing on it (or on button "show certificate") will cause a popup of a dialog with more detailed of the chosen certificate.

Here a user can see all possible information about a certificate such as it's issuer, it's signature, validity period and various extensions. There are a huge possible number of extensions, many of them are in use only by particual private companies so it is not always to find a description for them, information on how to read them and their use. In such case the user will get information that the given certificate extension isn't known to Spark and the Object ID of that extension will be listed in certificate dialog in its "unsupported extension's" area.

![Spark_certificate_dialog](https://github.com/Alameyo/GSoC2017/blob/master/SparkCertificateDialog.png?raw=true)

### Trust Managers

The next problem that came up during work was the existing Trust Manager. It is a class handling approval or rejection of certificates coming from the remote server. The existing trust manager was accepting all certificates, regardless of their validity or source. That was terrible policy so I have made 2 new Trust Managers. The first one is a trust manager which also doesn't do any checks on validity of the certificates, but assures that a certification path can be build using the certificate chain given by server and it ends on trusted certificate in Spark's KeyStore. This Trust Manager is used for exempted certificates. The second Trust Manager is used by all other certificates. It performs every important security check according to the global policies from the Certificate Settings Panel. In case a certificate is invalid or the certification path cannot be built, then the connection is rejected.

### Mutual Authentication

It is rarely used (at least in XMPP) but the TLS protocol also definies optional Authentication of the both sides of the connection. For client side authentication I created an additional KeyStore which holds the client's private key together with the client's certificate. During the set-up of the TLS connection I send this certificate (or more certificates as the client can have a number of them) using the Key Manager class. The corresponding server's Trust Manager will decide wether to accept these certificates. This panel allow users to add and remove private keys and certificates to the identity KeyStore.

![Spark_Mutual_Auth_panel](https://github.com/Alameyo/GSoC2017/blob/master/SparkMutualAuthPanel.png?raw=true)

### Creating CSR and Self Signed certificates

An additional utility which is provided by the Mutual Authentication Panel is the creation of the Certificate Sign Request and Self Signed Certificates. The first one contains public key and other data which can be brought to the Certificate Authority. The CA can sign this with it's private key and issue a certificate. That issued certificate can be uploaded using the Mutual Authentication Panel to identify the client. 
The second option is to create a Self Signed Certificate, which isn't perfectly safe, and thus shouldn't be used for daily connections. Nevertheless it is often used as kind of proxy for testing purposes. This option will create a certificate signed by it's own private key.

### Blog
Started during the community bonding period, I documented the whole development proces with a blog. It also included some thoughts about PKI and the project. [Here](https://community.igniterealtime.org/people/alameyo/blog) you can see my posts.

## Code:
During developement code was often merged with Spark's master code base in a number of pull request. To clarify the development process JIRA's issue tracking system has been used. Here are links to the code and issues:

GITHUB: [SPARK-1926, SPARK-1927, SPARK-1928, SPARK-1929, SPARK-1938 and SPARK-1937](https://github.com/igniterealtime/Spark/pull/344) 

JIRA: [SPARK -1926](https://issues.igniterealtime.org/browse/SPARK-1926), [SPARK-1927](https://issues.igniterealtime.org/browse/SPARK-1927), [SPARK-1928](https://issues.igniterealtime.org/browse/SPARK-1928), [SPARK-1929](https://issues.igniterealtime.org/browse/SPARK-1929), [SPARK-1937](https://issues.igniterealtime.org/browse/SPARK-1937), [SPARK-1938](https://issues.igniterealtime.org/browse/SPARK-1938), [SPARK-1940](https://issues.igniterealtime.org/browse/SPARK-1940)

GITHUB: [SPARK-1932, SPARK-1930, SPARK-1935, SPARK-1933](https://github.com/igniterealtime/Spark/pull/345) 

JIRA: [SPARK-1932](https://issues.igniterealtime.org/browse/SPARK-1932), [SPARK-1930](https://issues.igniterealtime.org/browse/SPARK-1930), [SPARK-1935](https://issues.igniterealtime.org/browse/SPARK-1935), [SPARK-1933](https://issues.igniterealtime.org/browse/SPARK-1933)

GITHUB: [SPARK-1944](https://github.com/igniterealtime/Spark/pull/346)

JIRA: [SPARK-1944](https://issues.igniterealtime.org/browse/SPARK-1944)

GITHUB: [SPARK-1934, SPARK-1939 Certificate extensions](https://github.com/igniterealtime/Spark/pull/351)

JIRA: [SPARK-1934](https://issues.igniterealtime.org/browse/SPARK-1934), [SPARK-1939](https://issues.igniterealtime.org/browse/SPARK-1939)

GITHUB: [SPARK-1945, SPARK-1957, SPARK-1936](https://github.com/igniterealtime/Spark/pull/352)

JIRA: [SPARK-1936](https://issues.igniterealtime.org/browse/SPARK-1936), [SPARK-1945](https://issues.igniterealtime.org/browse/SPARK-1945), [SPARK-1957](https://issues.igniterealtime.org/browse/SPARK-1957)

GITHUB: [SPARK-1941, SPARK-1954](https://github.com/igniterealtime/Spark/pull/353)

JIRA: [SPARK-1941](https://issues.igniterealtime.org/browse/SPARK-1941), [SPARK-1954](https://issues.igniterealtime.org/browse/SPARK-1954)

GITHUB: [SPARK-1966, SPARK-1952, SPARK-1969, SPARK-1970, SPARK-1971 and partially SPARK-1968](https://github.com/igniterealtime/Spark/pull/356)

JIRA: [SPARK-1952](https://issues.igniterealtime.org/browse/SPARK-1952), [SPARK-1966](https://issues.igniterealtime.org/browse/SPARK-1966), [SPARK-1968](https://issues.igniterealtime.org/browse/SPARK-1968), [SPARK-1969](https://issues.igniterealtime.org/browse/SPARK-1969), [SPARK-1970](https://issues.igniterealtime.org/browse/SPARK-1970), [SPARK-1971](https://issues.igniterealtime.org/browse/SPARK-1971), [SPARK-1975](https://issues.igniterealtime.org/browse/SPARK-1975), [SPARK-1977](https://issues.igniterealtime.org/browse/SPARK-1977)

GITHUB: [SPARK-1953, SPARK-1959, SPARK-1980 and some work on blacklist](https://github.com/igniterealtime/Spark/pull/367)

JIRA: [SPARK-1953](https://issues.igniterealtime.org/browse/SPARK-1953), [SPARK-1959](https://issues.igniterealtime.org/browse/SPARK-1959),  

GITHUB: [SPARK-1985 Support for TLS mutual authentication](https://github.com/igniterealtime/Spark/pull/371)

JIRA: [SPARK-1985](https://issues.igniterealtime.org/browse/SPARK-1985)

GITHUB: [SPARK-1989 create empty KeyStores](https://github.com/igniterealtime/Spark/pull/376)

JIRA: [SPARK-1989](https://issues.igniterealtime.org/browse/SPARK-1989)

GITHUB: [SPARK-1994 Make use of the JRE certificates](https://github.com/igniterealtime/Spark/pull/377)

JIRA: [SPARK-1994](https://issues.igniterealtime.org/browse/SPARK-1994)

GITHUB: [SPARK-1995, SPARK-1996, SPARK-1997 and lack of resources in one text](https://github.com/igniterealtime/Spark/pull/378/commits)

JIRA: [SPARK-1995](https://issues.igniterealtime.org/browse/SPARK-1995), [SPARK-1996](https://issues.igniterealtime.org/browse/SPARK-1996), SPARK-1997(https://issues.igniterealtime.org/browse/SPARK-1997)

### Still TO-DO

The project is generally done but still there are some things that could be improved as well as new ideas for extending it. Worthy to mention:

* sorting of the tables with certificates is temporaily disabled as it require some fix [SPARK-1951](https://issues.igniterealtime.org/browse/SPARK-1951)
* new idea: to add certificates to TrustStore from chain recived from server [SPARK-2001](https://issues.igniterealtime.org/browse/SPARK-1951)
* guide for the new settings provided by the project is yet to be created
* it would be nice to add some tool for searching certificates by typing their names 
* from time to time there occurs an error at adding/removing certificate from exceptions [SPARK-1999](https://issues.igniterealtime.org/browse/SPARK-1999)
