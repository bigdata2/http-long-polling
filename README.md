# http-long-polling

##Dependencies
The stack server python program (server.py) is implemented using Twisted. Twisted is an event driven network programming framework written in python. In order to run this program you will need python version 2.7.11 or later and Twisted version 16.4.1

You can installed twisted using pip by issuing the following command:

'sudo pip install twisted'

To run the program execute 'python server.py' and leave the shell runnning. The stack server is now listening for incoming TCP connections on port 8080 and will start processing the data received.

To run test-cases you need to Ruby version 2.3.0 or later. If you have Ruby instaled checkout rvm [https://rvm.io/]. You can then run this test suite as follows:

'ruby stack-test.rb'

