# OSI Model 
![](attachment/903e5a54805762b4d9cf302728cf8972.png)
Layer 5 is the *Application* layer 
Layer 1 is the *Physical* layer 
# Week 3
## Web 
Internet applications are end system applications (or Processes).
Processes may exchange messages, messages act as input to a process.
Different processes may be running on different end systems. so possibly on different OS's. A process must be able to address another process. 

**Process and Hosts**
An end system (host) may run multiple programs which run multiple processes. 
![](attachment/a5f039a6e3f13191132f5af1fab96cae.png)
A process is addressed within its host by a *port number.*
We can have only one application use one port at any given time on the same machine. If we want to be able to run two applications and each of them run on the same port, we would require 2 IP addresses which requires 2 NIC cards, which is feasible. 

Think of the port number as a door in the house and every door has a number. So we have reserved some numbers for some applications. 
They basically help the to know where to give the data to go to OS.

**WWW**
Invented in 1989 by Tim Bernes Lee. 
Based on idea of hypertext and hyperlinks.
HTTP became extremely successful tho it is just a glorified version of FTP.
Simplicity is what made it popular. 

![](attachment/237b78bc0abb035def3c39d8aaf39b4e.png)

**HTTP**
Uses a connection oriented transport mechanism (TCP).
Although it can also work over *UDP*
HTTP is stateless. 

How HTTP was used back in the day 

So in the first version of HTTP used *One TCP connection per object*. So what it means is say the client wants to get an image, it opens a TCP connection and then gets the image. Now lets say it wants one another image, it should close the old connection and then reopen another TCP connection to get the image. This is pretty inneficient!

So HTTP/1.1 introduced *persistent connections*. 
It used the same TCP connection by the client to issue multiple requests and by the server to return multiple replies and possibly multiple objects. The default behaviour is to use persisitent connections. If the client then says 'close' in the request and response indicates the intention, of the cllient and the server to not use a persistent connection. 

![](attachment/0b15b19b062765b1bfd401d694bbfbe8.png)

This is one way of using the persisitent connection. 
But we can be a bit more efficient by *pipleining* our requests 
![](attachment/45f00ae85df53c4e377b8833cde5a19f.png)

Protocol features 
![](attachment/0df5f1a6cd6cedaabc1d661fc886b70a.png)

HTTP/2 is better 
![](attachment/df9eeb931ccb39b3738caf033f35a1fc.png)

**Request**

![](attachment/ef2132653adbd1a0b28ef346fabf61ee.png)

*Remember:* The two carriage returns at the end is absolutely mandatory. 

**Methods**
GET retrieves the object indentified by the URL
POST allows submission of data to the server. 
HEAD is like GET but you dont get the body. Useful for testing. 
PUT requests that the enclosed object be stored under the given URL. So its what the server uses when we are uploading the file. 
DELETE deletes the given object

**Response**
![](attachment/8bf5e0d87b7fc6db061fe668dc7ab129.png)

Headers for CW
![](attachment/9c458931862077cab04696dcf3f3e716.png)

Status codes
![](attachment/456725a23f6be96ad9c1bdd2ddfcfae7.png)
To memorise
![](attachment/801770024d2ac24b539ca96def2a2d37.png)

## Web Caching 
![](attachment/4130bdf9d9e1e16d59d88c0f06d4cc37.png)

proxy is basically a cache. lol 

Benefits of using proxy/cache 
1. Performance
2. Security. Server sees the proxy as the client as remember the client sends request to the proxy. If the data is not present in the proxy then it would go and ask to the server for the data it requires. Therefore it is more private. 

Problems 
1. latency 
2. complexitiy 
3. Data freshness

HTTP uses *proxy*
![](attachment/7de1259252dfcdceb947ca2efc0821b1.png)
It further supports headers that can request to go and fetch new data from the server host. 

![](attachment/6ff609a5505b361c9806f621e6bfc4df.png)

![](attachment/69bca0def67b8c1822c16d3a20b2e777.png)
Please dont give me cached objects lol.

More control
![](attachment/be1a38f7c8eec5427632eaa0bbc599da.png)

**Cookies**
HTTP is stateless protocol. So how do we implemente a shopping cart, or give it some state?. 
Solution : Cookies

Set cookie header 
	sent within an HTTP response, from the server to the client. 
	tells the clinet to store the given cookie as a session identifier for that site.
Cookie Header
	Sent within an HTTP request, from the client to the server. 
	Tells the server that the request belongs to the given session.

So cookies are basically data stored on the cache that can help the webserver know more about the client. 
It basically gives state to the stateless HTTP.

**Scripting**

![](attachment/c8b608601517c7f06cf4162be80ae385.png)

## CDN
*Problem* How to stream content to hundreds of thousands of simultaneous users?
*Solution* Store/Serve multiple copies of videos at multiple geographically distributed sites (CDN)

Two different types 
**enter deep** Push CDN servers deep into many access networks. Closer to users. Like Akamai. The most popular one. 

**bring home** Smaller number of larger clusters in PoPs (Points of Presence) near but not within access networks 

They do look alike cl. Prob look into it in a bit more depth for the actual difference. But as far as i know for now... the enter deep CDNs would basically mean once the request is sent to the ISP, the CDN would handle it locally. So would not send the request to some other CDN which is in another Network which would be case in bring home. 
Prob Future Jayce can make the above more precise... 

