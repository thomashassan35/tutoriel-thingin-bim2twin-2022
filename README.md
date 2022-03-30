# tutoriel-thingin-bim2twin-2022

## Introduction 

This tutorial / hands-on session covers a few basics required to use Thing'In the future APIs, as well as some more advanced examples.
For all elements of this tutorial, and for your future experience with Thing'In the future, you can refer to the documentation/Wiki : https://wiki.thinginthefuture.com/. 

## Agenda
1. Presentation of the Thing in platform (~1h)
2. Creation of a dataset using Building Topology Ontology (BOT) (~1h)
    * Presentation of building injector (IFC + topology)
    * Creation of the dataset
    * Injection of the dataset on the Thing in platform
3. Query on the dataset (~1h)
    * Presentation of the wizard 
    * Query the dataset, with wizard and with swagger or postman
    * Select queries
    * Update queries
    * Aggregation queries
    * Geographic queries
    * Use Blobs (binary large objects)
    
    
## Postman

To go through this hands-on session you can either use a preconfigured set of requests given in a Postman collection, or the swagger interface / thingin portal. 

### Importing the postman collection 

If you choose to use the Postman collection, the import procedure is simple:
After cloning / downloading this repository, locate the postman collection file (.json) on your system (The collection is located on the github repository under /postman). 

Then, in postman, go to 
> File -> import

And choose the preconfigured collection.

### Settings up requests in the following sections of the tutorial

In the examples below, when using the swagger/portal you have to replace the variable <yourname> with a string with no spaces or special characters, e.g. "thassan". You have to do this wherever "<yourname>" appears (in the query payloads). 
	
When using Postman, you only have to set an environment variable in your Postman Environment **once**:
Go to
> Environments
	
and add a row : under **VARIABLE**, write **yourname**, and under **CURRENT VALUE**, set it with a string of your choice, the string must have no spaces or special characters, e.g. "thassan".


### Setting up connection to ThingIn  
    
To setup connection with BIM2TWIN's thingin tipod, first use the credentials provided at the beginning of the session (Teams). 

For Swagger/portal :
After logging in through the portal, you have to use a TOKEN to use the APIs. This is a simple two-step process
	- click on Develop (on top) -> Get My Thingin Token -> Copy to clipboard for swagger Usage 
	- In the swagger interface,  before sending queries, do this step once : click on authorize ->  paster your token under " bearerAuth  (http, Bearer) Thing'in token (without Bearer keyword)"
	
	
In Postman, go to: 
> get developpper TOKEN. 

You have to fill the Authorization with basic authentication using the credentials, then send the request
    
To ensure that the following Postman requests are well configured, go to:
>batchPostavatars TEST TOKEN

You have to fill in the payload (Body), then send the request. The response code should be a 201. If you have a bad character error, you might not have configured the <yourname> variable. 
    
**You don't have todo any additional steps for the other queries to use your credentials, it is filled automatically**
    


## Creation of the dataset

### Presentation of building injectors

There exist two types of injector to upload ifc files:
* Ifc file to Dogont ontology:
    * This injector will only inject the topology of the building using both DogOnt and Bot ontologies
* Ifc file to Ifc ontology:
    * This injector will inject the ifc according to the ifc ontology

Both injectors can be found under "Provide" > "specific injector".

To inject an ifc file, first click on "Create a new injection".
Then you can choose Injector type as detailed aboce.
The "domain" will be the bucket were your avatars will be injected, and is **must starts with**: "http://bim2twin.eu/"
> Ex: http://bim2twin.eu/training/ac20/

An example of an injected file can be found under "Explore" > "Explore Thing'in database".
Select the request "AC20 - Topological injection".
The original ifc file can be find in:
> dataset/AC20-FZK-Haus.ifc

### Create dataset

#### Bot Ontology

https://w3c-lbd-cg.github.io/bot/

