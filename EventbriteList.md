
* This is the EventbriteList hiting two times the end point and pass the results to Eventlists view as came followed
```java
public class Eventslist {

    //private variable
    private String Eventinfo;
    private String name;
    private String startdate;
    private String enddate;
    private String status;
    private String createdat;
    private String urllink;
    private String id;
    public String Getlists(){
      return Eventinfo;
    }    


    //function called by view
    public String GetEventinfo() { 


        // Instantiate a new http object
        Http h = new Http();

        // Instantiate a new HTTP request, specify the method (GET) as well as the endpoint
        HttpRequest req = new HttpRequest();
        req.setHeader('Authorization', 'bearer BOJPDFI7HSHDCV6WQZAL');
        req.setEndpoint('https://www.eventbriteapi.com/v3/users/me/owned_events/?token=BOJPDFI7HSHDCV6WQZAL');
        req.setMethod('GET');
        HttpResponse res = h.send(req);
        String responseJson = res.getBody();
        List<String> thelist = new List<String>();
         String loc = res.getHeader('Location'); // get location of the redirect
         req = new HttpRequest();
         req.setEndpoint('https://www.eventbriteapi.com/v3/users/me/owned_events/?token=BOJPDFI7HSHDCV6WQZAL');
         req.setMethod('GET');
         req.setHeader('Content-Type', 'application/json');
         req.setHeader('Accept','application/json');
         res = h.send(req);
         Map<String, Object> m = (Map<String, Object>)JSON.deserializeUntyped(res.getBody());
         
        
        
         List<Object> positions = (List<Object>) m.get('events');
         
         String positionString = '<table class=\'table-bordered table-hover th\'>';
         positionString += '<tr> <th class=\'tr\'>Event Name</th> <th class=\'tr\'>Start Date</th>  <th class=\'tr\'>End Date</th>  <th class=\'tr\'>Status</th>  <th class=\'tr\'>Created at</th>  <th class=\'tr\'>Link to Event</th><th class=\'tr\'>Contact List</th></tr>';
        for (Object item : positions) {
           Map<String, Object> i = (Map<String, Object>)item;
                   Map<String, Object> names = (Map<String, Object>) i.get('name');
                      
                         name = String.valueof(names.get('text'));      
                         Map<String, Object> starts = (Map<String, Object>) i.get('start'); 
                           startdate = String.valueof(starts.get('local'));                        
                         Map<String, Object> ends = (Map<String, Object>) i.get('end'); 
                            enddate = String.valueof(ends.get('local'));
                         createdat = String.valueof(i.get('created'));
                         status = String.valueof(i.get('status'));
                         urllink = String.valueof(i.get('url'));
                         id = String.valueof(i.get('id'));
               //////////////// Check campaign exists /////////////////  
             
              ///////////////////////////////////////////////////////
              
              ///////////// Create campaigns /////////////////////// 
               Campaign new1 = new Campaign(Name= name, EndDate= Date.parse(enddate),
                          StartDate= Date.parse(startdate),
                          Status =  status        
                              );
               insert new1;
              
              ///////////////////////////////////////////////////////
              
               positionString += '<td class=\'tr\'>'+  name + '</td>';
               positionString += '<td class=\'tr\'>'+ startdate + '</td>';
               positionString += '<td class=\'tr\'>'+ enddate + '</td>';
               positionString += '<td class=\'tr\'>'+ status + '</td>';
               positionString += '<td class=\'tr\'>'+ createdat + '</td>'; 
              // positionString += '<td class=\'tr\'>'+ ends.get('local') + '</td>';
               positionString += '<td><button><a href=\''+ urllink + '\'>Event</a></button></td>';
               positionString += '<td><a href=\'https://c.na73.visual.force.com/apex/orders?Id='+ id +' \'>List</a></td></tr>';
                      
        }

        positionString += '</table>';
        Eventinfo = positionString;
      //  System.debug('_____'+Eventinfo+'_______');
       return Eventinfo;

    }

}

```
* Eventlists view
```java
<apex:page controller="Eventslist" standardStylesheets="false" >

<apex:includeScript value="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"/>
<apex:includeScript value="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js" />
<apex:stylesheet value="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css"/>
  <style type="text/css">
      .card {
          float: left;
          height: 60px;
          width: 80px;
          padding: 10px 10px 10px 10px;
          text-align: center;
          border: 1px solid black;
          margin-right: 10px;
          background-color: red;
          color: white;
          font-weight: bold;
          font-size: 14px;
      }
        
      .tr  {
   border-bottom: 1px solid grey;
   border-left: 1px solid grey;
}
       .th  {
    border: 1px solid grey;
}

  </style>
  <br />
  <center> <h1 style="font-size:16px; color:grey">List of Events</h1> 
    <br /><br />
  <apex:form > 
     
      <apex:outputText value="{!Eventinfo}" escape="false"></apex:outputText>
  </apex:form>
  </center>
</apex:page>
```