*Example* of how CDNs actually work 
Context:
Bob requests a video at `http://netcinema.com/6YZA`
The video is stored on a CDN at `http://KingCDN.com/224408`
Flow:
1. Bob clicks on the URL for video `http://netcinema.com/6YZA` from the google search results 
2. `netcinema.com` is resolved via Bob's local DNS srver 
3. Bob connects to netcinema but the page returns that the video is at `http://KingCDN.com/224408`
4. Bob's system asks for IP of KingCDN.com but its authorative DNS server is stored instead. 
5. Then Bob's DNS server asks the KingCDN DNS server for the IP Bob requested then returns it. 
6. Bob's system requests the video from the KingCDN server, streaped via HTTP. 

#### CDN cluster selection stratergy 
How does a CDN DNS select a good CDN node to stream.
1. It picks a CDN node geographically closest to client 
2. pick a CDN node with the shortest delay to client 

But how does the CDN know the geographical location of the client. 
You would think of geolocating the user based on their IP address. *BUT* you are *WRONG!* When we ask for IP to the KingsCDN DNS server, who is doing the talking?? it is our local DNS server... READ THE ABOVE STEPS FOR EXAMPLE CAREFULLY. ITS 100% right 

So if we use a public DNS like Google or Cloudfare?  Which would be hosted in America or something, then we aren't getting any benefit are we??

**Alternative**
Let the client decide, give the client a list of several CDN servers, client pings servers and picks best. Useally based on RTT(Round Trip Time). 

#### Netflix Case Study 
Netflix uses their own CDN, OpenConnect 
![](attachment/63bca53e51de2084aa73d17906ac336f.png)

Here is their own version of both enter deep and bring home. OCAs are just Open connect access point. 
So we would get a better Netflix exchange if we are in certain ISPs

## Email 
Its Asynchronous 
![](attachment/38f69eb51efd750912f8208c77608c12.png)

#### Architecture 
![](attachment/8920000b446a56fc07b9eee0321edbe4.png)
Oooo Queue.... Kafka

SMTP
![](attachment/f5293169b9d52f13c0ec051c3a8822a4.png)

# Week 4 - Transport Layer 
## TCP/UDP 
The transport layer provides:
1. reliable connection oriented services  (TCP)
2. unreliable connection less services  (UDP)
3. parameters for specifyting quality of service 

The Transport layer protocols provide for logival communication between application processes 
It runs on end hosts only. 

Transmission Control Protocol (TCP) - connection oriented 
User Datagram Protocol (UDP) - connectionless

Transport Layer's TCP data are called *segments*
Transpeort Layer's UDP data are called *datagrams*

Basic assumptions for the underlying Network layer:
1. Every host has one unique IP address 
2. Ip: *best-effort* delivrery service (It just tries its best)
		no guarantees on the integrity of packet transmission 
		no guarantees on the order in which packets are delivered 
Jayce -> The transport layer tries to fix these two issues. 

Other Layer 4 Protocols: QUIC, UDP-Lite, DCCP/UDP, SCTP, RSVP/NSIS

### Terminology Agreement 
![](attachment/6cd097a779fb06b4dffb86f6551f8136.png)

Int TCP layer the data is broken into TCP segments by a process called (Segmentation)
In internet layer the process is called fragmentation which means a maximum size thats allowed. 

**Multiplexing**
Motivation:
![](attachment/b9f783b8fb4ce47ede936548ba28b012.png)
How would you say these requests apart?

Solution: Port numbers 

Each application running on a host is identified by a unique port number. 
port numbers are simply cross platform process identitifiers 

So how to identify a socket connection 
![](attachment/8c324619eccc043749681edae475a9cb.png)

![](attachment/9185ceea4768166553a9ff5868cbe4e2.png)

Ephemeral for clients basically are the ports which the client would open to connect to some web server. 

Goal of port numbers is to separate between connecting to applications 

### TCP

TCP should offer *reliable* data transfer. NOT SECURE!!! different keywords. dont mess them up. 

Ordered delivery is enforced by TCP. 
*Interesting fact* about this ordered delivery offered by TCP is that lets say we send the following packets `1 2 3 5 6 7` the receiver would receive `1 2 3` and then once it recieves `5 6 7` it basically just throws them away!! so basically we have wasted our connection time with the other computer. 

It further offers congestion control. So if something goes wrong, it avoids congestion within the network. 

The internet's primary transport protocol 

Initiates Connection oriented  service. so endpoints intially shake hands to establish a connection. Not a circuit switched connection nor a virtial circuit. An actual socket!

Full Duplex as well, so can sned and receive at the same time 


**Berkeley socket interface**
Basically an interface that gives us a set of commands which the OS can then map to actual hardware commands for setting up this connnection. 
```
SOCKET -> Create a new communication endpoint
BIND -> Attach a local IP address to socket. 

LISTEN -> Announce willingness to accept N connections (the server starts listening on this socket, thus telling the kernel that it will now wait for connections from clients)

ACCEPT -> Block until some remote client wants to establish a connection (servers are stuck on accept till someone connectes to them)
CONNECT -> Client would run this command to establish a connection. 

After that we can now send/receive messages

SEND -> Send data over a connection 
RECEIVE -> Receive data over connection 

CLOSE -> Release the connection. 
```

![](attachment/dd415d7ed553b873ae1685b945ffedf2.png)

Q. So now whats different when it comes to connection-less communication?
A. There is no connection in UDP, i.e. no need for listen, accept, connect. You can still close the file pointer

Simple Client/Server code in Java 
![](attachment/39bde13ada91e9bfe0c304474af67cd6.png)
*Note* the server code would be blocked in the accept statement. And then we wait for some client to cnnect to us. 
The problem with the above one is that we would need to close the connection before opening the next. So at any point in time it can only serve one client. 
So in order to serve multiple clients at the same time, we would need to use threading.
![](attachment/ac1998f9ca07dbd3dc3dbd1357e9964a.png)

