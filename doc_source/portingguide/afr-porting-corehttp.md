# Configuring the coreHTTP library<a name="afr-porting-corehttp"></a>

Devices on the edge can use the HTTP protocol to communicate with the AWS Cloud\. AWS IoT services host an HTTP server that sends and receives messages to and from connected devices at the edge\. 

## Testing<a name="testing-corehttp"></a>

Follow the steps below for testing:
+ Setup the PKI for TLS mutual authentication with AWS or an HTTP server\.
+ Run CoreHTTP integration tests\.