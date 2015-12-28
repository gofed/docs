## Request and response specification

### Request specification

Each client request consists of sequence of requests (controller requests).
Controller is responsible for forwarding requests to the correct worker only.
Thus, each client request has its context (which request in the sequence is currently active).

Thus, each request consists of:

* Client request header
* Request header
* Request body

Client request header holds information such as analysis to commit (on a high level),
extraction to commit (on a high level), golang project to process, etc.
In general, everything describing complete information about a client request
to break the request into individual controller requests and to be able to complete
each request in the sequence.
I.e. holding all data that can not be retrieved by controller requests.

Request header holds information such as which storage to use,
which concrete analysis to send data to, etc.
In general, all information describing particullar controller request to be
able to prepare worker.

Request body holds data send to a worker.
It can be storage query, it can be data sent to a storage,
data sent to a worker, etc.

### Response specification

Each response consists of:

* Client response header
* Response header
* Response body

Client response header corresponds to client request header (in many cases 1:1 copy).
Response header corresponds to request header and describe an origin of the response.
Response body holds data produced by a worker.

Client response header and response header creates a context
and are used by request handler to generate the next request (usually
to generate a request for storing data in a storage).

### Examples

#### Allocated API analysis
```yaml
client-request: "golang-allocated-api-analysis"
project: "github.com/coreos/etcd"
commit: "8d368c4dbad1277a5ad216af96ccadd39b04320c"
context: "retrieve-dependencies-from-storage"
query:
- "artefact = golang-project-symbol-table AND project = 'github.com/coreos/pkg/capnslog' AND commit = '2c77715c4df99b5420ffcae14ead08f52104065d'"
- "artefact = golang-project-symbol-table AND project = 'github.com/google/btree' AND commit = 'cc6329d4279e3f025a53a83c397d2339b5705c45'"
...
```

Here, *client-request, project, commit* are part of client request header, *context* part of request header and *query* part of request body.
