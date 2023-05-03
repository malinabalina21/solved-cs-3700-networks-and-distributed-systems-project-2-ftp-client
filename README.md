Download Link: https://assignmentchef.com/product/solved-cs-3700-networks-and-distributed-systems-project-2-ftp-client
<br>
<nav class="navbar navbar-inverse navbar-fixed-top"></nav>

<article class="container">

 The goal of this project is to deepen your ability to write network code. At this point you can open a socket and send and receive simple messages. In this project, you will implement a client for a more complex protocol, that has more features, and uses two sockets rather than one.




 Specifically, in this project you will develop a client for the File Transfer Protocol (FTP). We have setup an FTP server for you to use when developing and debugging your client. The server is available at <a href="ftp://3700.network/">ftp://3700.network</a>.

 <h3>What is FTP</h3>

 Developed in 1971, the File Transfer Protocol (FTP) is one of the oldest protocols still in common use today. FTP’s development predates TCP/IP, yet the protocol still works fairly well on the modern internet.

 FTP is a client-server oriented protocol for uploading, downloading, and managing files. The FTP server’s job is to listen for incoming connections from clients and then respond to their requests. These requests include all common file and directory operations, such as listing the files in a directory, making and deleting directories, uploading files from the client machine to the server, and downloading files from the server to the client machines. FTP clients connect to FTP servers and issue commands to tell the server what to do.

 Because the FTP protocol is so old, it has many, many features, some of which are vestigial and no longer make sense on the modern internet (e.g., uploading a file in 36-bit, EBCDIC format directly to a line-feed printer), and others that are so esoteric that they are rarely used and supported. Wikipedia has an <a href="https://en.wikipedia.org/wiki/File_Transfer_Protocol">extensive article</a> on the FTP protocol, as well as a <a href="https://en.wikipedia.org/wiki/List_of_FTP_commands">list of all FTP protocol commands</a>. Fortunately, a FTP client only needs to support a minimum, basic set of commands in order to function. We outline the necessary functionality for the FTP client you will develop below. Our reference implementation is roughly 300 lines of Python3, including self-documentation and extensive error checking.

 <h3>FTP Server Decorum</h3>

 We have set up a FTP server for you to help you develop and debug your client. It is available at <a href="ftp://3700.network/">ftp://3700.network</a>. To use the FTP server you must login with a username and password. We will email each student to give them a unique username and password. After logging in, the server will give each student access to an isolated folder for uploading and downloading files.

 <h3>Getting Familiar with FTP</h3>

 If you’ve never used an FTP client or server before, it may be helpful to use an existing client to familiarize yourself with how FTP works at a high-level. We recommend that beginners start with <a href="https://filezilla-project.org/">FileZilla</a>, which is a popular, feature packed, open-source FTP client that works across all major operating systems. Note that FileZilla is a GUI client; in this project you will be building a command line client.

 Unix and BSD-compliant systems like Linux and MacOS have a <a href="https://linux.die.net/man/1/ftp">command line FTP client</a> named <i>ftp</i> that is typically pre-installed, or can be installed easily from the command line. This command line ftp client is interactive: running it opens an FTP shell where the user can type FTP commands like “dir” and “cd” to view and change remote filesystem directories, respectively. The “help” command will list available FTP commands, and “help [command name]” provides helpful information about a specific command.

 The <a href="https://linux.die.net/man/1/curl"><i>curl</i></a> command line program, which is also available for most Unix and BSD-based systems, supports downloading files from FTP servers. It has a command line syntax that is most similar (though not identical) to the FTP client that you will be developing in this assignment.

 <h3>High Level Requirements</h3>

 Your goal to write a basic FTP client application. This client will run on the command line, and must support the following six operations: directory listing, making directories, file deletion, directory deletion, copying files to and from the FTP server, and moving files to and from the FTP server.

 Your FTP client must execute on the command line using the following syntax:

 <pre>$ ./3700ftp [operation] param1 [param2]</pre>

 <i>operation</i> is a string that specifies what operation the user is attempting to perform. Valid operations are <i>ls</i>, <i>mkdir</i>, <i>rm</i>, <i>rmdir</i>, <i>cp</i>, and <i>mv</i>. Each operation requires either one or two parameters, denoted by <i>param1</i> and <i>param2</i> on the command line. <i>param1</i> and <i>param2</i> are strings that either represent a path to a file on the local filesystem, or a URL to a file or directory on an FTP server.

 <i>3700ftp</i> is not required to print anything to STDOUT or STDERR. That said, students may add functionality that prints out FTP protocol messages (e.g., to aid debugging), directory listings, and errors (e.g., network or socket errors, FTP protocol errors, etc.).

 Here is an example of a full-fledged <i>3700ftp</i> implementation. Note that it includes two optional parameters (<i>–verbose</i> and <i>–help</i>); <b>your client is not required to have these optional parameters</b>. The help for this client describes what each operation does, how to interpret the command parameters with respect to each operation, and the URL format used to specify the FTP server’s connection information.

 <pre>$ ./3700ftp --helpusage: 3700ftp.py [-h] [--verbose] operation params [params ...]FTP client for listing, copying, moving, and deleting files and directories on remote FTP servers.positional arguments:  operation      The operation to execute. Valid operations are 'ls', 'rm', 'rmdir',                 'mkdir', 'cp', and 'mv'  params         Parameters for the given operation. Will be one or two paths and/or URLs.optional arguments:  -h, --help     show this help message and exit  --verbose, -v  Print all messages to and from the FTP server# Available OperationsThis FTP client supports the following operations:    ls &lt;URL&gt;                 Print out the directory listing from the FTP server at the given URL    mkdir &lt;URL&gt;              Create a new directory on the FTP server at the given URL    rm &lt;URL&gt;                 Delete the file on the FTP server at the given URL    rmdir &lt;URL&gt;              Delete the directory on the FTP server at the given URL    cp &lt;ARG1&gt; &lt;ARG2&gt;         Copy the file given by ARG1 to the file given by                             ARG2. If ARG1 is a local file, then ARG2 must be a URL, and vice-versa.    mv &lt;ARG1&gt; &lt;ARG2&gt;         Move the file given by ARG1 to the file given by                             ARG2. If ARG1 is a local file, then ARG2 must be a URL, and vice-versa.# URL Format and DefaultsRemote files and directories should be specified in the following URL format:    ftp://[USER[:PASSWORD]@]HOST[:PORT]/PATHWhere USER and PASSWORD are the username and password to access the FTP server,HOST is the domain name or IP address of the FTP server, PORT is the remote portfor the FTP server, and PATH is the path to a file or directory.HOST and PATH are the minimum required fields in the URL, all other fields are optional.The default USER is 'anonymous' with no PASSWORD. The default PORT is 21.# Example UsageList the files in the FTP server's root directory:    $ ./3700ftp ls ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="e89bdb8b9adb9ca88e9c98c68d90898598848dc68b8785">[email protected]</a>/List the files in a specific directory on the FTP server:    $ ./3700ftp ls ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="95e6a6f6e7a6e1d5f3e1e5bbf0edf4f8e5f9f0bbf6faf8">[email protected]</a>/documents/homeworksDelete a file on the FTP server:    $ ./3700ftp rm ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="b3c080d0c180c7f3d5c7c39dd6cbd2dec3dfd69dd0dcde">[email protected]</a>/documents/homeworks/homework1.docxDelete a directory on the FTP server:    $ ./3700ftp rmdir ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="e093d38392d394a0869490ce8598818d908c85ce838f8d">[email protected]</a>/documents/homeworksMake a remote directory:    $ ./3700ftp mkdir ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="eb98d88899d89fab8d9f9bc58e938a869b878ec5888486">[email protected]</a>/documents/homeworks-v2Copy a file from the local machine to the FTP server:    $ ./3700ftp cp other-hw/essay.pdf ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="b6c585d5c485c2f6d0c2c698d3ced7dbc6dad398d5d9db">[email protected]</a>/documents/homeworks-v2/essay.pdfCopy a file from the FTP server to the local machine:    $ ./3700ftp cp ftp://bob:<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="04773767763770446270742a617c65697468612a676b69">[email protected]</a>/documents/todo-list.txt other-hw/todo-list.txt</pre>







 <h3>(Il)Legal Libraries</h3>

 Part of the challenge of this assignment is that <b>all FTP request and response code must be written by the student, from scratch</b>. In other words, you need to implement the FTP protocol yourself. Students may use any available libraries to create socket connections and parse URLs. However, you may not use <b>any</b> libraries/modules/etc. that implement FTP. Obviously, your code is also not allowed to invoke system tools that implement FTP like <i>ftp</i> or <i>curl</i>.


 For example, if you were to write your FTP client in Python3, the following modules would all be allowed: <i>socket</i> and <i>urllib.parse</i>. However, the following modules would <b>not</b> be allowed: <i>urllib.request</i>, <i>ftplib</i>, and <i>pycurl</i>.

 If students have any questions about the legality of any libraries please post them to Piazza. It is much safer to ask ahead of time, rather than turn in code that uses a questionable library and receive points off for the assignment after the fact.




 <h3>Implementation Details</h3>

 In this assignment, you will develop an FTP client. This client must be able to login to a remote FTP server and perform several operations on the remote server. In this section, we will explain how to connect to an FTP server, the format of FTP protocol requests and responses, and the FTP commands that your client must support.


 Modern FTP implementations use TCP/IP as their transport and network protocols. Thus, to connect to an FTP Server, your client will need to open a TCP socket. By default, FTP servers listen on port 21, although users may override the default port by specifying a different one on the command line. Once your client has connected a TCP socket to a remote server, it will begin exchanging text-formatted requests and responses with the FTP server.

 All FTP requests take the form:

 <pre>COMMAND &lt;param&gt; &lt;...&gt;r
