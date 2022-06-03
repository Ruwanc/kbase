# Network Models
## OSI Model
Open System Interconnection ModelA conceptual model that categorizes and standardized the different functions in a network.
7 layers

7.Application -> Interract with software applications(web browsers), http,https
		Functions-> Identifying communication partners,Synchronizing communication, Provide process to process communication.
		
6.Presentation -> Data in the application layer in application format. It needs to be translated to a different format to be sent over the network.
		Function -> Trans between application and knetwork format. Eg. encrypttion of data as it is sent and decrypt data as it is received. Also translates between different application layer formats.
		
5.Session -> Controls dialogues(sessions) between communicating hosts. Establishes, manages and terminates communications between the local application and the remote application.(web browser and youtube)

4.Transport -> Segments and reassembleds data for communications between end hosts. Break large pieses of data into smaller segments which can be more esdily sent over the network and are less likely to cause transmission problems if errors occur. Provide host-to-host communication.

3.Network ->Provide connectivity between end hosts on different networks. Provide logical addressing. Provide path selection between source and destination. Router operae at layer 3.

2.Data Link -> Provides node to node connectivity and data transfer(pc to switch,switch to router, router to router). Defines how data is formatted for transmission over a physical medium(UTP). Detects and corrects physical layer errors. Uses layer 2 addressing, separate from Layer 3 addressing. Switches operate at layer 2.

1.Physical -> Defines physical characteristics of the medium used to transfer data between devices.

PDU Types

7,6,5 (Upper Layer data) -> DATA

4 (Transport Layer) -> Segment

3 (Network Layer) -> Packet

2 (Data Link Layer)-> Frame

Protocols: Set of logical rules that devices must follow to communicate.

## 5 Layer TCP/IP model
1. Application

2. Transport 

3. Network

4. Data-Link

5. Physical

Application & Transport = Applications that need to send and receive data.
Network = Delivering data over the entir path from the original source to destination.
Data-Link = Focuses sending data over one type of physical link.
Physical = How to transmit bits over each individual link.

TCP/IP Application Layer
Application layer protocols provide services to the application software running on a computer. Application layer does not define the applications itself, but it defines services that applications need.
Ex:- HTTP defines how web browsers can pull the content of a web page from a web server.
Provides an interface between software running on a computer and the network itself.

TCP/IP Transport Layer
Transport layer protocols provides services to the application layer protocols that reside one layer higher in the TCP/IP model.
	Services provides by TCP
		Error recovery service(Provided to application layer protocols)
		- To recover from errors TCP use concept of Acknowledgements.
		
TCP/IP Network Layer
IP protocol belongs to this layer. IP protocol provides addressing and routing.

TCP/IP Data-Link & Physical Layer
Define the protocols and hardware require to deliver data accross some physical network.

Encapsulation

Application    Data
Transport      Data + TCP Header   => Segment
Network        Data + TCP header + IP Header   => Packet
Data-Link      Link Trailer + Data + TCP Header + IP Header + Link Header   => Frame

## Adjacent-layer interaction
Interaction between different layers of the OSI(or TCP/IP) model. Occurs on one computer, with two adjacent layers in the model. The higher layer requests service from the next lower layer and the lower layer provides the service to the higher layer.

## Same-layer interraction
Interraction between the same layer on different hosts. This allows you to 'ignore' the other layers involved and focus on interraction between a single layer on different devices. The protocols define headers to communicate with each computers and what they need to do.