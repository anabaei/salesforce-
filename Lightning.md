## Lightning Basic
* Lightning Experience is a modern user interface, while it doesnt hava features like `fead and publisher` or `clean button` or `javascript button` or `customizing objects` but it has more features from [here](https://trailhead.salesforce.com/modules/lex_migration_introduction/units/lex_migration_introduction_rightforme) 
* You can customize using users from lightening by assiging permission set or by changing in their custom profiles 
* It uses javascript in client side and apex in server side to retrive data
* Kanban view allows you to drag, drop elements 
* Choose the domain: `setup>mydomain` and set the domain to deploy to users as `setup>domain>click on your domain>finsh up deploy to users`
* Add lightening inspector from google chrome store to inspect lightening 
* Each component has cmp file like view, css file and js controller file. To see components we have to create app and then use `<c:componentname anyattribute! />` then save and have preview click
### Controller and Data
```javascript
<aura:component >
 <aura:attribute name="cases" type="Case[]" />  // type of collection of case records     
  // Handler specifies an action:
    <aura:handler name="init" value="{!this}" action="{!c.doInit}" />  
  // iterate component to iterate through the collection, v.cases sayign get data from above and assign a variable name for each case as 
    <aura:iteration items="{!v.cases}" var="case" >
  // show to display each case, we could use outputtext component as well. here each record is a variable from our iteration 
       <div>
        <force:recordView recordId="{!case.Id}" type="FULL" /> 
       </div>
        
    </aura:iteration>
</aura:component >
```
* In controller we need to have async process to touch server side action, so after we call it we use a callback to receive response and if successfull send it back to aura:component 
In OpenCaseController.js we have 
```javascript
({
	doInit : function(component, event, helper) {
	
        var action = component.get("c.getCaseDB");
        console.log('calling out');
        action.setCallback(this, function(response){
            var state = response.getState();
            if(component.isValid() && state == "SUCCESS"){
                console.log('success');
                component.set("v.cases", response.getReturnValue());
               }
        });
        $A.enqueueAction(action);
	}
})
```

### Server Side Action Controller
It is OpenCasesApexController class
```java
public with sharing class OpenCasesApexController {
@AuraEnabled
 public static List<Case> getCasesDB() {
   return [SOQL Query goes here];
  }
}
```
* It should be static and uses public or general modifier
* Then add controller to `<aura:component  controller="OpenCasesApexController">`

#### With Sharing
* This key word allows organization enforce security policies for record access concerns

* Lightening controller from [here](https://trailhead.salesforce.com/modules/lex_dev_lc_basics/units/lex_dev_lc_basics_controllers)
#### UI Components
* By adding `...force.com/auradocs/reference.app` to access documentation
* At any component if you see this `change={"!c.oninchange"}` it means by chaning it call a funciton in controller 

## Lightning Flow
* Create program with UML. There are two automation tools: Process Builder and Cloud Flow Designer. Process builder you build processes and with Cloud Flow Design you build flows when users inputs

* You can create Schedule as `Under Scheduled Actions> Set Schedule > Add Action > Create a Record> Task > Save`
#### Process Builder
* [here](https://trailhead.salesforce.com/modules/business_process_automation/units/process_builder)
* `Setup > Process Builder > save > Add Object (add trigger) > save >  Add Criteria > save` 
* Process Builder is workflow tool and tells if some conditions meet like updating some fields, automatically update some others. For example if a company you work for change its location and you want a way to automatically update addresses of all people
* You first create a process and then select the object on which the process runs. You also make sure the process kicks off whenever a record is edited or created

#### Cloud Work Flow Designer
* It is shortcut to create for example an account from home page from [here](https://trailhead.salesforce.com/modules/business_process_automation/units/flow). You define screen and then create records to make flow
* first enable it from `setup> Automation >Enable Lightning runtime`
* To create flow `setup>flow>new> drag screen into canvas> define names, fields with labels` Then drag `record create> name and assign variable accountid` then connect then save then active it.
* To add it to home page or make it Look Like Lightning `setup>builder>Lightning App Builde> new>Home Page>Clone Salesforce Default Page>drag flow to place`
* You can add flow into process designs. Some advantages is for example in process builder, you can’t grab the ID of the created record and use it elsewhere but you can do so in a flow.
* Workflow only can Create, Update, Email and outbound messages but processes allow us to do a lot more, such as creating a record, launching a flow, posting to Chatter, quick actions (global actions), submit for approval and updating records.