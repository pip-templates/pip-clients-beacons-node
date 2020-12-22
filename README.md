# <img src="https://github.com/pip-services/pip-services/raw/master/design/Logo.png" alt="Pip.Services Logo" style="max-width:30%"> <br/> Client library to Beacons microservice in Node.js

This client library allows to use the [pip-services-beacons-node](https://github.com/pip-templates/pip-services-beacons-node) microservice to store a list of BLE beacons (a.k.a. iBeacons).

Supported functionality:
* HTTP client (REST and Commandable)
* GRPC client (Plain and Commandable)
* Direct client to connect to the microservice in-process
* Memory client to be used as a mock in automated tests
* Lambda client for supporting work with the AWS cloud platform

<a name="links"></a> Quick links:

* Communication Protocols:
  - [gRPC Version 1](src/protos/beacons_v1.proto)
  - [HTTP Version 1](src/swagger/beacons_v1.yaml)
* Service SDKs:
  - [Node.js SDK](https://github.com/pip-templates/pip-services-beacons-node)
* [API Reference](https://pip-templates.github.io/pip-clients-beacons-node/globals.html)
* [Change Log](CHANGELOG.md)

##  Contract

The contract of the client is presented below. 

```typescript

export interface IBeaconsClientV1 {
    getBeacons(correlationId: string, filter: FilterParams, paging: PagingParams,
        callback: (err: any, page: DataPage<BeaconV1>) => void): void;

    getBeaconById(correlationId: string, beaconId: string,
        callback: (err: any, beacon: BeaconV1) => void): void;

    getBeaconByUdi(correlationId: string, udi: string,
        callback: (err: any, beacon: BeaconV1) => void): void;

    calculatePosition(correlationId: string, siteId: string, udis: string[], 
        callback: (err: any, position: any) => void): void;

    createBeacon(correlationId: string, beacon: BeaconV1,
        callback: (err: any, beacon: BeaconV1) => void): void;

    updateBeacon(correlationId: string, beacon: BeaconV1,
        callback: (err: any, beacon: BeaconV1) => void): void;

    deleteBeaconById(correlationId: string, beaconId: string,
        callback: (err: any, beacon: BeaconV1) => void): void;            
}

```

## Get

Get the client library source from GitHub:
```bash
git clone git@github.com:pip-templates/pip-clients-beacons-node.git
```

Install the client library as a binary dependency:
```bash
npm install pip-clients-beacons-node
```

## Use

Install the client NPM package as
```bash
npm install pip-clients-beacons-node
```

Inside your code get the reference to the client SDK
```typescript
 import { BeaconsCommandableHttpClientV1 } from 'pip-clients-beacons-node';
```

Instantiate the client
```typescript
// Create the client instance
let client = new BeaconsCommandableHttpClientV1();
```

Define client configuration parameters.
```typescript
// Client configuration
let httpConfig = ConfigParams.fromTuples(
    "connection.protocol", "http",
    "connection.host", "localhost",
    "connection.port", 3000
);
client.configure(httpConfig);
```

Connect to the microservice
```typescript
// Connect to the microservice
client.open(null, function(err) {
    if (err) {
        console.error('Connection to the microservice failed');
        console.error(err);
        return;
    }
    
    // Work with the microservice
    ...
});
```

The client is now ready to perform operations
```javascript
// Define a beacon
let beacon: BeaconV1 = {
    id: '1',
    udi: '00001',
    type: BeaconTypeV1.AltBeacon,
    site_id: '1',
    label: 'TestBeacon',
    center: { type: 'Point', coordinates: [ 0, 0 ] },
    radius: 50
};

async.series([
    // Create the beacon
    (callback) => {
        this.client.createBeacon(
            null,
            BEACON1,
            (err, beacon) => {
                if(err){
                  // Error handling...
                }
                
                // Do something with the returned beacon...

                callback();
            }
        );
    },
    // Get a list of beacons
    (callback) => {
        this.client.getBeacons(
            null,
            FilterParams.fromTuples(
              "label", "TestBeacon",
            ),
            new PagingParams(0, 10),
            (err, page) => {
                if(err){
                  // Error handling...
                }

                // Do something with the returned page...
                // E.g. beacon = page.data[0];

                callback();
            }
        )
    }
], (err, results) => {
    if (err) {
        // Error handling...
    }
});
```

## Develop

For development you shall install the following prerequisites:
* Node.js 8+
* Visual Studio Code or another IDE of your choice
* Docker
* Typescript

Install dependencies:
```bash
npm install
```

Compile the code:
```bash
tsc
```

Before running tests launch infrastructure services and required microservices:
```bash
docker-compose -f ./docker-compose.dev.yml up
```

Run automated tests:
```bash
npm test
```

Generate GRPC protobuf stubs:
```bash
./protogen.ps1
```

Generate API documentation:
```bash
./docgen.ps1
```

Before committing changes run dockerized build and test as:
```bash
./build.ps1
./test.ps1
./clear.ps1
```

## Contacts

This client SDK was created and currently maintained by *Sergey Seroukhov*.
