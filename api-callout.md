### Callout API end point 
* Create a controller for callout and define a function 
```java
public class PositionsController {
    public void GetCandidatePositions() {
        // Instantiate a new http object
        Http h = new Http();
        // Instantiate a new HTTP request, specify the method (GET) as well as the endpoint
        HttpRequest req = new HttpRequest();
        req.setEndpoint('https://www.eventbriteapi.com/v3/users/me/?token=XXXXXXXXXXXXXX');
        req.setMethod('GET');
        HttpResponse res = h.send(req);
        String responseJson = res.getBody();
```
* However some cases like Eventbrite they return `error 301` in first hit, then we need a second hit following above codes
```java
         String loc = res.getHeader('Location'); // get location of the redirect
        System.debug(loc); 
         req = new HttpRequest(); 
         req.setEndpoint(loc+'/?token=xxxxxxxxxx');
         req.setMethod('GET');
         req.setHeader('Content-Type', 'application/json');
         req.setHeader('Accept','application/json');
         res = h.send(req);
         System.debug(res.getBody());
    }
}
```
* To parse json return body we first map the results into string: list(objects) format. If the end point is like 
```java
{
    "emails": [
        {
            "email": "amirxxxx@gmail.com", 
            "verified": false, 
            "primary": true
        }
    ], 
    "id": "2355xxxxxxxx", 
    "name": "sfu3 xxxx", 
    "first_name": "sfuxxxx", 
    "last_name": "Txxxxx", 
    "is_public": false, 
    "image_id": null
}
```
* Then the parsing format is like 
```java
 Map<String, Object> m = (Map<String, Object>)JSON.deserializeUntyped(res.getBody()); 
 List<Object> positions = (List<Object>) m.get('emails');
 for (Object item : positions) {
           Map<String, Object> i = (Map<String, Object>)item;
           System.debug(i.get('email')); //which returns amirxxxx@gmail.com
        }
```
* Helpfull [link](https://stackoverflow.com/questions/14912599/parsing-json-object-in-salesforce-apex)

* To display results in a view, we need to create an apex form and call controller, then assign action and variable as below
```java
<apex:page controller="PositionsController">
  <style type="text/css">
      .card {
          float: left;
          height: 60px;
      } </style>
  
  <center> <h1 style="font-size:16px; color:grey">List of Events</h1> 
    <br /><br />
  <apex:form > 
      <apex:CommandButton value="Synch with Eventbrite" action="{!GetCandidatePositions}"/><br /><br />
      <apex:outputText value="{!PositionsList}" escape="false"></apex:outputText>
  </apex:form>
  </center>
</apex:page>
```
* Inside `PositionsController` define `PositionsList` string to concat results as 
```java
private String PositionsList;
String positionString = ' ';
positionString += '<td class=\'tr\'>'+ names.get('text') + '</td>';
PositionsList = positionString;
```
##### Derialization and Deserialization in Java. 
 Serialization is a process of converting an object into a sequence of bytes which can be persisted to a disk or database or can be sent through streams like JSON format. The reverse process of creating object from sequence of bytes is called deserialization.

#### Mapping JSON Objects
* If we have `{events:[ {name:{},...},{},{}],other:.. }`
* Map a list of JSON object into a list as
```java
List<Object> positions = (List<Object>) m.get('events');
```
* Then need to go through each object with a for loop and map pair string, objects to be able to call objects with . followed by stringname
* We map json objects into a pair of (string,object). Then to access the object call the string as 
```java
i.get('status')
```
* Objects can be nested so we again map them into pairs of string, objects. 
* If there were repeated objects we can use for loop as below then map them
```java
 for (Object item : positions) {
  Map<String, Object> i = (Map<String, Object>)item;
```
