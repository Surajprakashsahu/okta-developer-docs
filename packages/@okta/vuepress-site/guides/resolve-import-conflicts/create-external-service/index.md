---
title: Creating Your External Service
---

The external service is the customer-provided piece of software that Okta calls when the inline hook fires. In the case of the Import Inline Hook, the payload of the call contains the user profile information of the user being imported and contextual information on the import process. The external service can respond with commands to change profile attributes and to set whether to treat the user as a match for an existing user or not.

Okta defines the REST API contract for the requests it sends to the external service and for the responses the external service can send back, but it's your responsibility to arrange hosting of the external service and to code the software for it.

The following sections demonstrate some sample code for an external service. The code here is meant as a demonstration of checking for conflict and then resolving the conflict by changing a user profile attribute.

### Create an HTTPS Server

Your service needs to implement an HTTPS server for Okta to send requests to. To create an HTTPS server, you need to have available an SSL key pair (PEM file) and its corresponding certificate (cert file) for the server to use.

<StackSelector snippet="create-https"/>

### Check Authorization Header

The requests that Okta sends to your external service will always include an authorization header containing a secret string. You set the value of this string when you register your external service, so that it can serve as an API access key for the service. Your code should always check for the presence of the authorization header and confirm its value. Processing should not proceed if the auhorization header cannot be verified.

<StackSelector snippet="check-auth"/>

### Deserialize JSON Payload

Information about the user being imported, as well as contextual information about the import process, is contained in the JSON payload of the request from Okta. To make it available for pocessing, let's deserialize the JSON into objects.

<StackSelector snippet="deserialize"/>


### Check for Conflict in login Attribute

If the value of `data.user.profile.login` for an incoming user is equal to the value of that attribute in another user profie, there is a conflict. As a demonstration only, the way we'll handle the conflict is to insert a unique integer into the value.

<StackSelector snippet="detect-conflict"/>

### Formulate Commands to Return

The way to update the value Okta assigns to a user profile attribute is to return a `commands` object, specifying the attribute and the value to set it to. Here, we will construct a `commands` object to set the value of `data.user.profile.login`. 

<StackSelector snippet="construct-commands-object"/>

Note that `commands` is an array, but in this case, only one command is being returned. 

### Serialize Response and Send

Finally, the `commands` object we constructed needs to be serialized into JSON, so that it can form the payload of the HTTP response we return to Okta. The response sent should have an HTTP status code of 200 OK.

<StackSelector snippet="serialize"/>

<NextSectionLink />
