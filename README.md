Download Link: https://assignmentchef.com/product/solved-cs270-project-5-a-file-server
<br>
<h1>Introduction</h1>

Many services are provided over the Internet by means of communication between clients (anywhere) and servers (on specific machines). These services include the secure shell (used by the <strong><em>ssh</em></strong> client), the network time service, and e-mail. Many cloud services such as file storage also fit into this category.

This project is for you to write your own file service. The goal of this project is to help you understand the basics about network programming. The project consists of two parts.

A <em>server</em> running the program <strong><em>filed</em></strong> that accepts a set of valid requests (listed below),

Three client application programs that make various requests to the server. There is a fourth, extra-credit application as well.

Your code for this project must be written in C or C++ and must use the native Unix TCP/IP socket interface (socket syscalls) or the <strong>csapp</strong> library calls provided by the textbook (such as <strong>open_clientfd()</strong>, <strong>open_listenfd()</strong>, <strong>Rio_writen()</strong>, <strong>Rio_readnb()</strong>), which can be found on in the files <strong>csapp.c</strong> and <strong>csapp.h</strong> on the book’s code web page: <a href="http://csapp.cs.cmu.edu/public/code.html">http://csapp.cs.cmu.edu/public/code.html</a>.

<h1>Groups</h1>

You may work in groups of two on this project. You may select your own partner for this project; you do not need to select the same partner you had before. You may also decide to work alone on the project, but you must still do the entire project; there is no reduction in the amount of work you must do.

<h1>Assumptions</h1>

You should assume the following when writing your code:

machineName (DNS names or dotted decimal IPv4 addresses) have a maximum length of 40 characters.

File data can be either binary or text.

Your server does not provide service unless the client includes your server’s secretKey in its requests. The server and the clients get the initial secretKey from the command line.

The secretKey is an unsigned 32-bit integer (in the range 0 to 2<sup>32</sup>-1).

<h1>File Server</h1>

Your file server should listen for incoming connections using the IP address <strong>INADDR_ANY</strong> and a port number specified as a commandline parameter to your server. Your server should only respond to requests that include a secretKey: an integer used to prevent unauthorized access to your server (like a PIN on your bank account). To run your server, you might type <strong>filed 5678 987654</strong>, which would start your file server listening for connections at port <strong>5678</strong> with <strong>987654</strong> as the secretKey. See the example <em>echo</em> server from the notes and textbook as an example of how to create a server listening on a particular port.

Your file server should accept incoming requests from clients. Each request is in a new connection to your server. Your server should handle only one request per connection. The details of the protocol used to exchange information between your clients and the server is described <u>below</u>. You must verify the secretKey on every transaction.

Your server should print the following information to standard output for every valid request it receives:

<table width="623">

 <tbody>

  <tr>

   <td width="178"><strong>Output</strong></td>

   <td width="445"><strong>Note</strong></td>

  </tr>

  <tr>

   <td width="178"><strong>Secret key = <em>secretKey</em></strong></td>

   <td width="445"><em>secretKey</em> is the secret key contained in the request.</td>

  </tr>

  <tr>

   <td width="178"><strong>Request type = <em>type</em></strong></td>

   <td width="445"><em>type</em> is one of <strong>newKey</strong>, <strong>fileGet</strong>, <strong>fileDigest</strong>, and for extra credit: <strong>fileRun</strong>.</td>

  </tr>

  <tr>

   <td width="178"><strong>Completion = <em>status</em></strong></td>

   <td width="445"><em>status</em> is either <strong>success</strong> or <strong>failure</strong></td>

  </tr>

  <tr>

   <td width="178"><strong>Detail = <em>detail</em></strong></td>

   <td width="445"><em>detail</em> is specific to the particular request type, as shown below.</td>

  </tr>

 </tbody>

</table>

If the secret key is wrong, it is only necessary to print out the secretKey line, and the server may immediately close the connection to the client.

<h1>Applications</h1>

You must program three client-side applications, each a separate program. These programs take command-line parameters needed to communicate with the server: <em>machineName</em>, <em>port</em>, and <em>secretKey</em>, followed by specifics detailing the command.