* Check whether account already exist or not then create it
```java
public List<Campaign> c;
public String a;
public void createcam(){
       a = '33301name';
       c = [SELECT Id, Name FROM Campaign where Name =: a ]; 
       if(c.size()<1)  {
           Campaign new1 = new Campaign(Name= a, 
                          Status = 'live'       
                              );
                insert new1;  
       }
   }
```
* Check an object like Campaign in salesforce and retrieve data attribute like 'Name' ftom it to compare 
```java
for (Campaign it : c) {
                    if (it.Name == 'alafei') {positionString += '<td class=\'tr\'>no'+ it.Name +'</td>'; }
                 
                } 
```
*Convert string to array of chars and compare them
```java       String a = 'yes'; 
              String[] ass = name.split('');
              String[] bss = b.split('');
                
             for(integer ii=0; ii<ass.size();ii++)
                {
                    if(!b.contains(String.valueof(ass[ii])))
                    {
                     a = 'no';     
                    }
                    
                }
```
### Mapping Events to campaign 
* Go through all Events at A loop
* Go through all campaigns at B loop inside A
* Check one by one if exists save a temp variable `yes` otherwise `no`
* Since this temp variable would be overriden in next loop inside B so it is better to insert this temp inside a Set or array. (set is an array unrepeatable). so if the set size is one so includes only no but if is 2 so it includes yes and we can find which one existed. 
```java
Set<String> sds = new Set<String>();
             for (Campaign it : c) {
                a = 'yes';
                b = it.Name;
                String[] ass = name.split('');
                String[] bss = b.split('');  
             for(integer ii=0; ii<ass.size();ii++)
                {
                    if(!b.contains(String.valueof(ass[ii])) && ii<bss.size())
                    {
                      a = 'no'; 
                    }
                }
               sds.add(a);  
             }
```
* The main reason that the trivial solution doesnt work because in trivial we say if it match then return yes otherwise no. Since it check charactor by charactor so by comparing partial characotrs we can not make sure these are identical. so we use not equal 

### Split data and convert it to Data format for salesforce objects
```java
 temp = startdate.split('T');           
 temp = temp[0].split('-');  
 StartDate = date.parse(temp[1]+'/'+temp[2]+'/'+temp[0]); 
```
### Catch DML Error by defining a rollback action 