</pre>

 COMMAND is typically a three or four letter command, in all caps, that tells the FTP server to perform some action. Depending on what command is sent, additional parameters may also be required. Note that parameters should not be surrounded by &lt; and &gt; symbols; we use those to denote things in messages that are optional. All FTP requests end with r
.


 After each request, the FTP server will reply with at least one response. Some requests will elicit two responses. Additionally, FTP servers send a welcome message after the TCP connection is opened, before the client sends any requests. All FTP responses take the form:

 <pre>CODE &lt;human readable explanation&gt; &lt;param&gt;.r
</pre>

 CODE is a three digit integer that specifies whether the FTP server was able to complete the request.

 <ul>

  <li>1XX codes indicate that more action is expected (e.g., waiting for a file to download or upload);</li>

  <li>2XX codes indicate success;</li>

  <li>3XX indicates preliminary success, but more action is required (e.g., your client sent a username, but now a valid password is required);</li>

  <li>4XX, 5XX, and 6XX codes indicate that an error has occurred.</li>

 </ul>

 More details on FTP response codes can be found <a href="https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes">here</a>. Some servers include an optional, human-readable explanation in each response that explain what happened or what is expected of the client. These human-readable messages are useful for debugging purposes. Responses may also include a parameter that is necessary for the client to function (most importantly, for the PASV command, see below). All FTP responses end with r
