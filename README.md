# labs

When running the sample client programs, you can communicate with an existing server, such as a web server, or you can communicate with the sample server program, ClassFileServer. You can run the sample client and the sample server programs on different machines connected to the same network, or you can run them both on one machine but from different terminal windows.

## Configuration Requirements for SSL Socket Samples
When running the sample programs that create a secure socket connection between a client and a server, you will need to make the appropriate certificates file (truststore) available. For both the client and the server programs, you should use the certificates file samplecacerts from the samples directory. Using this certificates file will allow the client to authenticate the server. The file contains all the common Certificate Authority (CA) certificates shipped with the JDK (in the cacerts file), plus a certificate for localhost needed by the client to authenticate localhost when communicating with the sample server ClassFileServer. The ClassFileServer uses a keystore containing the private key for localhost that corresponds to the public key in samplecacerts.

To make the samplecacerts file available to both the client and the server, you can either copy it to the file java-home/lib/security/jssecacerts, rename it to cacerts, and use it to replace the java-home/lib/security/cacerts file, or add the following option to the command line when running the java command for both the client and the server: 
`-Djavax.net.ssl.trustStore=path_to_samplecacerts_file`

The password for the samplecacerts truststore is changeit. You can substitute your own certificates in the samples by using the keytool utility.

If you use a browser to access the sample SSL server provided in the ClassFileServer example, then a dialog box may pop up with the message that it does not recognize the certificate. This is normal because the certificate used with the sample programs is self-signed and is for testing only. You can accept the certificate for the current session. After testing the SSL server, you should exit the browser, which deletes the test certificate from the browser's namespace.

For client authentication, a separate duke certificate is available in the appropriate directories. The public key and certificate is also stored in the samplecacerts file.

## Running SSLSocketClient

The example SSLSocketClient.java demonstrates how to create a client that uses an SSLSocket to send an HTTP request and to get a response from an HTTPS server. By default, this example connects to www.verisign.com, but it can easily be adapted to connect to ClassFileServer. The output of this program is the HTML source for https://www.verisign.com/index.html.

You must not be behind a firewall to run this program as provided. If you run it from behind a firewall, you will get an UnknownHostException because JSSE cannot find a path through your firewall to www.verisign.com. To create an equivalent client that can run from behind a firewall, set up proxy tunneling as illustrated in the sample program SSLSocketClientWithTunneling.

Usage
`java SSLSocketClient`

## Running SSLSocketClientWithTunnelling

The example SSLSocketClientWithTunneling.java illustrates how to do proxy tunneling to access a secure web server from behind a firewall.

The program will return the HTML source file from https://www.verisign.com/index.html.

Usage

`java -Dhttps.proxyHost=webproxy -Dhttps.proxyPort=ProxyPortNumber SSLSocketClientWithTunneling`

Replace webproxy with the name of your proxy host and ProxyPortNumber with the appropriate port number.

The system properties https.proxyHost and https.proxyPort are used to make a socket connection to the proxy host, and then the SSLSocket is layered on top of that Socket.

## Running SSLSocketClientWithClientAuth
The example SSLSocketClientWithClientAuth.java is similar to Running SSLSocketClient, but this shows how to set up a key manager to do client authentication if required by a server. This program also assumes that the client is not outside a firewall. 

To run this program, you must specify three parameters: host, port, and requested file path. To mirror the previous examples, you can run this program without client authentication by setting the host to www.verisign.com, the port to 443, and the requested file path to https://www.verisign.com/. The output when using these parameters is the HTML for the website https://www.verisign.com/.

To run SSLSocketClientWithClientAuth to do client authentication, you must access a server that requests client authentication.

Usage

`java SSLSocketClientWithClientAuth host port requestedfilepath`

## Running ClassFileServer

This example, which consists of ClassFileServer.java and ClassServer.java, demonstrates the implementation of a mini-webserver, which can service simple HTTP or HTTPS requests (only the GET method is supported). By default, the server does not use SSL/TLS. However, a command-line option enables SSL/TLS. Requests must be of the form:

`GET /<filename>`

Usage

`java ClassFileServer port docroot [TLS [true]]`

port: The port on which the server resides. It can be any available unused port number, for example, you can use the number 2001.
docroot: The root of the local directory hierarchy. It indicates the directory on the server that contains the file you want to retrieve. For example, on Linux, you can use /home/userid/ (where userid refers to your particular UID), whereas on Windows systems, you can use C:\.
TLS: An optional flag which enables SSL/TLS services. If you omit the TLS and true parameters, which indicates that an ordinary (not TLS) file server should be used, without authentication, then nothing happens. This is because one side (the client) is trying to negotiate with TLS, while the other (the server) is not, so they cannot communicate.
true: An optional flag which requires that clients authenticate themselves. This option requires that SSL/TLS support be enabled.

The secure server comes preinstalled with a certificate for localhost. If the server is on the same host as the client, URLs of the form https://localhost:port/file should pass hostname verification. If you choose to run on separate hosts, you should create a new host certificate for the https hostname being used, otherwise there will be hostname mismatch problems. (Note: in Java, this can be corrected in the HttpsURLConnection class by providing a custom HostnameVerifier implementation, or in a browser by accepting the dialog box that describes the hostname mismatch.)

## Running SSLSocketClientWithClientAuth with ClassFileServer
You can use the sample programs SSLSocketClientWithClientAuth.java and ClassFileServer.;ava to set up authenticated communication, where the client and server are authenticated to each other. You can run both sample programs on different machines connected to the same network, or you can run them both on one machine but from different terminal windows or command prompt windows. To set up both the client and the server, do the following:

1. Run the program ClassFileServer from one machine or terminal window
2. Run the program SSLSocketClientWithClientAuth on another machine or terminal window. SSLSocketClientWithClientAuth requires the following parameters:
host is the host name of the machine that you are using to run ClassFileServer.
port is the same port that you specified for ClassFileServer.
requestedfilepath indicates the path to the file that you want to retrieve from the server. You must give this parameter as /filepath. Forward slashes are required in the file path because it is used as part of a GET statement, which requires forward slashes regardless of what type of operating system you are running. The statement is formed as follows:
`"GET " + requestedfilepath + " HTTP/1.0"`
