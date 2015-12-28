## Communication model

Communication is implemented through message passing.
Model consists of controllers, workers and clients.
Client is a source of requests (client requests from here on).
Each client request can be implemented as a sequence of controller requests (requests from here on).
Controller takes care of sending requests to workers and recieving responses from them.
Each response can be transformed into a new request. Controller sends
header of a response to request handler (RH) which decides what to do next.
If a new request is returned, it is put into a request queue.
If the response is final it is sent to response queue of a client.

![Communication model](/fig/communication_model.png)

### Components

#### Worker

Worker (W) is a system unit that is responsible for receiving a request and answering by response.
Worker can by concrete analysis, extractor, transformator or storage.
Workers of the same kind are grouped into a worker kind (e.g. dependency analysis, API storage, etc.).
In general, each kind can be located on different node.

#### Gate

Gates is responsible for receiving and enqueuing requests for computation.
And for sending results of computation back to a controller.
This way worker is unaware of source of data and communication complexity.

Each gate works with two queues:

* request queue (RQQ) and
* response queue (RSQ).

Request queue is used for storing incomming requests when worker is busy.
Response queue is used for storing results of computation.
Each result is wrapped into appropriate response and sent to a controller.
If the response is recieved by the controller, result is removed from the queue.

#### Proxy

To provide more computational power for one analysis more instances of the same kind can be created.
Proxy is reponsible for spreading computation among all instances equally.
In other words, it is responsible for receiving requests from controller
and forwarding them to the correct worker and back.

One proxy corresponds to one kind. Each proxy is located on a node.
Each node can host more proxies.

Proxy is also responsible for registering, resp. unregistering a kind to, resp. from a controller.

#### PoC

In some use cases, all workers, controllers and clients can be located on the same node.
In others, each proxy, client and controller can have its own node.
To abstract all components from their location, Point of Contact (Poc) is introduced.
It forwards all requests/responses to other PoCs which can be on the same or remote node.

#### Request handler

Each client request correspond to one semantic operation.
E.g. extract data from a tarball, analyse dependencies, etc.
Each such operation is broken down into simple operations
corresponding to one request (controller request).
E.g. "extract data from a tarball" operation is broken down to the following requests in given order:

* sending the tarball to extractor
* receiving extracted data and sending them to transformator
* receiving transformed data and sending them into a storage
* receiving confirmation from storage and reponsing back to client 

Thus, each client request can be seen as a sequence of requests
each in a given order representing a state of processing of the client request.

Request handler (RH) is responsible for processing each response and generating
the correct request based on the previous request the response belonged to.
Thus, each request and reponse carries (among other data) its client request state.

#### Register

To store location (local or remote) of each worker kind,
register component is introduced.
List of kinds can be read from a configuration file or picked up from a network.
It is used by controller to send requests to the correct consumer.

#### Controller

Controller is hearth of the system.
It is responsible for sending requests and receiving responses.
With help of request handler, new requests are generated based on incomming responses.
When client request is served (last state reached) or there is an error (e.g. missing analyzer, failed extraction, etc.),
controller properly responds back to a client.

Each request, resp. response is stored in request queue (RQQ), resp. response queue (RSQ).
Controller checks each request and based on its kind (which corresponds to its state)
it sends the request to its corresponding proxy.
Once a response from a worker kind is received, controller sends back confirmation message.
The request from which the response originates is removed from RQQ.

### Messages

Messages are transportation mechanism for exchaging [requests and responses](request_response_specification.md). Each request specifies what needs to be done and can carry [data](data_specification.md) for processing.


### Examples

#### Simple Synchronious Controller

The simplest implementation is a controller (Ctrl) that can process one request at time.
All components are on the same host, no networking supported.
All gates just invoke methods of the controller and wait for return.
All operations are synchronious. There is no paralelism, no queue.
All gates implement the same interface (IF).
Request handler (HR) is common for all various controller implementations.
Register provides a list of object instances, one for each worker kind.

![Simple Synchronious Controller](/fig/simple_sync_controller.png)

Gate implements:
```vim
send()
recieve()
```

Register implements:
```vim
register()
unregister()
lookup()
```

### TODO

* specify messages
* specify mechanism of pairing requests of the same kind with responses of the same kind (assign unique ID to each request? how to handle more controllers each on its own node, etc.? => different strategies and implementations of a controller)
* this is only message related = specification of data representation for API, dependencies, project info, etc. are not subject of this document