**TCP Segments**
TCP data are transmitted within TCP segments 
TCP segments are transmitted with a Network Layer Protocol (IPv4)
*Maximum Segment Size (MSS)* Maximum amount of application data transmitted in a single segment (headers not included). Typically related to the *MTU* of the connection
*Maximum Transmission Unit (MTU)* Largest link layer frame available to the sender host. 
So well how do we determine the largest link layer frame? With Path MTU discovery (PMTUD ) to determing the largest link layer frame that can be sent on all lnks from the sender host to the receiver host. 

**Inside the TCP**
![](attachment/aae44456bcf96ec05ca5a5723821e457.png)
*TCP Header*
Source and destination ports (16 bit each): application identifier 
Sequence number (32 bits) Used to implement reliable data transfer 
Acknowledgement number (32 bit) again used to implement reliable data transfer 
Receive window (16 bit): size of the window on the receiver end 
Header length / offset (4 bit): size of the TCP header in 32 bit words 
Optional and variable length options field: may be used to negotiate protocol parameters. 

*Sequence Numbers*
They are associated with bytes in the data stream. IMPORTANT: THEY ARE NOT WITH SEGMENTS, ie it is NOT a packet numbering system
The sequence number in a TCP segment indicates the **sequence number of the first byte carried by that segment**
![](attachment/042bc91841a22e5b1e7a309395fc5cae.png)
So these segments would have the following sequence number 
`1 1025 2049 3073`
BUT this is not how it works always 
When the TCP connection is set up, a random *Initial Seqence Number* ISN is decided upon in order to avoid receiveing any leftover segments by mistake. 
So lets say for the above example we have an *ISN* of `69`
Then the sequence numbers would be 
`69+1 69+1025 69+2049 69+3073`
resulting in 
`70 1094 ....`

*Acknowledgement number*
An acknowledgement number represnts the first sequence number not yet seen by the receiver. 
*Note* its not the last sequence number seen by the receiver, but the first one it expects to see next!!
Important detail!
Lets look at an example to make this a bit more clearer 
![](attachment/c1899638e00e01171c21548cf0c74f78.png)
So first off we can see the ISN is 1200. Nice! 
Now notice the second line, we are sending bytes from `2200 - 2699 (inclusive)`
So the Ack returned back is `2700` Which is the first byte B requires to see next! 
Nice!

So now remember TCP connnection consists of a full duplex link, therefore there are two streams. i.e two different sequence numbers 
![](attachment/a825d4b9c97bf218e28af1f5fd9fbeb8.png)
We can see how the Seq and Ack are being interchanged by each one acknowledging oh i have that byte and then requesitng can you give me the other byte. 
Note, the acknowledgements are just piggybanked on the actual segment header 

**Three way handshake**
The client sneds a TCP segment with the SYN flg set to true in its header. And also its initial sequence number (so agreeing on its ISN number)

The server responds with anoter SYN TCP segment, which also has the ACK flag (Acknoledgement) set to true and the first unseen client SEQ number (Acknowledging it has seen yours). As well as an initial sequence number for the server.

Finally the client responds with an ACK (Acknowledgement) including the first unseen server SEQ# and the clients new SEQ#

For disconneting, we would use a FIN rather that SYN 
![](attachment/fbb6aeb5c0193a7553fe3e51c1b4c37b.png)

With numbers it would look something like this 
![](attachment/259cdd3e7c35b230933de84f59542d1d.png)

![](attachment/9ff0638abd7cbc94c09f5879ee8c5475.png)

### UDP 
UDP provides only the two most basic functions of a transport protocol. 
1. Application identification 
2. Integrity check by means of a CRC type checksum 

UDP is simple:
1. no flow control 
2. no error control 
3. no retransmissions 

UDP datagrams cannot be larger than 65k
No need to segment them... so dont seq number 

In practice 500 to 1000 Byte datagrams are used, the smaller thay are, the more likely they are to make it intact!

Q. Why do we not just use IP instead
S. Becuause UDP adds an extra header on top of IP header which encodes the PORT NUMBER.

Where would we ever use UDP?
1. Finer application level control over wat data are sent and when, (real time, Skype)
2. NO connection establishment (faster than TCP)
3. No connection state 
4. smaller packet header overhead

Besides real time apps, we could use UDP for small requests, like DNS query

### QUIC
Meant Quick UDP Internet Connections *Remember: Never write connection with UDP in CW or exam cause REMEMBER: UDP is Connection less. Its TCP connection or UDP data exchange*
Implemented by a google engineer. 
Get best out of both worlds 
General purpose, is to replace HTTP. 

### FSM 
Finite State Machines 
States represent the state of a protocol.
Transitions are characterised by an event/action label.
`event: typically consists of an input message or timeout`
`action: typically consists of an output message`
![](attachment/f479502b8468ed38084db8d7c67dacb5.png)
So why is there a fin_wait2?, well the reason is because we have completed talking but the other side hasn't completed yet... we need to wait for them to send a fin and we need to acknowledge it. 

The other side 
![](attachment/ef0fefa89f8702bee803d2c90e30b243.png)

