
## Socket Programming

Multithreading

client initiates server connection

A thread is a separate flow of execution. This means that the program will have 2 things happening at once. *But for most python 3 implementations the different threads do not actually execute at the same time but only appear to*.

The python Global Interpreter Lock (GIL), in simple words, is a mutex (or lock) that allows only one thread to hold the control of the python interpreter.

Threads may run on different processors but they only run one at a time

Getting multiple tasks to run at the same time requires a non standard implementation of python, writing some code in a different language or using multiprocessing.

```python
#a custom function that blocks for a moment
def task():
	sleep(1) #wait a second
	print("This is from another thread") #prints a message
```

```python
...
#create a thread
thread = Thread(target=task)

...
#start a thread
thread.start()

...
#wait for the thread to finish
print("Waiting for the thread...")
thread.join()
```

```python
#Server side
import socket
from threading import thread
from SocketServer import ThreadingMixIn
from datetime import datetime

#creating the class for multithreading
class Thread_of_Client(Thread):
	def __init__(self, ip, port):
		Thread.ip = ip
		self.port = port
		print("New Server socket thread " + ip + ": " + str(port))

	def run(self):
		while True:
			data = conn.recv(2048)
			print("TIME: ", datetime.now().time())
			print("Server received data ", data)
			MESSAGE = raw_input("Enter the response from the server and end with .: ")
			if MESSAGE == ".":
				break

			conn.send(MESSAGE)

SERVER_IP = '127.0.0.1'
SERVER_PORT = 2005
BUFFER_SIZE = 2000

Server_obj = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
Server_obj.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
Server_obj.bind((SERVER_IP, SERVER_PORT))
threads = []

while True:
	Server_obj.listen(4)
	print("Waiting for connection from clients")
	(conn, (ip, port)) = Server_obj.accept()
	newthread = Thread_Of_Client(ip, port)
	newthread.start()
	threads.append(newthread)

for t_obj in threads:
	t_obj.join()
```

```python
#client side
import socket
from datetime import datetime

SERVER_IP = '127.0.0.1'
SERVER_PORT = 2005
BUFFER_SIZE = 2000

Client_Obj = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
Client_Obj.connect((SERVER_IP, SERVER_PORT))

while True:
	MESSAGE = raw_input("Enter message and end with . : ")
	if MESSAGE == ".":
		break
	Client_Obj.send(MESSAGE)
	data = Client_Obj.recv(BUFFER_SIZE)
	print("MESSAGE RECEIVED TIME: ", datetime.now().time())
	print("DATA RECEIVED: ", data)

Client_Obj.close()
```

Threads are used from a server to connect to different clients