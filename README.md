# Xfoil-Client

This SDK will enable the client to call Xfoil’s Services(APIs) and do the following:

## Current released version

```bash

version = 1.0.16

```

## Installation for Android

Import these in the repository at project-level build.gradle 

```bash

allprojects {
   repositories {
       maven { url 'https://jitpack.io' }
   }
}

```

Import this client in the dependencies at the app-level build.gradle 

```bash

implementation 'com.github.dev-xfoil:xfoil-client:<version>' [version: 1.0.15]


```

Add these rules in your android project proguard-rules.pro

```bash

-keep class dev.xfoil** { ; }
-keep class com.google.firebase{;}

```


## Usage

First, you need to initialize the xfoil-client with <API_KEY> provided to you by the XFOIL before using any of the services.

```java

XfoilClient.Init("<SERVER_URL>","<API_KEY>");

```
Once the client is initialized, you should execute the following steps in order to deposit cash
1) Create a rider (one time operation)
2) Generate QR code
3) Login on CDM

## Create rider
To Create Rider following steps to be followed
1) Fetch Regions
2) Fetch Locations for a region
3) Create Rider for that region and location

```java

RiderServices riderServices = RiderServicesImplementation.getInstance();
CompanyServices companyServices = CompanyServicesImplementation.getInstance();

# fetch reigons
response = companyServices.getCompanyRegions();


# fetch locations
response = companyServices.getLocationsByRegions(String region);

# create rider
response = riderService.createRider(
    String firstName, 
    String lastName, 
    String cnic, 
    String phoneNumber,
    String agentId,  
    String officeLocation, 
    String officeRegion);

        OR

response = riderService.createRider(
    String firstName,
    String lastName,
    String cnic,
    String phoneNumber,
    String agentId,
    String officeRegion);

```

## Generate Qrcode 
For Rider Authentication, Qrcode needs to be generated by providing anyone of the following cnic/agentId/phonenumber.

```java

response = riderService. generateQrCode(String cnicAgentIdPhone, String officeRegion);

        OR

response = riderService.generateQrCode(String cnicAgentIdPhone, String officeLocation, String officeRegion);

```

## Transaction Services

This service class have services related to Transactions

### DepositCash
This method can be called to send encrypted qr code payload to the backend to create a transaction record in our system in case there is a failure at the machine. This replay mechanism is designed on the following principles:
1. CDM will generate a QR for each transaction that can be scanned in the app and the payload can be send back using the method depositCash
2. QR code can be scanned multiple times and will result in only a single entry being created in our system (idempotent)

```java

TransactionBalanceService transactionBalanceService = TransactionBalanceServiceImplementation.getInstance();

# transaction depositToAcccount

response = transactionBalanceService.depositCash(String scannedQrcodeString);

```
### SetRiderDueBalance
This method is used to set due amount for a rider 

```java

BalanceResponse setRiderDueBalance(Long riderId, String companyCode, String amountReceived, String amountReturn);

```

## Server response

The server will return a 0 in response code if an operation is successful

## Error handling

The following class will capture the response from the server

```java
"response_status":{"errorCode":0,"errorDescription":"error in frame handler","isSuccess":false}
```

## Errors

The following errors are defined along with their error code

```java

1) "response_status":{"errorCode":5,"errorDescription":"rider not found","isSuccess":false}
2) "response_status":{"errorCode":5,"errorDescription":"company not found","isSuccess":false}
3) "response_status":{"errorCode":6,"errorDescription":"rider already exits","isSuccess":false} 
4) "response_status":{"errorCode":5,"errorDescription":"locations not available","isSuccess":false}
5) "response_status":{"errorCode":5,"errorDescription":"regions not available","isSuccess":false}
6) "response_status":{"errorCode":3,"errorDescription":"phone number fails validation","isSuccess":false}
7) "response_status":{"errorCode":3,"errorDescription":"cnic fails validation","isSuccess":false}
8) "response_status":{"errorCode":3,"errorDescription":"phone number already exists for this location","isSuccess":false}
9) "response_status":{"errorCode":3,"errorDescription":"cnic already exists for this location","isSuccess":false}
10) "response_status":{"errorCode":13,"errorDescription":"rider not created. please try again","isSuccess":false}
11) "response_status":{"errorCode":6,"errorDescription":"rider already exists with the similar agentId, cnic, phone number","isSuccess":false}
12) "response_status":{"errorCode":4,"errorDescription":"deadline exceeded","isSuccess":false}
```

## Note

- For the utlization of other services please follow the document provided to you by the company.
- If registering the user/rider with OfficeLocation, then you must provide the officeLocation at the time of user/rider authentication. 
