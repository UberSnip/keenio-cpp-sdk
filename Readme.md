========================================================================
    UberSnip||Carrots_KeenIO C++ SDK
========================================================================

KeenIO C++ SDK and Query language.

This project covers KeenIO's APIs, and also includes a query language similar to SQL which allows for quick, easy data extraction.

This API connects to KeenIO over Winsock.

##### Documentation

[KEENIO_CLIENT](https://github.com/UberSnip/keenio-cpp-sdk/wiki/class::KEENIO_CLIENT)

[HEENIO_HTTP](https://github.com/UberSnip/keenio-cpp-sdk/wiki/class::KEENIO_HTTP)

## WIKI
 Checkout the [wiki](https://github.com/UberSnip/keenio-cpp-sdk/wiki) for detailed implementation of this project :D
 
 ["keentycs": simple analytics extraction language for KeenIO.](https://github.com/UberSnip/keenio-cpp-sdk/wiki/keentycs)
 
 [keentycs filters.](https://github.com/UberSnip/keenio-cpp-sdk/wiki/keentycs-Filters)
 
 [keentycs Global Variables.](https://github.com/UberSnip/keenio-cpp-sdk/wiki/keentycs-Global-Variables)
 

## [The KeenIO Client](https://github.com/UberSnip/keenio-cpp-sdk/wiki/class::KEENIO_CLIENT)
The KeenIO Client is the main handler for the KeenIO C++ API. Handles include Keen related data such as keys, events, etc.

## [The KeenIO HTTP Client](https://github.com/UberSnip/keenio-cpp-sdk/wiki/class::KEENIO_HTTP)
The HTTP client handles `network` and request related information such as headers, api parameters, and sending/receiving requests. (WinSock!)

### Adding headers

Adding headers ensures that KeenIO understands your request. The following headers are required:

> ("Accept-Encoding", "gzip, deflate, sdch")

> ("Accept-Language", "en-US,en;q=0.8")

> ("Connection", "keep-alive")

> ("Host", "api.keen.io")

> ("User-Agent", "Carrots/KeenIO HTTP-1.0")

Minimal headers can be added via function `KEENIO_HTTP::addDefHeaders()`

	class KEENIO_HTTP{
	
		void addDefHeaders(void);
		
	}
	
	//Example
	KEENIO_CLIENT* kCLIENT;
	kCLIENT->kHTTP.addDefHeaders();
	
	//DATA IS RETURNED AS JSON STRING
	
### Adding URL Parameters
After connecting to the KeenIO API server, this client requests resources via URL. `IE: https://api.keen.io/3.0/projects/PROJECT_ID/events?api_key=1234&timeframe=this_14_days`

Parameters are added in the same manner as `headers`.

	class KEENIO_HTTP{
		void addParam(string head, string content);
	}	

	//Example
	KEENIO_CLIENT* kCLIENT;
	kCLIENT->kHTTP.addParam("api_key", "1234");
	
	//DATA IS RETURNED AS JSON STRING
	
	
#### Adding Base64 parameters.
Data parameters are simply put, URL encoded to Base64, which are required when sending data via `GET`.

	class KEENIO_HTTP{
		void addDataParam(string head, string content);
	}	

	//Example
	KEENIO_CLIENT* kCLIENT;
	kCLIENT->kHTTP.addDataParam("api_key", "1234");
	
	

## Getting project resources

A quick snippet with minimal required code

	//KEENIO_SDK CLIENT
	KEENIO_CLIENT* kCLIENT = new KEENIO_CLIENT();
	
	//Set the request URL
	kCLIENT->kHTTP.reqURL = "https://api.keen.io/3.0/projects/PROJECT_ID/events";
	
	//Add minimal required headers
	kCLIENT->kHTTP.addDefHeaders();

	//ADD Parameter api_key
	kCLIENT->kHTTP.addParam("api_key", "<read_key>");
	kCLIENT->request(kCLIENT->kHTTP);

## Sending an event to KeenIO

	KEENIO_CLIENT* kCLIENT = new KEENIO_CLIENT();
	kCLIENT->kHTTP.reqURL = "https://api.keen.io/3.0/projects/PROJECT_ID/events";
	kCLIENT->kHTTP.addDefHeaders();

	kCLIENT->kHTTP.addParam("api_key", "<master_key>");

	kCLIENT->kHTTP.addDataParam("data", {"app_event","xLAUNCH"} );
	kCLIENT->method(KEENIO_HTTP_GET);
	kCLIENT->request(kCLIENT->kHTTP);

	printf(kCLIENT->body.c_str());
	
# Examples

[Inspect single project](https://github.com/UberSnip/keenio-cpp-sdk/wiki/EXAMPLE::InspectProject)

[Inspect all collections](https://github.com/UberSnip/keenio-cpp-sdk/wiki/EXAMPLE::InspectAllCollections)
	
## Complete example	

	#pragma once
	#include "KEENIO_SDK.h"

	// SEND DATA TO KEENIO
	//EVENT = app_event, DATA = 'xLAUNCH' Base64Encoded
	int sendData(){
		KEENIO_CLIENT* kCLIENT = new KEENIO_CLIENT();
		kCLIENT->kHTTP.reqURL = "https://api.keen.io/3.0/projects/PROJECT_ID/events/COLLECTION_NAME";
		kCLIENT->kHTTP.addDefHeaders();

		kCLIENT->kHTTP.addParam("api_key", "<master_key>");

		string dataParam[2] = { "app_event", "xLAUNCH" };
		kCLIENT->kHTTP.addDataParam("data", dataParam);
		kCLIENT->method(KEENIO_HTTP_GET);
		kCLIENT->request(kCLIENT->kHTTP);

		printf(kCLIENT->body.c_str());
	
		return 1;
	}

	int main() {
		KEENIO_QUERYLANGUAGE::KEENIO_QUERY* keenQL = new KEENIO_QUERYLANGUAGE::KEENIO_QUERY();
		keenQL->KEY("<write_key>");
		keenQL->QueryExec(".export=example_exp.txt extraction pageview({$PROJECT_ID}) event_collection=pageview timezone=UTC timeframe=this_100_days if keen.id>0 && path<>'//'");

		printf("---QUERY REQUEST---\n%s", (char*)keenQL->ProcessQuery().c_str());
	
	
		KEENIO_CLIENT* kCLIENT = new KEENIO_CLIENT();
		kCLIENT->kHTTP.reqURL = "https://api.keen.io/3.0/projects/PROJECT_ID/events";
		kCLIENT->kHTTP.addDefHeaders();

		kCLIENT->kHTTP.addParam("api_key", "<read_key>");
		kCLIENT->kHTTP.addParam("event_collection", "pageview");
		kCLIENT->kHTTP.addParam("timezone", "UTC");
		kCLIENT->kHTTP.addParam("timeframe", "this_14_days");

		kCLIENT->method(KEENIO_HTTP_GET);
		kCLIENT->request(kCLIENT->kHTTP);

		printf(kCLIENT->body.c_str());
	
		sendData();
	
		return 0;
	}

---

### Managed C++ Version (App-Store ready)

The managed C++ version is app-store ready, and simplifies the KeenIO API to minimal lines of code, while achieving the same results.

Documentation on managed code can be found via [Wiki](https://github.com/UberSnip/keenio-cpp-sdk/wiki/Managed-Code)

##### Example : Load project resources

	// Load the project, all of it's collections, and the properties of each collection
	KeenIOAPI::KEENIO_PROJECT^ keen_project = ref new KeenIOAPI::KEENIO_PROJECT();
	keen_project->ID = "";
	keen_project->MasterKey = "";
	keen_project->LoadProject();
	
	//Sending an event to KeenIO
	keen_project->SendEvent("COLLECTION_NAME", "EVENT_NAME", "EVENT_VALUE");
	
	//Receive KeenIO API Request (JSON String)
	keen_project->ReceiveData("api_url_with_parameters");
	

Disclaimer: This product is in no way affiliated with KeenIO -- This is a community project, though, we would be very open to KeenIO picking up the project in the future! :D
