---
pagename: Context Session Store
redirect_from:
Keywords:
sitesection: Documents
categoryname: "Conversational AI"
documentname: Maven
subfoldername: Context Warehouse
permalink: maven-context-warehouse-context-session-store.html
indicator: both
---

### Overview

The Context Services API is a REST interface to a cloud based repository for storing and retrieving session state attributes that can be carried over and used throughout the conversational journey. This allows continuity in conversations as context can be transferred between agents and bots enabling a true warm handoff. The context service can be used for several purposes. For example:

1. Save the conversation session state data in LiveEngage (e.g. agent notes), and then retrieve them later in a different conversation session with a different agent.

2. Save contextual attributes in a concierge bot (e.g. intents or customer information) and carry this context over to another bot or human skill. 

The Context APIs are part of Maven, LivePerson’s AI engine, that allows brands to store, retrieve, and manage custom attributes programmatically. The context store provides a system of hierarchically organizing your data.

<img class="fancyimage" width="600" src="img/maven/image_37.png">

Each brand can have multiple **namespaces** for different business use cases. Typically a namespace may group together related attributes, for example customer information such as name, email, phone number etc. which are stored as **Key-Value Pairs.** Brands can define as many attributes they need per namespace. To group together the attributes in a namespace for example a conversation session brands can use the **Session ID**. Each object in the hierarchical structure (Namespace, Session ID, KVPs) comes with CRUD (Create, Read, Update, Delete) operations using the REST APIs. 

### Example Use Cases

* Passing context (intent, customer info) and customer routing / escalation path between bots.

<img class="fancyimage" width="600" src="img/maven/image_38.png">

* Use shared context across different agents in a single conversation

<img class="fancyimage" width="600" src="img/maven/image_39.png">

* Carry over attributes and information collected in a multi-step conversational journey for continuity and warm handoffs between bots and human agents. 

<img class="fancyimage" width="600" src="img/maven/image_40.png">

### Developer Key

To use Context Session store APIs you will need to create and use an API key. To get your unique key:

1. Login to Maven with your LiveEngage credentials and then navigate to **Developer Key**.

2. Copy and paste the key you see in the experience and use it in your API headers. 

3. To generate a new key, click on the **Regenerate Key **button. Please note that this will invalidate the previous key. The key is shared for all Maven APIs and therefore you will have to use the new key wherever the APIs are being called.  

<img class="fancyimage" width="600" src="img/maven/image_41.png">

3. [Create your namespace](#create-a-custom-namespace)

### Methods

Every API call to the Maven Context service requires the following Auth Headers to be accepted

`Content-Type : application/json`

`maven-api-key : <INSERT YOUR API KEY HERE>`

#### Create a custom namespace

To use the Maven Context API, you will first need to create a Namespace.

You will use the namespace value that is returned in the response in all other Context API methods.

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>POST</td>
            <td>/v1/account/{accountId}</td>
            <td>Create a custom namespace</td>
            <td>{
              "name": "someCustomNamespace"
            }
            </td>
            <td>Empty body; status code = 204
            </td>
        </tr>
    </tbody>
</table>

Example: 

```bash
curl --request POST \

  --url https://z2.context.liveperson.net/v1/account/le57708964 \

  --header 'content-type: application/json' \

  --header 'maven-api-key: CEl7KSCf59IQEAFTQ1H2uCGv0yr4HUtH' \

  --data '{

    "name": "myCoolNamespace"

}'
```

#### Set custom namespace properties

This will override the value if an existing key is used. If a new key is used then it will be added.

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>PATCH</td>
            <td>/v1/account/{accountId}/{customNamespace}/properties</td>
            <td>Set custom namespace properties [this will override the value if an existing key is used; if a new key is used then it will be added)]</td>
            <td>JSON object of properties and values:

{"a":1,"b":"hello","c":true}</td>
            <td>{"cas":"1560823737328336896"}
            </td>
        </tr>
    </tbody>
</table>

Example:

```bash
curl --request PATCH \

  --url http:///lp-mavencontext-app-qa.dev.lprnd.net/v1/account/le57708964/myCoolNamespace/properties \

  --header 'content-type: application/json' \

  --header 'maven-api-key: CEl7KSCf59IQEAFTQ1H2uCGv0yr4HUtH' \

  --data '{

    "minutesSinceLastConversation": 720,

    "salesforceId": "jeff@virtuoz.com",

    "isSomething": true

}'
```

#### List of namespaces created

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET</td>
            <td>/v1/account/:accountId</td>
            <td>List of namespaces created</td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Example: 

```bash

curl --request GET \
  --url https://z2.context.liveperson.net/v1/account/36209512 \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf'

```

#### Get all namespace variables

This will get all Key/value pairs for all requested properties that are available, prefixed with the namespace.

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET</td>
            <td>/v1/account/{accountId}/{customNamespace}/properties</td>
            <td>Get all Key/value pairs for all requested properties that are available, prefixed with the namespace</td>
            <td></td>
            <td>{"accountID":"le57708964","a":1,"b":"hello","c":true}
            </td>
        </tr>
    </tbody>
