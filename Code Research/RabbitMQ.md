Created at 07-03-2024 @ 21:03 by Reno Muijsenberg

# Table of Contents
[Context](#Context)
[Setup](#Setup)
[Concepts](#Concepts)
[Sending](#Sending)
[Receiving](#Receiving)
[Work Queues](#Work%20Queues)
[Round-robin Dispatching](##Round-robin%20Dispatching)
[Message Acknowledgment](##Message%20Acknowledgment)
[Message Durability](##Message%20Durability)
[Fair Dispatch](##Fair%20Dispatch)
# Context
For this code research I want to try and setup a working instance of RabbitMQ using Python as a programming language. So, why do I want to do this? Well its quite simple as our sixth semester at Fontys our learning outcomes are all pointing toward enterprise applications. As I personally think that using a message queue is a part of these enterprise applications, I wanted to get some experience with it.

# Setup
Before starting there will be some setting up to do. I will assume that a couple of things are already present on our computers.

Prerequisites:
- A newly setup Python project (current version 3.12)
- Docker Desktop running (current version 4.26.1)

After having the prerequisites we will start the docker container with the following command.
```console
docker run -d --hostname my-rabbit --name some-rabbit -p 5672:5672 -p 15672:15672 rabbitmq:management
```

When the container is started and the status is set to running, we go to the following URL: `http://localhost:15672/` and login with the username and password both being `guest`.

That is it, when being able to login we know have a working RabbitMQ server setup and running in a Docker container!

# Concepts
- A program that sends messages is a _producer_.
- A _queue_ is the name for the post box in RabbitMQ. 
- A _consumer_ is a program that mostly waits to receive messages.

# Sending
First we will start by sending a single message to a queue. To do this we will use a Python package called pika. 

In the console type:
```console
pip install pika
```

After having pika installed we will need to establish a connection to the RabbitMQ Docker container.

```python
import pika  
  
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))  
channel = connection.channel()
```

If everything is correctly setup, when this is run it will not return an error in the console. To validate it even better we can create a queue called hello, like so:

```python
channel.queue_declare(queue='hello')
```

When this code gets fired we can validate it in the RabbitMQ management client at the `Queues and Streams section` where it should display a queue called `hello`.

When the hello queue is displayed correctly, everything is working fine. Now it is time to send a simple message to this queue. We do this by adding the following code, where we send a message in the `body` to the queue specified in the `routing_key` 

```python
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
```

After this we should close the connection of to the RabbitMQ server to flush out all the network buffers by adding the following line of code:

```python
connection.close()
```

When everything is run without any errors, we can see in the RabbitMQ client that there is one message 'ready'.
# Receiving
For the second program we will make a Python script that will receive the sent messages. First of all lets create a new Python script called `receive.py`.

In this script we first will add the connection code again, that we also used to connect to send messages, and declare a queue we want to use:

```python
import pika  
  
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))  
channel = connection.channel()

channel.queue_declare(queue='hello')
```

After having this code, we will create a callback function that handles receiving the message:

```python
def callback(ch, method, properties, body):  
    print(f" [x] Received {body}")
```

Now that we have this function, we need to define this callback to the RabbitMQ code, and after that start consuming the messages:

```python
channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

print(' [*] Waiting for messages...')  
channel.start_consuming()
```

To clean-up the code we will add some handling for exiting the program, the finished code will look something like this:

```python
import pika  
  
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))  
channel = connection.channel()  
  
channel.queue_declare(queue='hello')  
  
  
def callback(ch, method, properties, body):  
    print(f" [x] Received {body}")  
  
  
channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)  
  
print(' [*] Waiting for messages...')  
  
try:  
    channel.start_consuming()  
except KeyboardInterrupt:  
    print(' [*] Exiting consumer...')  
    connection.close()
```

When this code is running and we run the `send.py` code sometimes it will have a console output of:

```console
 [*] Waiting for messages...
 [x] Received b'karin!'
 [x] Received b'karin!'
 [x] Received b'karin!'
 [*] Exiting consumer...
```

# Work Queues
The next concept in RabbitMQ we are going to try to implement is a work queue, a work queue according to the documentation is:

>The main idea behind Work Queues (aka: _Task Queues_) is to avoid doing a resource-intensive task immediately and having to wait for it to complete. Instead we schedule the task to be done later. We encapsulate a _task_ as a message and send it to the queue. A worker process running in the background will pop the tasks and eventually execute the job. When you run many workers the tasks will be shared between them.

## Round-robin Dispatching
The scripts used in the will use round-robin dispatching as a standard method, this means that it will even the workload between the available workers.

For example:

I sent 100 messages to the queue, all with an increased value of i which gets increased every instance (e.g., Hello 1, Hello 2, Hello 3 etc..)

Then I have two workers running in the background, a so called `worker 1` & `worker 2`.

- `Worker 1`: will always gets the first message (odd) (e.g., Hello1, Hello3, Hello5 etc.)
- `Worker 2`: will always gets the second message (even) (e.g., Hello2, Hello4, Hello6 etc.)

If the number or workers increases the workload also gets given to `worker 3`, so that would be Hello3, Hello6 etc., in this example.

>By default, RabbitMQ will send each message to the next consumer, in sequence. On average every consumer will get the same number of messages. This way of distributing messages is called round-robin.

## Message Acknowledgment
In our previous receive script we automatically acknowledge the message once it is read. When tasks become bigger, this can be bad and result in a message getting lost.

In order to make sure that a message is never lost, RabbitMQ supports message acknowledgement. This acknowledgement is sent back to the consumer / worker to confirm that that a message actually has been received, and that it is free to be deleted from the queue.

If a consumers / worker dies without sending an acknowledgement RabbitMQ will understand that it was not fully processed and it will be re-queued, if an other worker is online it will then quickly deliver it to this worker. A timeout of 30 minutes is the default time for delivering an acknowledgement. 

## Message Durability
In the previous sections we have learned to make sure when a consumer dies, the message does not get lost. But if the RabbitMQ server stops the message will still get lost.

When RabbitMQ quits or crashes it will forget the queues unless it is specifically set to remember these queues. Two things are required to make sure that this happens.

First we will need to tell a queue that it needs to be `durable`, this will only work when a queue is not yet created.

```python
channel.queue_declare(queue='task_queue', durable=True)
```

This `queue_declare` change needs to be applied to both the producer and the consumer.

To ensure the `task_queue` survives a RabbitMQ restart, we've configured it to be durable. Now, let's mark our messages as persistent by setting the `delivery_mode` property to `pika.DeliveryMode.Persistent`. This guarantees that messages are written to disk, preventing data loss in case of a server crash.

```python
channel.basic_publish(exchange='',  
	routing_key="task_queue",  
	body="Hello",  
	properties=pika.BasicProperties(  
	delivery_mode = pika.DeliveryMode.Persistent  
))
```

## Fair dispatch
When having everything setup till this point, it is noticeable that the queue's do not yet care about the workload of a task. As the example at [Round-robin Dispatching](##Round-robin%20Dispatching) states, the workload gets evenly distributed. This is because RabbitMQ does net yet know something about the workload of a consumer.

>This happens because RabbitMQ just dispatches a message when the message enters the queue. It doesn't look at the number of unacknowledged messages for a consumer. It just blindly dispatches every n-th message to the n-th consumer.

In order to beat this, we can use the `prefetch_count=1` settings. This uses the `basic.qos` protocol method to tell RabbitMQ not to give more than one message to a worker at a time. Instead, it will dispatch it to the next worker that is not still busy.

```python
channel.basic_qos(prefetch_count=1)
```

You can try out the `fair dispatching` by running multiple workers with a different sleep value to see the difference.

We taking all these concepts into consideration the completed code will look something like:

Send.py
```python
import pika  
  
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))  
channel = connection.channel()  
  
channel.queue_declare(queue='task_queue', durable=True)  
  
for i in range(100):  
    channel.basic_publish(  
        exchange='',  
        routing_key='task_queue',  
        body=f"Hello World! {i}",  
        properties=pika.BasicProperties(  
            delivery_mode=pika.DeliveryMode.Persistent  
        ))  
  
connection.close()
```

Worker.py
```python
import pika  
import time  
  
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))  
channel = connection.channel()  
  
channel.queue_declare(queue='task_queue', durable=True)  
print(' [*] Waiting for messages. To exit press CTRL+C')  
  
  
def callback(ch, method, properties, body):  
    print(f" [x] Received {body.decode()}")  
    time.sleep(1)  
    print(" [x] Done")  
    ch.basic_ack(delivery_tag=method.delivery_tag)  
  
  
channel.basic_qos(prefetch_count=1)  
channel.basic_consume(queue='task_queue', on_message_callback=callback)  
  
channel.start_consuming()
```