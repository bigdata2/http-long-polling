# HTTP Long Polling Stack Server

###Dependencies
The stack server is implemented using Twisted. Twisted is an event driven network programming framework written in Python. In order to run this program you will need Python version 2.7.11 or later and Twisted version 16.4.1

You can installed twisted using pip by issuing the following command:

'sudo pip install twisted'

To run the program execute 'python server.py' and leave the shell runnning. The stack server is now listening for incoming TCP connections on port 8080 and will start processing the data received.

To run test-cases you need to Ruby version 2.3.0 or later. If you have Ruby instaled checkout rvm [https://rvm.io/]. You can then run this test suite as follows:

'ruby stack-test.rb'

### Stack server
A http server that provides a LIFO stack, providing push and pop operations. The server listens for requests from clients connecting over TCP on port 8080. The server responds to the request and then close the connection.

A push request pushes the given payload onto the stack. However, the stack can have no more than 100 items on it. Push requests for a full stack blocks until stack space becomes available. 

A pop request returns the top item from the stack to the client. If the stack is empty, the pop request blocks until an item becomes available on the stack.

Both push and pop requests are served (and their associated stack operations performed) in the order in which they arrive fully. Note that this is not necessarily the order in which the server accepts TCP connections: some clients may be very slow to write their requests. Clients that connect later may 'overtake' slower clients that are still writing their request. Those fast clients should get their response before the slow clients.

The server does not juggle more than 100 clients simultaneously. Additional client connections are rejected by sending a single byte response indicating busy-state and then immediately disconnected. 

However, to prevent deadlock (eg, 100 pop requests all waiting for a push request that is always rejected) the server must free up resources under specific conditions. If the server is already handling 100 connections and a new client tries to connect, it must disconnect the oldest client, provided their connection is older than 10 seconds. The server should only disconnect an old client when provoked by a new incoming client connection that would otherwise have to be rejected. It should not just disconnect old clients for no reason.

A push request format is as follows. The first byte is the header. The rest of the request is the payload. The most significant bit in the header byte is 0; the 7 remaining bits are the length of the payload, in bytes. (As such, the minimum size for a push request is 2 bytes: 1 header byte and 1 payload byte. The maximum size for a push request is 128 bytes: 1 header byte and 127 payload bytes.)

The format of a pop request is a single byte with the most significant bit set to 1. The rest of the byte is ignored.

The format of a push response is 1 byte, all zeros.

The format of a pop response is 1 header byte, with the most significant bit set to 0 and the rest of the payload indicating the payload size. The rest of the response is the payload indicated number of bytes of payload.

The format of a busy-state response is 1 byte, value 0xFF.