.


 Your FTP client must be able to send the following FTP commands:

 <ul>

  <li><pre>USER &lt;username&gt;r
</pre>Login to the FTP server as the specified <i>username</i>. If the user does not specify a username on the command line, then your client may assume that the username is “anonymous”. This is the first request that your client must send to the FTP server.</li>

  <li><pre>PASS &lt;password&gt;r
</pre>Login to the FTP server using the specified <i>password</i>. If the user specified a password on the command line, then this is the second request that your client must send to the FTP server. If the user did not specify a password on the command line then your client may skip this request.</li>

  <li><pre>TYPE Ir
</pre>Set the connection to 8-bit binary data mode (as opposed to 7-bit ASCII or 36-bit EBCDIC). Your client should set the TYPE before attempting to upload or download any data.</li>

  <li><pre>MODE Sr
</pre>Set the connection to stream mode (as opposed to block or compressed). Your client should set MODE before attempting to upload or download any data.</li>

  <li><pre>STRU Fr
</pre>Set the connection to file-oriented mode (as opposed to record- or page-oriented). Your client should set STRU before attempting to upload or download any data.</li>

  <li><pre>LIST &lt;path-to-directory&gt;r
</pre>List the contents of the given directory on the FTP server. Equivalent to <i>ls</i> on the Unix command line.</li>

  <li><pre>DELE &lt;path-to-file&gt;r