### Noisy Channels 
Reliable TCP over a network with bit errors. Ever so often a bit might be modified during transmission (a bit might be flipped) however no packets will be lost. 
Therefore the reciever must be able to know when a received packed is corrupted (ie. contains a flipped bit).
Then the receiver must be able to alert the sender that a corrupted packet was received. 
Sender must then retransmit corrupted packes.
We can do this by using Acks! 
![](attachment/db09bd9c736d7bb7dc68d72bd85498a5.png)
here is the other case, where the ACK may not be sent properly
![](attachment/2f6e0ebf21594728d9aeaf35274361f6.png)
But now if the timeout is a bit too *long*, we would loose valuable time 
And if it is too *short* we might receive overlaps :( like the example below 
![](attachment/4f4d4bfce1c03047e5006a2a8fe1d2d0.png)
We are wasting bandwith!
There are some more stuff in the slides, i.e. the state diagrams of how these connections actually work. You might wanna look into that if you are intereseted future Jayce!

## Headers
*Note:* For ethernet (layer `2`) The checksums are at the footer. 
![](attachment/daecc042a8439356d38336f94b2f3b90.png)

### Detecting Congestion 
If all traffic is correctly acknowledged, then the sender assumes there is no congestion. We know that!! 
**Congestion** means that the queue of one or more routers between the sender and the receiver overflow. 
The server assumes that the network is congested when it detects a segment loss (TCP Reno, a flavour of TCP)
1. Time out (i.e no Ack), here we don't really know if the other side is alive or not. 
2. multiple acknowledgements (equivalent to NACK), so this means the other side is alive but for some reason they can't receive what i am sending.

**TCP flavours**
There are different algorithms that are used for TCP congestion based on the flavour of TCP used. 
Here are some 
![](attachment/ea1a3904030d617475abe761c80c3c91.png)

These algorithms combine various characteristics. e.g
1. Tahoe: Slow Start, AIMD, Fast Retransmit 
2. Reno: Fast Recovery 
3. Vegas: Congestion Avoidance

The TCP protocol described so far is often referred to as **TCP RENO**
![](attachment/4e0740559673395bf8e5dac379a12d18.png)

COLD 

Another popular implementation is **TCP Vegas**

**GOAL**
1. The goal is to detect congestion before losses occur 
2. Imminent packet loss is predicted by observing the RTT (Round Trip Time)
3. The longer the RTT of packets the greater the congestion in the routers. 

TCP Reno checks for RTT usually its just `latency * 2` It starts by sending `x` bytes and records the RTT. If you have a really good RTT, then it means you have a really good connection to the other side and it keeps increasing the window of oppurtunity so you can send more bytes and more bytes. 
But if you have a bad RTT, TCP Reno says ohh you are really slow, we can't trust you, so sends even less bytes. 
*Problem* with the above approach is, imagine you have a really poor internet connection. So the packets are being sent really slow... increasing the RTT, so less packets are also sent. Thus slowing the connection even more!

**TCP Cubic** solves this problem by making window increase a function of time rather than RTT. 
Nice!!

Some terms 
**Congestion Window** 
The sender maintains a Congestion Window (W)
Congestion window is how many bytes I can send before waiting for an acknowledgement. So like a window. That number is an *INTEGER* 
![](attachment/b4fe7a29794011239cf1f2666c432c0c.png)

So its basically like the space in between where it can send some more bytes of data before stopping and waiting for acknowledgement to free up the window. 
So if `W = n bytes` Then the number of packets that can be sent before really waitining for ACKs is `n / MSS` where MSS is the maximum segment size. 

So for a concrete example 
```
Assume 
MSS = 1000 bytes
W   = 4000 bytes

Max packet in flight = 4000 / 1000 = 4 packets. 

Sender sends packet 1 
Sender sends packet 2 
Sender sends packet 3 
Sender sends packet 4

Now window is full 
AKA Last byte Sent - Last Byte Acked == W 
So we wait. 

Now when Ack for packet 1 arrives.
Sender receives ack for packet 1
Now our window has an empty space 

We can even relate to this notation 
Last Byte Sent - Last Byte Acked <= W 

So we can send another packet 
Sender sneds packet 5 

Now the window is full agian. 
```
Nice!

![](attachment/2cf1a1d4a54db90e1ae49ecf627bbb87.png)
We will look at Congestion Window and Receiver Window in a bit. 

**Action**
So how does congestion control work in action 
1. Slow Start
	1. Initially the window size should be quite small, which is perfect as it increases rapidly 
	2. Initial value of W is *MSS*, Remember MSS is the maximum segment size, which is quite low for modern high speed networks. *Note:* So over here as W is initialised to MSS we can simply say by doing `W / MSS = MSS / MSS` W holds the number of packets that can be sent in the window. 
	3. Now to quickly get to a good throughput, TCP increases its sending rate exponentially for its first phase (W is doubled every RTT).
		1. Increase W by `1 MSS` on every good segment Acknowledgment (number of acknowlegements we get would be = W  as remember W is the `number of packets` here, as its initalised to be `MSS` ; so we are technically exponentially increasing W.) until `W >= Slow Start Threshold (ssthresh)` or until a congestion occurs. 
		2. If `W > ssthresh`, use a Congestion Avoidance, so move from using exponential to linear. 
		3. Initially we simple set `ssthresh` to `infinite` 
		So the way it works is first we set `ssthresh` to `infinite` and `W` to `MSS` and then we run the following loop 
```python
# Initial state
W = 1 * MSS
ssthresh = LARGE_VALUE

mode = SLOW_START


while connection_alive:

	send up to W bytes of data

	when ACK received:

		if mode == SLOW_START:
			W = W + MSS      # grow fast

			if W >= ssthresh:
				mode = CONGESTION_AVOIDANCE

	if packet_loss_detected:

		ssthresh = cwnd / 2
		W = 1 * MSS
		mode = SLOW_START
```
This below picture describes it in a bit more detain 
	![](attachment/62a729aeeb3e6c7619ff18483998c7f1.png)
So we got upto `4` but now someting bad happens. So we move to congestion avoidance. 

2. Congestion Avoidance 
	![](attachment/64eb35194fb7959479562be60b5678ff.png)
	So *Note* now that we are just incrementing W by 1 every RTT instead of every Ack received. So becomes linear now. 
	The *formula* is done per **ACK** received itself. Really important, dont misunderstand the formula only works per RTT. For per RTT you can just add the MSS directly. *The MSS / W takes care of this when applied per ACK*
	This process continues until something happens agian. 
	Example 
		Suppose W = `14600` and MSS = `1460` then the sender increases W to `16060` after `10` acknowledgements. Why is this so? 
		![[Pasted image 20260502193856.png]]
		so with that we can easily derive 
		![[Pasted image 20260502193926.png]]

3. Additive Increse and Multiplicative  Decrease 
	Stands for AIMD
	![](attachment/6b832c47ae9012d5a00d6aaf67673928.png)
	So basically after SS -> CA, if we again find some packet loss then the TCP would half the congestion window. 
	
4. Reaction to timeout events (based on protocol algorithms)
	TCP provides reliable data transfer using a timer to detect loss segments. 
	timeout without an ACK -> loss segment -> retransmission 
	How long to wait for acknowledgements?
	Timout interval T must be alrger than the RTT, so as to avoid unnecessary retransmissions. 
	However, T should not be too far from RTT, so as to detect and retransmit lost segments as quickly as possible. 
	TCP sets its timeouts using the smoothed RTT (SRTT) and the RTT time variation (RTTVAR):
	![](attachment/5a7155810e1b64be057d32cf9ff37b99.png)
	TCP controls its timout by continuosly estimating the curernt RTT. 
****
Ok so now lets say we identified something went wrong and we need to retransmit. 
How do we doo that?
**Fast Retransmit**
Agreement: `3` duplicate ACKs are interpreted as a NACK (Fast Retransmit)
*Note:* both timeout and NACKs singal a loss, but they say different things about the status of the network.  A timeout indicates congestion. While a NACK is just saying the sender is certainly alive and is responding.. its just that it hasn't received its data yet. 
![](attachment/a3eb4bc974daefed178bcc56702e56f6.png)
*Note* the four identical ACKs. basically 3 duplicates of the first proper ACK. 

**Fast Recovery**
![](attachment/6f3e42aaaed88564384d34a0bdd0ab75.png)
*Note:* Timout starts all the way from SS, while NACK just does the AIMD thing. 
This graph summarises what we know till now. 
![](attachment/d7ea3db2a248a5ae5ef92cba31b07276.png)
*Note:* Every time we have a SS, the grap is exponential. AS EXPECTED
EASY!!
****
**Sliding Window**
Instead of a normal window, some special people might prefer using sliding window. 
![](attachment/e03402b6f63e9441f27dc9221dae1363.png)
Its harder to implement but does increase performance. Again for special people.
![](attachment/31e0ff0d01e156a9208128f9693325ca.png)

### Flow control vs Congestion control 
We have seen congestion control till now. 
**Congestion control** aims not to overflow the *network*.
**Flow control** aims not to overflow the *receiver*. 
So now we as senders have a **congestion window** to know when to wait for ACKs and not full send our data to the internet. 
The receivers can have a **receiver window** to let the sender know if it is full and can't take it anymore. 
![](attachment/c25c12e57995d06e3127c87e6bf5e5ed.png)
The receiver along with the acknowledgment sends the maximum number of bytes that may be sent (Receiver window)
That's why remember we initally set W to be the min of congestion window, receiver window. 
A window size of `0` is legal, it means that no more segments can be sent. *Ping* is still allowed 
### Wireless TCP (pre-WIfi)
![](attachment/0677e0b8d0986e4a07cedeaed334cb5c.png)
### Network Usage 
**Utilisation factor**
![](attachment/c2f6b9fd644b8b4ac4f30dcf35169450.png)
Exmaple:
![](attachment/320f637f64bdf31aa0e3d162e562f527.png)

We have one more formula for the same thing but different numbers provided
![](attachment/c58846d6f33592d14184d1855d6d0fed.png)

Final practice question 
![](attachment/35e60b8b841d4955ee9d88b48b9778d8.png)
My ans: `SS -> CA -> NACK -> CA -> Timeout -> SS`
Its correct, lets goo chat!!!
# Week 5 - Security 
Part one notes are not taken! Oh noo!
Lets say a user wants to access a resource through the internet. 
![](attachment/fdc38dfd6c58082d3443b7b41dbb3ed7.png)
We need to first maintain *access control*. 
Only certain users are allowed access to resource 
*Authentication* User knows that the resource is exactly what it says it is. Vice versa
*Confidentiality* Users limit access to information/resources they own. Data confidentiality. 
*Integrity* Action of users should not be able to affect the overall integrity of the resource. 
*Non repudiation* Users can't deny communication took place. 
### Access Controls 
There is a *guard* sitting infront of the request to invalidate requests that are from different geographical places 
![](attachment/a83b74f61cce72916392558e41f52949.png)

So what are these guards, well it can be Firewalls
*Firewalls* control access to the network, a security gate between the internal and external networks. 
![](attachment/72895c4037a4f4dadf2ff32f81c01a95.png)

**Firwall Components**
1. Application level gateway: runs on the host, only protects that host 
2. Proxy server: runs on the network, can protect the entire LAN 
3. Circuit level gateway: acts like a non-caching proxy, it fully takes over the host's communication with the recipient and then decides what to allow and what to block. 
4. Packet filtering:
	1. stateless: checks source/destination IP addresses and source/destination ports 
	2. stateful: remembers connections and checks contents of current andprevious packets

So without a firwall anyone can attack you!
![](attachment/5e314b54c23e073006105051547ffcbe.png)
Words from future Jayce: Well this is the case when there is no NAT in play. If there is a NAT in play then the above statement doesn't really hold 

But with proxy 
![](attachment/d4331a564e253c12d047d9f1d9301c29.png)

**Bastion host**
Bastion host in the above example is just a proxy. 
So its a system that expects to be attacked! 
It performs auditing/logging. It runs a trusted/secure OS. Adminstered via a dedicated terminal. 
![](attachment/1bb74c155cd7efa21bbbc640a1f3f8ad.png)

![](attachment/2fd316532ea4c7541780bd182f81c133.png)

**Access Control Lists (ACL)**
Basically a list of rules that suggest how data should transfer between packets. 
![](attachment/965e297b65c4a8459f76ef9eb809c181.png)
Example of an ACL in action
![](attachment/48864660877603341b07ed0754899a46.png)

**ip tables and tcpd**
![](attachment/d3c5cf9d868aadaa9053c66ff3c83164.png)

**Types of firewalls**
1. IDS: Intrusion detection system -> Software that detects intrusions but does nothing to stop them. except inform the system. 
2. IPS: Intrusion prevention system -> Works with IDS but prevents them as well if IDS detects anything. So actively blocks SYN flooders 
3. NGFW: Next Generation Firewall -> A stateful firewall that came with an IPS/IDS system in addition to ACL mechanisms. 
4. UTM: Unifieid Threat management -> Similar to NGFW but with more capabilities (e.g. antispam, antivirus)

**DMZ**
De militarized zone. 
Basically the area between you and the outside dangerous zone. External hosts can only speak directly to your internal hosts that lie within the DMZ (if any). 
All other non DMZ are protected by gateway /router/firwall
The router uses NAT, to get the external messages to the correct internal host. 
	So routers come with NAT and what they do is they transform your private IP into a public IP and then fetch data and then send back the data that was fetched to your computer! So they are the intermediate. 
	So NATs convert private to public ip 

**Port Forwarding**
This lets the router know that packets for certain ports should be forwarded direclty to an internal host/port. 
So for example we can say, any packet received at port 12345 of the router's public IP, should be forwarded to the NAT based LAN IP of host1 at its port 80 
Basically of vs code live share works 
So basically port forwarding prevents exposing the entire computer, instead only forwards the port that is required. 

### Confidentiality and Data Integrity
**Cryptography**
Like simple shift algorithm 
like cipher 
![](attachment/92d78d45b750ac0a0466b77b0b0fd5f9.png)
But how do we share the key?

**Symmetric encryption**
Basically ` K = K^-1` 
Like if i encrypt the file with a given key K like some sort of password, then i need to share that password with ou so you would be able to decrypt the file. .. Nice!
![](attachment/1763e0d13799b48902c9909fac0265c6.png)

**Public key encryption**
We have a public private key pair. 

So for validation we have the following happening 
![](attachment/9604d2ea6fd5a67c3dcb6570d3170940.png)
So if I encrypt with my private key and send it to lets say X. X can now decrypt with my public key and validate that the message came from me. yay!

And for ensuring confidentiality
![](attachment/6ef94866bbfe9a456ad79cd2bc8d7d77.png)
we can do something even more smart
We can encrypt the data with the destination's public key and then now the destination is the only one that can decrypt the message with their private key. Lets goo!

We can mix them both to have both validation and confidentiality. 

**Encryption Comparision**
![](attachment/6efc98916b7cca37f195af47a8fc8d2e.png)

**Secure Channel Establishment**
Lets say I dont have a file to share but what if we want to obtain these keys over a sepcific channel. Like if we want our website to be secure, how do i share the key with everyone? 

We can't require that all hosts must be given all the keys they may need in advance as it will not scale really well. 
It even doesnt allow new hosts to be added. 

*Solution A:* Agree on a new key right there and then 
*Solution B:* Use trusted secure hosts

Lets look at both the solutions 

Solution A is basically the **Diffie Hellman key exchange**

**Diffie Hellman Key Exchange**
Secure key exchange, even over a public, unsecured channel. This is what SSL and TLS are using in its secure key exchange 
A channel is considered unsecure unless you are using a secure protocol but to use a secure protocol you have to agree on a key to make the proctocol secure to actually use the security. 

Example of how it really works 
```
1. Bob and Alice agree on a public value g(generator) and a large prime number p. (Note: These can be sent through the internet and doesn't matter if the hacker intercepts these and eavesdrops on them.)
2. Bob chooses a secret value b and Alcie chooses a secret value a. (both a and b must be less than p)
3. They each use their secret value to calculate their public value:
   x = (g^b mod p) for Bob
   y = (g^a mod p) for Alice 
   They exchange these public values. (Again doesn't matter if the hacker intercpets these )
4. They then use the otehr's public value to calculate the shared secret key:
   Bob: y^b mod p = (g^a mod p)^b mod p = g^(ab) mod p 
   Alice: x^a mod p = (g^b mod p)^a mod p = g^(ba) mod p
   this result/key can now be used by them to communicate securely. 
```

The hacker doesn't even know the secret key aka `a` and `b`. They need to make guesses which by the time they actually make it, the connection would most likely be closed!

Example 
![](attachment/55339d51b27ca668f99db5ae9e1ee7fa.png)


Now Solution B is **Secure Hosts**
![](attachment/8341da78cee6ee4632c30736eb7f1c7a.png)

Example of such a Secure host is Kerberos 

**Kerberos**
Kerberos is a user authentication system which knows your password. 
It also knows the passoword of the user/resource you want to communicate with. 
Its Key distribution center can provide you with a ticket. Allowing you communciate with that user/resource. 
Tickets expire after a predefined amount of time. In which case you have to relogin to generate new tickets. 
Yt video link: [MicroNugget: How Kerberos Works in Windows Active Directory | CBT Nuggets](https://www.youtube.com/watch?v=kp5d8Yv3-0c)

Summary of it. 
![](attachment/4c4075f2f2e84629056a90e9ee43e43d.png)
So first of all, all the tickets are stored in the client. 
And interesting enough we have 2 tickets. I'll explain from where do they come in a bit. The Tray lives in memory so if we ever encounter a crash, the tickets would just disappear. 
*Authenticator*
So first when the client wants to access a file. The client would obviously have their own password. So they would encrypt a portion of the request with their password. 
Then they would send it to the KDC. The KDC would know the client's password (pink) and then the KDC would decrypt the file and if it succeds it knows for sure the request came from the client! It now doesn't require this authenticator so it would replace it with a Ticket Granting Ticket.
*Ticket Granting Ticket* 
Also known as TGT. The KDC now would simply  encrypt portion of the ticket with its password (blue) and return it back to the client. 
This TGT would have a timestamp on it like 8hrs before expiration. 
*Real Ticket*
Now the client has the TGT and when it wants to access the file it would send a copy of the TGT to the KDC. This KDC would be able to decrypt with its password and if it suceeds it would know it was the one that generated that TGT and would return back a Ticket with portion of it encrypted with the file server's password. 
Now the Client has the TGT and the real Ticket in their tray. 
Finally the client would then send a copy of real ticket to the file server, which the file server would check with its password. If its valid then the actual file is returned. 

Back to encryption 
**Hashing**
The hashing funcction shouldn't be inversible basically. 
![](attachment/39bd166306a28aa4a735102f03cd3f47.png)

# Week 6: IP
### Intro
Data travels in datagrams here
Data are fragmented into packets and a layer 3 header is added in front of each. 

Then these travel over a series of interconnected networks 

WE have a problem. 
![](attachment/8f1968fa4488c37f3a0c24690b7cd3e3.png)

There are different networks with different protocol stacks and somehow they need to talk to each other... !
The solution is *NOT* to force all the networks run the same protocol stack.  
But the solution is to construct a *gateway* which would be able to talk these two different protocols. 

### Network Terminology 
1. PAN: Personal Area Network. e.g. your phone connected to your PC via bluetooth 
2. LAN: Local Area Network 
3. MAN: Metropolitan Area Network. e.g. the bus stop signs that display live bus schedules 
4. WAN: Wide Are Network, e.g. the Internet. 
5. Repeaters (a.k.a Hubs) live at the *Physical Layer* (Layer 1) for boosting signals, they just repeat everything they hear to everyone.  
6. Switches and Bridges live at the *Data Link Layer* (Layer 2) to make interconnections decisions are based on MAC address. So it is more smarter than repeaters. It uses the MAC address to know which port to forward the frame to. 
7. Routers also known as Gateways like at *Network layer* where we are today! yay! Their decisions are based on IP addresses. Transport Layer Gateways and Application Layer Gateways also exist. 

We require a router to connect to any IP based Networks.
What IP based network would we all want to connect to, the INTERNET!

### The Internet
A collection of Autonomous Systems connected together by backbones. 

### Internet Model 
An application offers a data stream to the Transport Layer, using either connection oriented or connection less services. 
The transport layer data stream is converted into an IP datagram for the network layer. 
IP dattagrams are routed through the internet.. Routers pass datagrams to the underlying Data Link Layer (e.g. to LANs and WANs). Then the cables take over (Physical layer). Basically IP is an essential bridge between data and `0`s `1`s. 

### Interntet Network Layer
**IP** performs 
	datagram format 
	fragmentation 
	addressing 
	packet handling 

Internet Control Message Protocol (ICMP) used for error reporting / signalling 
Dyntamic Host configuration Protocol (DHCP) which decides on your IP address dynamically. 
And finally routing protocols, defines paths and creates forwarding tables. RIP, OSPF, BGP. which helps you send the datagram. 

### Fragmentation
Routers must handle cases where size of input datagram exceeds the Maximum Transmission Unit (MTU) of the output link. 
In those cases, The IP datagram needs to be fragmented. 
The destination reasssembles fragmeneted datagrams, not the intermediate routers.. mainly to push complexity out of networks. 
Datagram may have to be fragmented further along the path 
Fragmentation scheme must ensure that intermediate routers can fragment datagram to whatever level necessary. 
![](attachment/8e02ba1a69c25e562a3b41638e3442eb.png)
To know if more fragments are expected to follow, the more fragments flag is set to 1. 
And even *Note:* How many actual number of 8B packets can we send.. it is`8191` BUT it is `8190`. 
Simple example 
![](attachment/03ce0d035333354ab201afdf3c58d45f.png)
The reason we are getting 61 in the second line is because of incorporating the header length in as well. 
`(508 - 20) / 8 = 488/8 = 61` 

**IPV4**
4 billion possible IP addresses. 
`XXX.XXX.XXX.XXX where XXX is from 0 to 255 aka 256 values`
So an example of IP address would look like 
`123.1.1.0`
`123.138.207.160`
Each IP address is associated with *interface* like NIC (network interface controller), not host. Host with more than one *interfaces* may have more than one *IP addresses*. 

Assign addresses with same prefix to interfaces that belong to same organisation. e.g. the network IP address groups all addresses on that network.

We intially had classes that took care of IP addressing. 
![](attachment/283c7dcc87b774a4c32a17320487820f.png)
That was claseful IPv4. Back in the good old days were only universities were talking with each other.

Now we use *class less*
Network IP addresses managed by non profit Internt Corporation for Assigned Names and Numbers (ICANN) to avoid conflicts.
Taken care by subnet masking. 

**Subnet masking**

![](attachment/889a1905cd80a1258d3c9145b8d6b3d6.png)

**Network addresses prefix length notation: address / prefix-length**
This notation is called *Classless Inter Domain Routing* (CIDR)
So for example we have the IP address 
`123.1.1.0/24 -> which means take the first 24 bytes for Network address and the rest for host address.`
So in the above example it would be like `123.1.1 is the network address and 0 is the host address` Remember, even the `1` after the dot is basically a `0 -> 255` value so `log(256) = 8` Therefore between each dots you jump with `8` 
`8.16.24.32` if you know what i mean. 
So if we have `128.138.207.160/27` the `27` would fall in the last item after the dot.  

So for calculating the range of hosts avaliable for a given IP address, all we need to do is 
![](attachment/71a5e9b864765bb9d9b900ba145b0b5a.png)
Simple! 

Net Masks 
We know network addresses are made up of `address/mask` 
Prefix of length p corresponds to mask 
![](attachment/4406b968a8db14f9ab2f3ffc4ad752ae.png)
So for example 
```
128.138.207.160/27 = 128.128.207.160/ 11111111 11111111 11111111 11100000
```
So its simply a mask that says how much of the ip can i use to get the network address. 
![](attachment/93ac4c0b4a82c54ab2f148cb84f39112.png)

So now this is way better than using classes. 
We can specify the number of bits that needs to be reserved for the number of hosts we have in that company. 
Say we want `65k` hosts we would have `128.138.0.0/16`. The number `16` is calculated by doing `32 - ceil(log(65k)) (Made by The Goat Jayce) `
So say we want to calculate the slash number for 200 hosts. We would do 
`32 - ceil(log(200)) = 24`

![](attachment/2ee77d45d8ce0e5406365179888a2d11.png)
Important things to *NOTE* from above image. 
Getting the Network address is easy. Just make every bit reserved for host `0`.
BUT now what if we make them all `1`, this address is not usable but it is the *Broadcast address.* The last usable one is broadcast - `1`. 

### Allocation of address blocks 
So say we want to send da packet from our ip address to another IP address in some other network. Its the router's job to use the subnet mask to check if it knows where is the network present. 
![](attachment/331516c2586c627beb2826a53bb9d2a1.png)

In order to do that they use the longest possible prefix, in order to select the correct network. 
![](attachment/3b210da6cd2819b7162b28b7add0b652.png)

The forwarding table is collection of physical ports of the router, not the Layer 4 ones. 
We will look into some algorithms as well. Stay tuned!

### Dynamic Ip address
There is one more problem, how does a host safely decide on its own Ip address? 
It can't just randomly guess one as doing so would mean it can conflict with someone else's ip address. 
Solution is the *DHCP*
![](attachment/afa890119e9b11589886fdb71255a4f4.png)
Using this dynamic Up address stuff prevents our client from becoming a server, as the IP address keeps changing. 
We can even request for a static address from the DHCP which would be more expensive tho.

### NAT (Network Address Translation)
![](attachment/a499a065eb87647d46e695ec74caccc7.png)
So basically my phone and my computer have different local IP address from the router they have the same global facing IP address. if that makes sense. 
The router would internally maintain a table, saying which requrest is to whom. So it is perfectly valid for my local computer in my network to have the same IP address as some other client in their own network. 
Its just the global IPs should be the same. 

So to summarise better 
When someone wants to reach you, they are reaching to the Ip address of the router which is assigned by the ISP. 
But now if you want to connect to internet, you would connect to the private part of the LAN that the router has and you go via the router.
When you connect to the router, you get a new IP, *not* from your ISP *but* your router uses DHCP service to give you an IP within the private LAN. 
So now when you want to connect to the internet, the *NAT* would would transoform this DHCP router based assigned local IP to the public IP which is used to connect to the internet. And gets it back and remembers that it is you and sends it back to you on your private IP. 
This is what happens at home, we connect phone, laptop or desktop to our router using the same public facing ip address but all your devices would have different local ip addressses which are unique within your home network. 
![](attachment/09033c6ab685d9d8173069a75830c3a1.png)

![](attachment/0ba341582294ddc4fda439baa7102a0c.png)

![](attachment/9d71850fdd972d4640268910cae4b167.png)

![](attachment/593277f597cfb5f4524604ef3971f62f.png)


**ICMP**
![](attachment/97c72f2215f10fc3af8116c442104026.png)

### Ipv6
it solves the above issues of not having enough Ip addresses.
![](attachment/739a7057b20550c4e6f5df2290a05c69.png)

![](attachment/a54485eed30dad6ed24fc8e2c136ddbe.png)

Fragmentation is gone!
![](attachment/efbea3c867995183fd7e3050663aa1bd.png)

![](attachment/3c32fa61852e298835e7bb92de1aef3c.png)


# Wireshark 
Wireshark is an open source network protocol analyser. Captures network traffic and stores data for offline analysis. 

### Why would we want to catch network traffic. 
1. Troubleshoot performance issues of a network 
2. Analyse the contents of network transactions 
3. Trace connections 
4. Identify bursts of traffic in the network. 

### Limitations of Wireshark. 
1. Users need a good understanding of network protocols. 
2. Cannot be used as intrusion detection system. 
3. Cannot always decrypt encrypted traffic. 

### Careful use of Wireshark. 
You should only monitor traffic on networks that you own!
Dont run wireshark on Imperial networks! 

### What can Wireshark Capture?
Connected to a Hub.
1. Local traffic 
2. Broadcast and multicast packets 
3. entire network (if on promiscuosu mode)

Connected to a switch 
1. Local traffic 
2. Broadcast and Multicast packets 
3. Network connected to the same switch port 

Connected to WLAN 
1. Local traffic 
2. Broadcast and Multicast packets 
3. Entire WLAN(if on promiscuos mode)
4. All wireless packets in range (if on monitor mode)