![Schema of BOT ontology](https://w3c-lbd-cg.github.io/bot/assets/zones.png)


#### Mandatory properties

Each avatar have two mandatory properties:
* **__iri_**: the identifier ot the avatar (Inernational Ressource Identifier). IRIs **must start** with the following prefix: "http://bim2twin.eu/"
> ex: "__iri_": "http://bim2twin.eu/training/simpleBuilding/Room_A_1"

* **__classes_**: list of ontology classes. The avatar is an instance of each of these classes. An avatar have at least one class
> ex: "__classes": ["https://w3id.org/bot#Space"]

When avatars are created the are stored in a domain. Domains can be seen as bucket.
They are created automatically based on the **__iri_** of an avatar.
For the avatar above the following domain will be created:
> ex: "__domain":"http://bim2twin.eu/training/simpleBuilding/"

If an new avatar's iri follow the same prefix ("http://bim2twin.eu/training/simpleBuilding/"), then the avatar is store in the same domain.
> ex: "__iri_": "http://bim2twin.eu/training/simpleBuilding/Room_A_2", is in the same domain than: "__iri_": "http://bim2twin.eu/training/simpleBuilding/Room_A_1"

Using Bot ontology create the following:
- 1 bot:Building
- 2 bot:Storey
- 4 bot:Space (2 space per Storey)
- Link bot:Storey - hasSpace -> bot:Space
- Linkt bot:Building - hasStorey -> bot:Building

There exist two possibles format to build your dataset:
* RDF (Ressource Description Framework): is a standard from W3C coming from the Semantic Web community to describe a data model.
* JSON (JavaScript Object Notation): is an open standard file format

See examples bellow of data model both in RDF and JSON to create one building with one storey.

#### RDF
Input: 

Complete the query body by replacing (not necessary in Postman if done previously) :
```
<yourname>
``` 

In the example bellow the line "@prefix ex: <http://bim2twin.eu/training/simpleBuilding/<yourname>/>", refers to the domain in which your avatars will be stored.

```rdf
@prefix ex: <http://bim2twin.eu/training/simpleBuilding/<yourname>/> .
@prefix bot: <https://w3id.org/bot#> .
@prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

ex:Storey_1 rdf:type bot:Storey .

ex:Small_Building rdf:type bot:Building ;
	bot:hasStorey ex:Storey_1 .

```

<details>
  <summary>Answer:</summary>
  
  ```rdf
    @prefix ex: <http://bim2twin.eu/training/simpleBuilding/<yourname>/> .
    @prefix bot: <https://w3id.org/bot#> .
    @prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

    ex:Room_A_1 rdf:type bot:Space .

    ex:Room_A_2 rdf:type bot:Space .

    ex:Room_B_1 rdf:type bot:Space .

    ex:Room_B_2 rdf:type bot:Space .

    ex:Storey_1 rdf:type bot:Storey ;
	    bot:hasSpace ex:Room_A_1 ;
	    bot:hasSpace ex:Room_B_1 .

    ex:Storey_2 rdf:type bot:Storey ;
	    bot:hasSpace ex:Room_A_2 ;
	    bot:hasSpace ex:Room_B_2 .
	
    ex:Small_Building rdf:type bot:Building ;
	    bot:hasStorey ex:Storey_1 ;
	    bot:hasStorey ex:Storey_2 .
```
</details>




#### JSON

Input:

Complete the query body by replacing (not necessary in Postman if done previously) :
```
<yourname>
``` 

In JSON you do not have to set the name of the domain, it will be directly extracted form the avatars' Iri.

```json
[
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Small_Building",
	"_classes": ["https://w3id.org/bot#Building"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_1"
	  }
	]
  }
]
```


<details>
  <summary>Answer:</summary>
  
```json
[
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_A_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_A_2",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_B_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_B_2",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_1",
	"_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_A_1"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_B_1"
	  }
	]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_2",
	"_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_A_2"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Room_B_2"
	  },
	  {
		"_label": "http://orange-labs.fr/fog/ont/building.owl#isAbove",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_1",
		"bim2twin:dismantlingTime": 90
	  }
	]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_3",
    "_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
		"_label": "http://orange-labs.fr/fog/ont/building.owl#isAbove",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_2",
		"bim2twin:dismantlingTime": 102
	  }
	]
  },
  {
    "_iri": "http://bim2twin.eu/training/simpleBuilding/Small_Building",
	"_classes": ["https://w3id.org/bot#Building"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_1"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_2"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/<yourname>/Storey_3"
	  }
	]
  }
]
```
</details>

### Inject your data

#### Swagger
    
The swagger can be find in "Develop" > "Thing in API reference"

Avatar can be injected one by one:
> /avatars

Inject several avatars at once:
> /batch/avatars/

#### Postman

Under the folder:
> /Avatars injection

To inject avatars one by one in JSON:
> singlePostAvatar JSON

To inject several avatars at once:
> batchPostAvatars JSON
	
> batchPostAvatars TURTLE
	
## Queries

### Use the wizard

Under "Explore" > "Explore Thing in database" > "Use wizard"

Queries can be build according to three possibles options:
* Domain: in which domain are the avatars
* Classes: avatars of the given class(es) to look for
* Geolocation: define a polygon or a radius given a point to look for avatars (works only for avatars with geo coordinates)

### Use the swagger or postman collection

#### Swagger
    
The swagger can be find in "Develop" > "Thing in API reference"

To find avatars:
> /avatars/find/

#### Postman collection

In the postman collection under:
> /Avatar requests

To retrieve avatars in JSON format:
> find as JSON

To retrieve avatars in TURTLE format:
> find as TURTLE

### Query your own dataset

Using the swagger or postman collection.

```json
{
  "query": {
    "$domain": "http://bim2twin.eu/training/ac20/"
  },
  "view": {}
}
```

### Query - Find the rooms of a given store:

Example to find storeys of a given building, where "http://www.bim2twin.com/training/ac20/ifc-2hQBAVPOr5VxhS3Jl0O47h" is the identifier of the building:

```json
{
  "query": [
    {
      "$domain": "http://bim2twin.eu/training/ac20/",
      "$iri": "http://bim2twin.eu/training/ac20/ifc-2hQBAVPOr5VxhS3Jl0O47h",
      "->http://elite.polito.it/ontologies/dogont.owl#contains": "storey"
    },
    {
      "$alias": "storey",
      "$classes": "https://w3id.org/bot#Storey"
    }
  ],
  "view": {}
}
```
	
<details>
  <summary>Answer:</summary>
  
  ```json
{
  "query": [
    {
      "$domain": "http://bim2twin.eu/training/ac20/",
      "$iri": "http://bim2twin.eu/training/ac20/ifc-2eyxpyOx95m90jmsXLOuR0",
      "->http://elite.polito.it/ontologies/dogont.owl#contains": "room"
    },
    {
      "$alias": "room",
      "$classes": "https://w3id.org/bot#Space"
    }
  ],
  "view": {}
}
```
Bellow an example to find rooms contained in storeys, without specifying the storey
```json
	{
  "query": [
    {
      "$domain": "http://bim2twin.eu/training/ac20/",
      "$classes": "https://w3id.org/bot#Storey",
      "->http://elite.polito.it/ontologies/dogont.owl#contains": "room"
    },
    {
      "$alias": "room",
      "$classes": "https://w3id.org/bot#Space"
    }
  ],
  "view": {}
}
```
</details>



### Add a storey to the building

First create the storey:

```json
{
    "_iri": "http://bim2twin.eu/training/simpleBuilding/Storey_4",
    "_classes": ["https://w3id.org/bot#Storey"]
}
 ```

Swagger:
> /avatars/

In Postman :
> /Avatars injection/singlePostAvatar JSON
    
Add the storey to the building :

First find the "__uuid_" of the building. It's a property of the building's avatar.
> ex: 0aba42b6-0f4e-55e3-908b-5b1eec1d3f4a

To update the data use, the swagger:
> /avatars/update/set/{uuid}

or in Postman:
> update SET


```json
{
	"_iri": "http://bim2twin.eu/training/simpleBuilding/Small_Building",
	"_outE": [{
		"_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://bim2twin.eu/training/simpleBuilding/Storey_4"
	}
    ]
}
```
    

### Aggregation query

Example of a query to dismantle a building, where relation of type "http://orange-labs.fr/fog/ont/building.owl#isAbove", have a property of time expressed in minutes. The query makes the sum of the value of the property of type "bim2twin:dismantlingTime".
    
```json
{
  "query": [
  {
    "$alias": "a",
    "$domain": "http://bim2twin.eu/training/simpleBuilding/",
    "->http://orange-labs.fr/fog/ont/building.owl#isAbove": "b"
  },
  {
    "$alias": "b_e",
    "$sum": {
      "$property": "bim2twin:dismantlingTime",
      "$alias": "totalTime"
    }
  }
  ],
  "view": {}
}
```
With swagger use:
> /avatars/findAggregate/

With postman use:
>  /Avatars requests/find AGGREGATE

For more information on aggregation queries see the [wiki](https://wiki.thinginthefuture.com/public/aggregation).

  
### Geographic queries

The following queries require avatars to have geographic coordinates.
    
Given a polygon find the avatars inside:

```json
{
  "query": [
    {
      "$alias":"Arbre",
      "$iri":"http://bim2twin.eu/training/rennes/polygon/tree_17683"
    },
    {
      "$domain": "http://bim2twin.eu/training/rennes/polygon/",
      "http://www.opengis.net/gml/pos/polygon": {
        "$geoContains": {
          "$alias":"Arbre"
        }
      }
    }

  ],
  "view": {}
}
```
    
Given the geographic position of an avatar, find avatars nearby a radius of 100 meters:
```json
{
  "query": [
    {
      "$alias":"Arbre",
      "$iri":"http://bim2twin.eu/training/rennes/polygon/tree_17683"
    },
    {
      "$domain": "http://bim2twin.eu/training/rennes/polygon/",
      "http://www.opengis.net/gml/pos": {
        "$distance": {
          "$alias":"Arbre",
          "$lte":100
        }
      }
    }

  ],
  "view": {}
}
```

For more info one query see the wiki for [RDF](https://wiki.thinginthefuture.com/public/Avatar_tutorial) and for [JSON](https://wiki.thinginthefuture.com/public/Manipulate_Avatars).

### Blob
    
Thing'In the future offers an array of more advanced features for avatars such as ACLs and Security Groups, graph traversal and paths, geometry or unit conversion, avatar clusters, etc. We cannot view every functionnality in one session, but you can have a look at the Wiki, as well as the complete swagger. 
    
One of the advanced features that may be of particular use for BIM data is Blobs https://wiki.thinginthefuture.com/en/public/Manipulate_blobs. Blobs can be created along avatars and attached to avatars. 

We show below a basic example of how to create a blob and attach it to an avatar, then retrieve or delete it.
    
**Create a blob.**
With swagger, use 
>/blobs/

With postman, use 
> post BLOB

When using the swagger, you can simply use the UI to select a file.
For the other parameters : 
- description is indicative, optional
- tags are indicative, optional
- visibility can be set to 0 (public) or 255 (private, default)
    
As for avatars, the response includes the '$uuid' which we can use to retrieve it, delete it, or link the blob to an avatar.
    

**Link to an avatar**
We will not cover how to create an avatar again, refer to the previous section of the tutorial or use an existing avatar you have created, you will need either its IRI or its UUID.

With swagger, use 
>/blobs/link/

With postman, use 
> link BLOB to avatar
    
In Postman, you don't need to copy and paste the $uuid of the blob from the blob creation response, a post-request script handles that for you and fills the following Delete/Get requests. 
    
**Retrive a blob**
With swagger, use 
>GET /blobs/{uuid}

With postman, use 
> get BLOB
    
**Delete a blob**
    
With swagger, use
> DEL /blobs/{uuid}
    

With postman, use 
> delete BLOB
    
    
## End of the session
That's it for this hands-on session, feel free to ask any question :)
	
Find complementary information on the [wiki](https://wiki.thinginthefuture.com/).