</pre>Delete the given file on the FTP server. Equivalent to <i>rm</i> on the Unix command line.</li>

  <li><pre>MKD &lt;path-to-directory&gt;r
</pre>Make a directory at the given path on the FTP server. Equivalent to <i>mkdir</i> on the Unix command line.</li>

  <li><pre>RMD &lt;path-to-directory&gt;r
</pre>Delete the directory at the given path on the FTP server. Equivalent to <i>rm -r</i> on the Unix command line.</li>

  <li><pre>STOR &lt;path-to-file&gt;r
</pre>Upload a new file with the given path and name to the FTP server.</li>

  <li><pre>RETR &lt;path-to-file&gt;r
</pre>Download a file with the given path and name from the FTP server.</li>

  <li><pre>QUITr
</pre>Ask the FTP server to close the connection.</li>

  <li><pre>PASVr
</pre>Ask the FTP server to open a data channel.</li>

 </ul>




 <h3>Control Channel, Data Channel</h3>

 The FTP protocol is a bit unusual in that it requires not one, but two socket connections. The first socket that your client will open to the FTP server is known as the control channel. The control channel is typically the connection to port 21 on the FTP server. The control channel is for sending FTP requests and receiving FTP responses. However, <b>no data is uploaded or downloaded on the control channel</b>. To download any data (i.e., a file or <b>a directory listing</b>) or upload any data (i.e., a file) your client must ask the server to open a data channel on a second port.

 The FTP command to open a data channel is PASV. The client sends PASV to the FTP server, and it responds with a message that looks something like this:

 <pre>227 Entering Passive Mode (192,168,150,90,195,149).</pre>

 Code 227 indicates success. The six numbers in parenthesis are the IP address and port that the client should connect a TCP/IP socket to to create the data channel. The first four numbers are the IP address (192.168.150.90 in this example) and the last two numbers are the port. Port numbers are 16-bits, so the two numbers represent the top and bottom 8-bits of the port number, respectively. In this example, the port number is (195 &lt;&lt; 8) + 149 = 50069.


 The semantics of the data channel are always the same: <b>once the data transfer is complete the data channel must be closed by the sender</b>. What changes is <i>who</i> closes the channel. If the server is sending data (i.e., a downloaded file or a directory listing) then the server will close the data socket once all data has been sent. This is how the client knows that all data has been received. Alternatively, if the client is sending data (i.e. uploading a file), then the client must close the data socket once all data has been sent. This is how the server knows that all data has been received. If the client wishes to upload or download additional data, e.g., perform multiple operations during a single control channel session, then one PASV data channel must be opened per operation.

 Note that the control channel (i.e., the first socket) must stay open while the data channel is open. Once the data channel is closed, the client is free to end the FTP session by sending QUIT to the FTP server on the control channel and closing the control socket.

 <h3>Language</h3>

 You can write your code in whatever language you choose, as long as your code compiles and runs on <b>unmodified</b> Khoury College Linux machines <b>on the command line</b>. Do not use libraries that are not installed by default on the Khoury College Linux machines, or that are disallowed for this project. You may use IDEs (e.g. Eclipse) during development, but do not turn in your IDE project without a Makefile. Make sure you code has <b>no dependencies</b> on your IDE.

 <h3>Suggested Implementation Approach</h3>

 When starting work on this project, we recommend implementing the required functionality in the following order.

 <ul>

  <li><b>Command Line Parsing.</b> Start by writing a program that successfully implements the required command line syntax and can parse the incoming data, e.g., FTP URLs.</li>

  <li><b>Connection Establishment.</b> Add support for connecting and logging-in to an FTP server. This includes establishing a TCP control channel, correctly sending USER, PASS, TYPE, MODE, STRU, and QUIT commands. Print out responses from the server to confirm that each command is being received and interpreted correctly.</li>

  <li><b>MKD and RMD.</b> Implement support for making and deleting remote directories. These commands are simpler because they do not require a data channel. Verify that your client is working by using a standard FTP client to double check the results.</li>

  <li><b>PASV and LIST.</b> Implement support for creating a data channel, then implement the LIST command to test it.</li>

  <li><b>STORE, RETR, and DELE.</b> Complete your client by adding support for file upload, download, and deletion. Double check your implementation by comparing it to the results from a standard FTP client.</li>

  <li>Double check that your client works successfully on a CCIS Linux machine, e.g., login.ccs.neu.edu</li>

 </ul>




 <h3>Submitting Your Project</h3>

 To turn-in your project, you should submit your (thoroughly documented) source code along with two other files:

 <ul>

  <li>A Makefile that compiles your code.</li>

  <li>A plain-text (no Word or PDF) README.md file. In this file, you should briefly describe your high-level approach, any challenges you faced, and an overview of how you tested your code.</li>

 </ul>

 Your README.md, Makefile, source code, etc. should all be placed in a directory. You submit your project by running the turn-in script as follows:

 <pre>$ /course/cs3700f20/bin/turnin project2 [project directory]</pre>

 [project directory] is the name of the directory with your submission. The script will print out every file that you are submitting, so make sure that it prints out all of the files you wish to submit! The turn-in script will not accept submissions that are missing a README.md or a Makefile. You may submit as many times as you wish; only the last submission will be graded, and the time of the last submission will determine whether your assignment is late.





 <h3>Double Checking Your Submission</h3>

 To try and make sure that your submission is (1) complete and (2) will work with our grading scripts, we provide a simple script that checks the formatting of your submission. This script is available on the Khoury College Linux machines and can be executed using the following command:

 <pre>/course/cs3700f20/code/check/ftp_fmt_chk.py [path to your project directory]</pre>

 This script will attempt to make sure that the correct files (e.g. README.md and Makefile) are available in the given directory, that your Makefile will run without errors (or is empty), and that after running the Makefile a program named <i>3700ftp</i>&gt; exists in the directory. The script will also try to determine if your files use Windows-style line endings (r
) as opposed to Unix-style line endings (
). If your files are Windows-encoded, you should convert them to Unix-encoding using the dos2unix utility before turning in.





 <h3>Grading</h3>

 This project is worth 10% of your final grade. You will receive full credit if 1) your code compiles, runs, and produces the expected output, and 2) you have not used any illegal libraries. All student code will be scanned by plagiarism detection software to ensure that students are not copying code from the Internet or each other.


 To test your <i>3700ftp</i> client, we will ask it to perform a number of tasks on the command line, including uploading and downloading files, creating and deleting directories, and deleting files. In other words, we will exercise its expected functionality. If your client does not obey the command line specification then we will not be able to exercise its functionality, and you will lose points. Similarly, points will be lost for each expected feature that is not implemented correctly.

 You can see your grades for this course at any time by using the gradesheet program that is available on the Khoury College machines.

 <pre>$ /course/cs3700f20/bin/gradesheet</pre>




</article>

<footer class="footer navbar-inverted"></footer>

5/5 - (2 votes)

<span style="font-size: 1.618em;">Description</span>

<h3 class="panel-title">WARNING: DO NOT TEST YOUR FTP CLIENT ON PUBLIC FTP SERVERS</h3>

There are many FTP servers out there on the internet. Even though some of these FTP servers are “public” and allow anonymous access (i.e., no password required), <b>we ask that students confine their testing to our server</b>. Since your clients will behave weirdly and violate the FTP protocol during development, you don’t want to send this kind of anomalous traffic to unsuspecting FTP servers, as it may alarm their administrators.

<h3 class="panel-title">WARNING: DO NOT UPLOAD GIANT OR ILLEGAL FILES TO OUR SERVER</h3>

Our server does not have unlimited capacity. If we observe a student uploading large files to the server (say, larger than 10 megabytes), those files will be deleted and we will cut off the student’s access.

Similarly, students are responsible for the files they upload to the server and are expected to exercise good judgement. Do not upload illegal or inappropriate content to the server. Any student that violates this rule will be reported to the university administration, along with copies of the offending files.