<strong>Parameter        Purpose</strong>

<em>machineName </em>the DNS name (like <strong>george.netlab.uky.edu</strong>) or the IP address (like <strong>128.163.146.12</strong>) of the machine where your file server is running

<em>port       </em>the TCP port number where your file server is listening <em>secretKey    </em>the number to use as the secretKey when communicating with your server

In each of the descriptions below, <strong><em>value</em></strong> can be any sequence of characters not exceeding 100 bytes. The four programs you must write are:

<h2> newKey machineName port secretKey value</h2>

<strong><em>newKey</em></strong> asks the server to set the secret key to a new value, which must be a valid integer in the range 0 to 2<sup>32</sup>-1. The command must include the current secret key. The client prints either <strong>success</strong>, <strong>failure</strong> (if the server rejects the command) or <strong>invalid</strong> (if the value is invalid).

<h2> fileGet machineName port secretKey fileName</h2>

<strong><em>fileGet</em></strong> asks the server to respond with the first 100 bytes of the given file (or fewer if the file is shorter). The server reports failure if the file does not exist or is not accessible. The client prints the bytes it receives from the server (in <strong>%02x</strong> format), or the single word <strong>failure</strong>.

<strong> fileDigest <em>machineName port secretKey fileName </em></strong><strong><em>fileDigest</em></strong> asks the server to generate a cryptographic digest of the given file, the result of this invocation:

<strong>/usr/bin/sha256sum fileName</strong>. The result is guaranteed to be 100 bytes or less. The client prints the result it receives from the server (as a string), or the single word <strong>failure</strong>.

<h2> fileRun machineName port secretKey programName</h2>

This command is for extra credit. <strong><em>fileRun</em></strong> asks the server to run a specified program and send the first 100 bytes (or fewer if there are fewer) of its output back to the client. Only certain programNames are valid; the others must be rejected. The valid values are:

<h2>programName run this command</h2>

inet <strong>/bin/ip address </strong>hosts <strong>/bin/cat /etc/hosts </strong>service <strong>/bin/cat /etc/services </strong>identity <strong>/bin/hostname</strong>

The client prints the result it receives from the server or the single word <strong>failure</strong>.

<h2>Creating a set of Library/API calls</h2>

Construct a <em>fileServer library</em> that could be used by any client, not just the three or four client programs that you write. Each of the library routines should take as parameters the <em>machineName</em>, <em>port</em>, and <em>secretKey</em> of the filer server. Library routines that you should write:

<h3>int newKey(const char *machineName, unsigned short port, unsigned int secretKey, unsigned int newKey) int fileGet(const char *machineName, unsigned int port, unsigned int secretKey, const char *fileName, char *result, unsigned int *resultLength) int fileDigest(char *machineName, unsigned short port, unsigned int secretKey, const char *fileName, char *result, unsigned int *resultLength) For extra credit: int fileRun(const char *machineName, unsigned short port, unsigned int secretKey, const char *request, char *result, unsigned int *resultLength)</h3>

Each of these library routines returns 0 on success and -1 on failure.

These library routines should build a new connection for each request. The connection only lasts for the duration of the single request. The format to use for each request is described <u>below</u>.

<h1>File-server Protocol</h1>

Each request to your server is carried by a new connection over which the client sends a request and receives a reply. The protocol, that is, the format of the messages sent between clients and server, is described below for each type of request and response.

<h1>Messages from client to server</h1>

Numbers in all messages must be in network byte order. All messages from the client to the server have this format:

Bytes 0-3: A 4-byte unsigned integer containing <em>secretKey</em>.

Bytes 4-5: A 2-byte unsigned integer (a short) containing the type of request: newKey (0), fileGet (1), fileDigest (2), fileRun (3).

Bytes 6-7: Two bytes of padding, with arbitrary values.

The remainder of the bytes are command-specific.

newKey request

Bytes 8-11: the new secret key, as an unsigned 32-bit int, in network byte order.

fileGet request

Bytes 8-107: a null-terminated file name, no longer than 100 characters. fileDigest request

Bytes 8-107: a null-terminated file name, no longer than 100 characters.