</table>

Example:

```bash
curl --request GET \

  --url http://lp-mavencontext-app-qa.dev.lprnd.net/v1/account/le57708964/myCoolNamespace/properties \

  --header 'maven-api-key: CEl7KSCf59IQEAFTQ1H2uCGv0yr4HUtH'
```

#### Get all namespace variables

This will get all Key/value pairs for all requested properties that are available, prefixed with the namespace.

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET</td>
            <td>/v1/account/{accountId}/{customNamespace}/properties</td>
            <td>Get all Key/value pairs for all requested properties that are available, prefixed with the namespace</td>
            <td></td>
            <td>{"accountID":"le57708964","a":1,"b":"hello","c":true}
            </td>
        </tr>
    </tbody>
</table>

Example:

```bash
curl --request GET \

  --url http://lp-mavencontext-app-qa.dev.lprnd.net/v1/account/le57708964/myCoolNamespace/properties \

  --header 'maven-api-key: CEl7KSCf59IQEAFTQ1H2uCGv0yr4HUtH'
```


#### Get all properties

We recommend to use Namespace to group related attributes (KVPs), and use the groupID (aka entityId) to as a session state variable. You can put anything you want in the groupId. If you want to put consumer and conversation data in the same namespace, you can as long as the groupIDs are unique across the two. And it's optional – if omitted, the system will use a default entityId of `__default__`

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET</td>
            <td>/v1/account/{accountId}/{customNamespace}/{groupId}/properties/</td>
            <td>Get all properties
            </td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Example: 

```bash

curl --request GET \
  --url https://z2.context.liveperson.net/v1/account/36209512/namespace1/100/properties \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf'

```

#### Get one property

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET</td>
            <td> /v1/account/{accountId}/{customNamespace}/{groupId}/properties/{propertyName}</td>
            <td>Get one property</td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Example: 

```bash



```

#### Update multiple properties within an entity (group)

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>PATCH</td>
            <td>/v1/account/{accountId}/{customNamespace}/{groupId}/properties/
            </td>
            <td>Update multiple properties
            </td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Example: 

```bash

curl --request PATCH \
  --url https://z2.context.liveperson.net/v1/account/36209512/namespace2/200/properties \
  --header 'content-type: application/json' \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf' \
  --data '{
	"a": 1,
	"b": 2,
	"c": 3
}'


```

#### pass multiple propertyName to the GET properties	

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>GET </td>
            <td>/v1/brand/{accountID}/{customNamespace}/{groupId}/properties?include=prop1,prop2,prop3</td>
            <td>pass multiple propertyName to the GET properties</td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

Example: 

```bash

curl --request GET \
  --url https://z2.context.liveperson.net/v1/account/36209512/namespace1/properties?include=minutesSinceLastConversation,salesforceId \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf'

```


#### Delete a Group of properties

Delete groupID would be used if brands wants to delete a set of session attributes within a namespace. For instance after the attributes have been used in a conversational journey they may decide to delete the values at the logical end of the conversation. 


<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>DELETE</td>
            <td>/v1/account/{accountId}/{customNamespace}/{groupId}/properties</td>
            <td>Delete a group of properties </td>
            <td>
            </td>
            <td>
            </td>
        </tr>
    </tbody>
</table>

Example: 

```bash

curl --request DELETE \
 --url https://z2.context.liveperson.net/v1/account/36209512/namespace2/200/properties \
 --header 'maven-api-key: 04UQXk21ZDmKdN9jTwP8ty1sSoTLFAFQ'

```
#### Delete a property


<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>DELETE</td>
            <td>/v1/account/{accountId}/{customNamespace}/properties/{propertyName}</td>
            <td>Delete a property</td>
            <td>
            </td>
            <td>
            </td>
        </tr>
    </tbody>
</table>

Example: 

```bash
curl --request DELETE \
  --url https://z2.context.liveperson.net/v1/account/36209512/namespace1/properties/isSomething \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf'

```
#### Delete a custom namespace

Delete namespace may be used if brand wants to retire a namespace. This would really be quite rare, and may happen if entire set of attributes are not used any more, such as an entire use case is being retired.

<table>
    <thead>
        <tr>
            <th>Method</th>
            <th>Path</th>
            <th>Description</th>
            <th>Request Payload Example</th>
            <th>Response Payload Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>DELETE</td>
            <td>/v1/account/{accountId}/{customNamespace}</td>
            <td>Delete a custom namespace</td>
            <td>
            </td>
            <td>
            </td>
        </tr>
    </tbody>
</table>

Example: 

```bash
curl --request DELETE \
  --url https://z2.context.liveperson.net/v1/account/36209512/namespace1 \
  --header 'maven-api-key: DigxAZB4lO9M0XCaW1DphiwW4Tz9U2xf'

```