```java
 Savepoint sp = Database.setSavepoint();
            CampaignMember newt = new CampaignMember(CampaignId = '7011I000000d2P1', ContactId= idforcontact , Status='Opt-In');
           try {
            insert newt;
           }
             catch(dmlexception e) {
             Database.rollback(sp);
             }
         }
```
#### Eventbrite list to create campaign and contact only if not repeatet!
* Inclucdes checking wheter exist or not and if not then save it
```java
public class Eventslist {
    
    public List<Campaign> c;
    public Eventslist()
    {
         c = [SELECT Name FROM Campaign]; 
    }

    //private variable
    private String Eventinfo;
    private String name;
    private String details;
    private String startdate;
    private String enddate;
    private String status;
    private String createdat;
    private String urllink;
    private String id;
    List<object> findcamp;
    
    public String idString {get;set;}
    public String a;
    public String b;
    List<String> fff;
    
    public String Getlists(){
      return Eventinfo;
    }    
    //function called by view
    public String GetEventinfo() { 


        // Instantiate a new http object
        Http h = new Http();
        HttpRequest req = new HttpRequest();
        req.setHeader('Authorization', 'bearer BOJPDFI7HSHDCV6WQZAL');
        req.setEndpoint('https://www.eventbriteapi.com/v3/users/me/owned_events/?token=BOJPDFI7HSHDCV6WQZAL');
        req.setMethod('GET');
        HttpResponse res = h.send(req);
        String responseJson = res.getBody();
        List<String> thelist = new List<String>();
         String loc = res.getHeader('Location'); // get location of the redirect
         req = new HttpRequest();
         req.setEndpoint('https://www.eventbriteapi.com/v3/users/me/owned_events/?token=BOJPDFI7HSHDCV6WQZAL');
         req.setMethod('GET');
         req.setHeader('Content-Type', 'application/json');
         req.setHeader('Accept','application/json');
         res = h.send(req);
         Map<String, Object> m = (Map<String, Object>)JSON.deserializeUntyped(res.getBody());
      
         List<Object> positions = (List<Object>) m.get('events');
         
         String positionString = '<table class=\'table-bordered table-hover th\'>';
         positionString += '<tr> <th class=\'tr\'>Event Name</th> <th class=\'tr\'>Start Date</th>  <th class=\'tr\'>End Date</th>  <th class=\'tr\'>Status</th>  <th class=\'tr\'>Created at</th>  <th class=\'tr\'>Link to Event</th><th class=\'tr\'>Contact List</th></tr>';
        for (Object item : positions) {
           Map<String, Object> i = (Map<String, Object>)item;
                   Map<String, Object> names = (Map<String, Object>) i.get('name');
                      
                         name = String.valueof(names.get('text'));      
                         Map<String, Object> starts = (Map<String, Object>) i.get('start'); 
                           startdate = String.valueof(starts.get('local'));                        
                         Map<String, Object> ends = (Map<String, Object>) i.get('end'); 
                            enddate = String.valueof(ends.get('local'));
                         createdat = String.valueof(i.get('created'));
                         status = String.valueof(i.get('status'));
                         urllink = String.valueof(i.get('url'));
                         id = String.valueof(i.get('id'));
               //////////////// Check campaign exists /////////////////

           
            Set<String> sds = new Set<String>();
             for (Campaign it : c) {
                a = 'yes';
                b = it.Name;
                String[] ass = name.split('');
                String[] bss = b.split('');    
             for(integer ii=0; ii<ass.size();ii++)
                {
                    if(!b.contains(String.valueof(ass[ii])) && ii<bss.size())
                    {
                      a = 'no';                         
                    }
                }
               sds.add(a);  
              
             }
            
            if(sds.size()>1){
               positionString += '<td><a href=\'https://c.na73.visual.force.com/apex/notsaveorders?Id='+ id +
                  '&name='+ name +
                  '&startdate='+startdate+
                  '&enddate='+enddate+
                  '&status='+status+
                  '&createdat='+createdat+' \'>Campaign</a></td>';
                
            }
            else
            {
                 positionString += '<td><a href=\'https://c.na73.visual.force.com/apex/notsaveorders?Id='+ id +
                  '&name='+ name +
                  '&startdate='+startdate+
                  '&enddate='+enddate+
                  '&status='+status+
                  '&createdat='+createdat+' \'>New Campaign</a></td>'; 
            }
                           
               positionString += '<td class=\'tr\'>'+  name + '</td>';
               positionString += '<td class=\'tr\'>'+ startdate + '</td>';
               positionString += '<td class=\'tr\'>'+ enddate + '</td>';
               positionString += '<td class=\'tr\'>'+ status + '</td>';
               positionString += '<td class=\'tr\'>'+ createdat + '</td>'; 
              // positionString += '<td class=\'tr\'>'+ ends.get('local') + '</td>';
               positionString += '<td><button><a href=\''+ urllink + '\'>Event</a></button></td>';
               positionString += '<td><a href=\'https://c.na73.visual.force.com/apex/orders?Id='+ id +' \'>List</a></td></tr>';
                      
        }

        positionString += '</table>';
        Eventinfo = positionString;
      //  System.debug('_____'+Eventinfo+'_______');
     // creme();
       return Eventinfo;
    }
}
```