Run request (extra credit)

Byte 8-23: a 16-byte string (null terminated) holding one of the valid values.

<h1>Messages from server to client</h1>

Numbers in all messages must be in network byte order. If a client sends a request with an invalid secret key, the server should close the connection without returning any message at all.

All messages from the server to the client have this format:

Byte 0: A 1-byte return code: 0 for success, -1 for failure.

Bytes 1-3: Three bytes of padding, with arbitrary values.

The remainder of the bytes are command-specific.

newKey response

No further data fileGet, fileDigest, and (extra credit) fileRun response

Bytes 4-5: A 2-byte unsigned integer (short) giving the length of the value, which must not exceed 100, including the concluding null (for a string value).

Bytes 6 ..: The value itself. The server need not send any more than the number of bytes required.

<h1>Getting Started</h1>

You might want to use this <a href="http://www.cs.uky.edu/~raphael/courses/CS270/project5/Makefile">Makefile</a>.

You could start by modifying the echo client and server provided in the notes and in the textbook, but the file-server clients and server are quite different from the echo client and server. You can find a copy of the code online under the directory <strong>netp</strong> on the web page: <a href="http://csapp.cs.cmu.edu/public/code.html">http://csapp.cs.cmu.edu/public/code.html</a>.

Each client application makes one connection and exits, so you can begin by modifying the code to send a single echo request.

Another difference is that your clients send data across the connection (as opposed to lines read from a terminal). As a result, you should use <strong>Rio_readn()</strong> or <strong>Rio_readnb()</strong> rather than <strong>Rio_readlineb()</strong>, because <strong>Rio_readlineb()</strong> is designed to read text lines one at a time.

Your applications need to send the appropriate information across the connection based on the protocol description given <u>above</u>. On the server side, you may use the <strong>system()</strong> library call instead of lower-level calls like <strong>execvp()</strong>.

<h1>Credit and extra credit</h1>

You should complete all the client programs. However, you can get 90% of the full server score by only handling <strong>newKey</strong> and <strong>fileGet</strong>. Handling <strong>fileDigest</strong> is worth another 10%, and handling <strong>fileRun</strong> is worth a bonus of 5%.

<h1>Testing your Client Applications and the Server</h1>

Your code must compile and run on your VM. You may write your code on another machine and port it to your VM later, but your code must run properly on your VM.

You can run both your server and the clients on your VM. If you would like to run your client applications on other machines, try running from your laptop (if it is running Linux) or a MultiLab machine such as <strong>vio.cs.uky.edu</strong>, <strong>iri.cs.uky.edu</strong>, or <strong>pen.cs.uky.edu</strong> (they are similar enough to your VMs so that your code should run there without problems).

You can get working copies of the programs <a href="http://www.cs.uky.edu/~raphael/courses/CS270/project5/workingProgram.zip">here</a> (a ZIP file). You should test your clients and your server against the working program.

To test your client-side applications, you might type commands like the following and get responses like those shown. The responses depend, of course, on the particular machine running the server and its environment.

<strong>%</strong><strong> host=`hostname`</strong>

<strong>%</strong><strong> port=6564 </strong>

<strong>%</strong><strong> key=1411223</strong>

<strong>%</strong><strong> newKey=1226631232</strong>

<strong>%</strong><strong> ./newKey $host $port $key $newKey</strong>

<strong>success</strong>

<strong>%</strong><strong> ./newKey $host $port $newKey $newKey</strong>

<strong>success</strong>

<strong>%</strong><strong> ./newKey $host $port $key $key</strong>

<strong>failure</strong>

<strong>%</strong><strong> ./fileGet $host $port $newKey /etc/hosts </strong><strong>3132372e302e302e31206c6f63616c686f73740a0a232054686520666f6c6c6f77696e67206c696e65732061726520646573</strong>

<strong>%</strong><strong> ./fileGet $host $port $newKey /bin/ls </strong><strong>7f454c4602010100000000000000000003003e000100000050580000000000004000000000000000a0030200000000000000</strong>

