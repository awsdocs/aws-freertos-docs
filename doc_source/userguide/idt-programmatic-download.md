# Download IDT for FreeRTOS<a name="idt-programmatic-download"></a>

This topic describes the options to download IDT for FreeRTOS\. You can either use one of the following software download links or you can follow instructions to programmatically download IDT\.

**Topics**
+ [Download IDT manually](#idt-download-options)
+ [Download IDT programmatically](#idt-programmatic-download-process)

By downloading the software, you agree to the [AWS IoT Device Tester License Agreement](https://docs.aws.amazon.com/greengrass/v2/developerguide/idt-license.html)\.

**Note**  
IDT does not support being run by multiple users from a shared location, such as an NFS directory or a Windows network shared folder\. We recommend that you extract the IDT package to a local drive and run the IDT binary on your local workstation\.

## Download IDT manually<a name="idt-download-options"></a>

This topic lists supported versions of IDT for FreeRTOS\. As a best practice, we recommend that you use the latest version of AWS IoT Device Tester that supports your target version of FreeRTOS\. New releases of FreeRTOS might require you to download a new version of AWS IoT Device Tester\. You receive a notification when you start a test run if AWS IoT Device Tester is not compatible with the version of FreeRTOS you are using\.

See [Supported versions of AWS IoT Device Tester for FreeRTOS](dev-test-versions-afr.md)

## Download IDT programmatically<a name="idt-programmatic-download-process"></a>

IDT provides an API operation that you can use to retrieve a URL where you can download IDT programmatically\. You can also use this API operation to check if you have the latest version of IDT\. This API operation has the following endpoint\.

```
https://download.devicetester.iotdevicesecosystem.amazonaws.com/latestidt
```

To call this API operation, you must have permission to perform the **iot\-device\-tester:LatestIdt** action\. Include your AWS signature, with `iot-device-tester` as the Service Name

### API request<a name="idt-programmatic-download-request"></a>

HostOs – The operating system of the host machine\. Choose from the following options:  
+ `mac`
+ `linux`
+ `windows`

TestSuiteType – The type of the test suite\. Choose the following option:  
`FR` – IDT for FreeRTOS

ProductVersion  
\(Optional\) The version of FreeRTOS\. The service returns the latest compatible version of IDT for that version of FreeRTOS\. If you don't specify this option, the service returns the latest version of IDT\.

### API response<a name="idt-programmatic-download-response"></a>

The API response has the following format\. The `DownloadURL` includes a zip file\.

```
{
    "Success": True or False,
    "Message": Message,
    "LatestBk": {
        "Version": The version of the IDT binary,
        "TestSuiteVersion": The version of the test suite,
        "DownloadURL": The URL to download the IDT Bundle, valid for one hour
    }
 }
```

### Examples<a name="idt-programmatic-download-examples"></a>

You can reference the following examples to programmatically download IDT\. These examples use credentials that you store in the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables\. To follow best security practices, don't store your credentials in your code\.

**Example: Download using cURL version 7\.75\.0 or later \(Mac and Linux\)**  
If you have cURL version 7\.75\.0 or later, you can use the `aws-sigv4` flag to sign the API request\. This example uses [jq](https://stedolan.github.io/jq/) to parse the download URL from the response\.  
+ Replace *us\-west\-2* with your AWS Region\. For the list of Region codes, see [Regional endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints)\.
+ Replace *linux* with your host machine's operating system\.
+ Replace *202107\.00* with your version of FreeRTOS\.

```
url=$(curl --request GET "https://download.devicetester.iotdevicesecosystem.amazonaws.com/latestidt?HostOs=linux&TestSuiteType=FR&ProductVersion=202107.00" \
--user $AWS_ACCESS_KEY_ID:$AWS_SECRET_ACCESS_KEY \
--aws-sigv4 "aws:amz:us-west-2:iot-device-tester" \
| jq -r '.LatestBk["DownloadURL"]')

curl $url --output devicetester.zip
```

**Example: Download using an earlier version of cURL \(Mac and Linux\)**  
You can use the following cURL command with an AWS signature that you sign and calculate\. For more information about how to sign and calculate an AWS signature, see [Signing AWS API requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html)\.  
+ Replace *linux* with your host machine's operating system\.
+ Replace *Timestamp* with the date and time, such as **20220210T004606Z**\.
+ Replace *Date* with the date, such as **20220210**\.
+ Replace *AWSRegion* with your AWS Region\. For the list of Region codes, see [Regional endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.
+ Replace *AWSSignature* with the [AWS signature](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) that you generate\.

```
curl --location --request GET 'https://download.devicetester.iotdevicesecosystem.amazonaws.com/latestidt?HostOs=linux&TestSuiteType=FR' \
--header 'X-Amz-Date: Timestamp \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=$AWS_ACCESS_KEY_ID/Date/AWSRegion/iot-device-tester/aws4_request, SignedHeaders=host;x-amz-date, Signature=AWSSignature'
```

**Example: Download using a Python script**  
This example uses the Python [requests](https://pypi.org/project/requests/) library\. This example is adapted from the Python example to [Sign an AWS API request](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) in the *AWS General Reference*\.    
  
+ Replace *us\-west\-2* with your region\. For the list of Region codes, see [Regional endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.
+ Replace *linux* with your host machine's operating system\.

```
# Copyright 2010-2022 Amazon.com, Inc. or its affiliates. All Rights Reserved.
#
# This file is licensed under the Apache License, Version 2.0 (the "License").
# You may not use this file except in compliance with the License. A copy of the
#License is located at
#
# http://aws.amazon.com/apache2.0/
#
# This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS
# OF ANY KIND, either express or implied. See the License for the specific
# language governing permissions and limitations under the License.

# See: http://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html
# This version makes a GET request and passes the signature
# in the Authorization header.
import sys, os, base64, datetime, hashlib, hmac 
import requests # pip install requests
# ************* REQUEST VALUES *************
method = 'GET'
service = 'iot-device-tester'
host = 'download.devicetester.iotdevicesecosystem.amazonaws.com'
region = 'us-west-2'
endpoint = 'https://download.devicetester.iotdevicesecosystem.amazonaws.com/latestidt'
request_parameters = 'HostOs=linux&TestSuiteType=FR'

# Key derivation functions. See:
# http://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html#signature-v4-examples-python
def sign(key, msg):
    return hmac.new(key, msg.encode('utf-8'), hashlib.sha256).digest()

def getSignatureKey(key, dateStamp, regionName, serviceName):
    kDate = sign(('AWS4' + key).encode('utf-8'), dateStamp)
    kRegion = sign(kDate, regionName)
    kService = sign(kRegion, serviceName)
    kSigning = sign(kService, 'aws4_request')
    return kSigning

# Read AWS access key from env. variables or configuration file. Best practice is NOT
# to embed credentials in code.
access_key = os.environ.get('AWS_ACCESS_KEY_ID')
secret_key = os.environ.get('AWS_SECRET_ACCESS_KEY')
if access_key is None or secret_key is None:
    print('No access key is available.')
    sys.exit()

# Create a date for headers and the credential string
t = datetime.datetime.utcnow()
amzdate = t.strftime('%Y%m%dT%H%M%SZ')
datestamp = t.strftime('%Y%m%d') # Date w/o time, used in credential scope

# ************* TASK 1: CREATE A CANONICAL REQUEST *************
# http://docs.aws.amazon.com/general/latest/gr/sigv4-create-canonical-request.html
# Step 1 is to define the verb (GET, POST, etc.)--already done.
# Step 2: Create canonical URI--the part of the URI from domain to query 
# string (use '/' if no path)
canonical_uri = '/latestidt' 
# Step 3: Create the canonical query string. In this example (a GET request),
# request parameters are in the query string. Query string values must
# be URL-encoded (space=%20). The parameters must be sorted by name.
# For this example, the query string is pre-formatted in the request_parameters variable.
canonical_querystring = request_parameters
# Step 4: Create the canonical headers and signed headers. Header names
# must be trimmed and lowercase, and sorted in code point order from
# low to high. Note that there is a trailing \n.
canonical_headers = 'host:' + host + '\n' + 'x-amz-date:' + amzdate + '\n'
# Step 5: Create the list of signed headers. This lists the headers
# in the canonical_headers list, delimited with ";" and in alpha order.
# Note: The request can include any headers; canonical_headers and
# signed_headers lists those that you want to be included in the 
# hash of the request. "Host" and "x-amz-date" are always required.
signed_headers = 'host;x-amz-date'
# Step 6: Create payload hash (hash of the request body content). For GET
# requests, the payload is an empty string ("").
payload_hash = hashlib.sha256(('').encode('utf-8')).hexdigest()
# Step 7: Combine elements to create canonical request
canonical_request = method + '\n' + canonical_uri + '\n' + canonical_querystring + '\n' + canonical_headers + '\n' + signed_headers + '\n' + payload_hash

# ************* TASK 2: CREATE THE STRING TO SIGN*************
# Match the algorithm to the hashing algorithm you use, either SHA-1 or
# SHA-256 (recommended)
algorithm = 'AWS4-HMAC-SHA256'
credential_scope = datestamp + '/' + region + '/' + service + '/' + 'aws4_request'
string_to_sign = algorithm + '\n' +  amzdate + '\n' +  credential_scope + '\n' +  hashlib.sha256(canonical_request.encode('utf-8')).hexdigest()
# ************* TASK 3: CALCULATE THE SIGNATURE *************
# Create the signing key using the function defined above.
signing_key = getSignatureKey(secret_key, datestamp, region, service)
# Sign the string_to_sign using the signing_key
signature = hmac.new(signing_key, (string_to_sign).encode('utf-8'), hashlib.sha256).hexdigest()

# ************* TASK 4: ADD SIGNING INFORMATION TO THE REQUEST *************
# The signing information can be either in a query string value or in 
# a header named Authorization. This code shows how to use a header.
# Create authorization header and add to request headers
authorization_header = algorithm + ' ' + 'Credential=' + access_key + '/' + credential_scope + ', ' +  'SignedHeaders=' + signed_headers + ', ' + 'Signature=' + signature
# The request can include any headers, but MUST include "host", "x-amz-date", 
# and (for this scenario) "Authorization". "host" and "x-amz-date" must
# be included in the canonical_headers and signed_headers, as noted
# earlier. Order here is not significant.
# Python note: The 'host' header is added automatically by the Python 'requests' library.
headers = {'x-amz-date':amzdate, 'Authorization':authorization_header}

# ************* SEND THE REQUEST *************
request_url = endpoint + '?' + canonical_querystring
print('\nBEGIN REQUEST++++++++++++++++++++++++++++++++++++')
print('Request URL = ' + request_url)
response = requests.get(request_url, headers=headers)
print('\nRESPONSE++++++++++++++++++++++++++++++++++++')
print('Response code: %d\n' % response.status_code)
print(response.text)

download_url = response.json()["LatestBk"]["DownloadURL"]
r = requests.get(download_url)
open('devicetester.zip', 'wb').write(r.content)
```