<strong>%</strong><strong> ./fileDigest $host $port $newKey /etc/services </strong><strong>75d36dfa5c3f829d23f64750d6451641963c5a1e0c6e5d85fb83525755b1450c  /etc/services</strong>

<strong>%</strong><strong> ./fileDigest $host $port $newKey /etc/sorvices</strong>

<strong>failure</strong>

<strong>%</strong><strong> ./fileRun $host $port $newKey service </strong><strong># Network services, Internet style</strong>

<strong>#</strong>

<strong># Note that it is presently the policy of IANA to assign a sing</strong>

<strong>%</strong><strong> ./fileRun $host $port $newKey inet</strong>

<strong>1: lo: &lt;LOOPBACK,UP,LOWER_UP&gt; mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000     link</strong>

For that client input, the server might output as follows:

<strong>  % </strong><strong>~/CS270/project5/filed 6564 1411223</strong>

<strong>  Secret key = 1411223</strong>

<strong>  Request type = newKey</strong>

<strong>  Detail = 1226631232</strong>

<strong>  Completion = success </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = newKey </strong>

<strong>  Detail = 1226631232</strong>

<strong>  Completion = success </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1411223</strong>

<strong>  Request type = newKey </strong>

<strong>  Detail = 1411223</strong>

<strong>  Completion = failure </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = fileGet </strong>

<strong>  Detail = /etc/hosts</strong>

<strong>  Completion = success</strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = fileGet </strong>

<strong>  Detail = /bin/ls</strong>

<strong>  Completion = success </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = fileDigest </strong>

<strong>  Detail = /etc/services</strong>

<strong>  Completion = success </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = fileDigest </strong>

<strong>  Detail = /etc/sorvices</strong>

<strong>  Completion = failure </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = run </strong>

<strong>  Detail = service</strong>

<strong>  Completion = success </strong>

<strong>  ————————– </strong>

<strong>  Secret key = 1226631232</strong>

<strong>  Request type = run </strong>

<strong>  Detail = inet</strong>

<strong>  Completion = success   ————————– </strong>

<strong>  </strong>

<h1>Some hints</h1>

<ol>

 <li>The clients and the server need to send and receive data. They don’t need to send data all at once; they can send part of the required information and then send the rest. Likewise, they don’t need to receive all the data at once; they can separate receiving information into multiple requests.</li>

 <li>To capture the result of running a program, the server can redirect the program to a file such as <strong>/tmp/foo</strong>, then read that file to prepare for sending a result to the client. Fancier, but somewhat higher quality, is to use <strong>popen(3)</strong>.</li>

 <li>If you are writing in C++, to include the <strong>h</strong> header:</li>

</ol>

<strong>extern “C”</strong>

<strong>{</strong>

<strong>  #include “csapp.h”</strong>

<strong>}</strong>

<h1>What to Submit</h1>

Your code must compile and run on the VM that you have been assigned or you will receive no credit.

Only one person from your group should submit the code. Remember to list both people from your group in the <strong>README</strong> file. The other member of the group should submit a file saying “worked with (name your partner here)”.

You should submit all your code plus a documentation file. You should not submit <strong>.o</strong> files or other binary files. To create your submission, tar and compress all files that you are submitting: <strong>tar czf proj5.tgz <em>projectdirectory</em></strong>. The tar package should include the following:

<strong> README</strong> — listing all the files you are submitting along with your names, and documentation. The documentation should be a brief description of your project, including the algorithms you used. It should also include a description of any special features or limitations of your project. If you do not document a limitation, we will assume you did not know about it, and we will consider it a bug. <strong>Do not submit MS Word, PostScript, or PDF files</strong>.

<strong>Makefile</strong> — we will type <strong>make</strong>, and we expect <strong><em>make</em></strong> to compile your program.

all your C/C++ files all your header files

Upload your tar file as usual to <a href="https://www.cs.uky.edu/csportal">https://www.cs.uk</a><a href="https://www.cs.uky.edu/csportal">y</a><a href="https://www.cs.uky.edu/csportal">.edu/csportal</a><a href="https://www.cs.uky.edu/csportal">.</a> You may upload your project as many times as you like. The csportal timestamps your submission with the date/time of the last submission.