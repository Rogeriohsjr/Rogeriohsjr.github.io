---
layout: post
---

How to get SObject by a String(Full Object API Name)

Lately at my work, we had an issue where we were using Type.forName to get the ObjectType and that was returning the object which was inside of the managed package, and we want to return an object that was outside of the name package, even though we were calling the object without a namespace.

These code below shows how long it takes to get a normal standard object using 3 ways to get the object.

- These codes were running in the Apex Developer Console >> Execute Anonymous Windows.

- Each group of code was executed by itself, which means, I didn't execute all the groups at once, I executed one group once, then again 3 times.

{% highlight apex %}


// Using Type.ForName
// 1000xTimes = 480, 460, 481
// 10xTimes = 11, 10, 11
String obj1 = 'Account';
Long time1 = DateTime.now().getTime();
for (Integer i = 0; i < 10; i++) {
    SObjectType r = ((SObject)Type.forName('Schema',obj1).newInstance()).getSObjectType();
    DescribeSObjectResult d = r.getDescribe();
}
Long time2 = DateTime.now().getTime();
System.debug(time2-time1);


// Using GlobalDescribe
// 1000xTimes = 710, 530, 562
// 10xTimes = 344, 384, 334
String obj1 = 'Account';
Long time1 = DateTime.now().getTime();
Map<String,Schema.SObjectType> gd = Schema.getGlobalDescribe(); 
for (Integer i = 0; i < 10; i++) {
    Schema.SObjectType sobjType = gd.get(obj1);
    Schema.DescribeSObjectResult describeResult = sobjType.getDescribe();
}
Long time2 = DateTime.now().getTime();
System.debug(time2-time1); 


// Using JSON.Serialize
// 1000xTimes = 677, 542, 554
// 10xTimes = 12, 11, 11
String obj1 = 'Account';
Long time1 = DateTime.now().getTime();
for (Integer i = 0; i < 10; i++) {
    Schema.SObjectType currentSObjectType = ((SObject) JSON.deserialize('{"attributes":{"type":"' + obj1 + '"}}', SObject.class)).getSObjectType();
    Schema.DescribeSObjectResult describeResult = currentSObjectType.getDescribe();
}
Long time2 = DateTime.now().getTime();
System.debug(time2-time1); 


{% endhighlight %}

My conclusion:
- I will be trying out the JSON one, the JSON.Serialize supports my use case and the performance is better than Schema.getGlobalDescribe();.


What I have tried with Type.forName

1. I tried to use the Namespace, passing Type.forName('', 'MyCustomObject__c');, it didn't work, it still returns my namespace object which also is called 'MyCustomObject__c', but it has a namespace.

2. I tried to use a namespace called 'Schema', like Type.forName('Schema', 'MyCustomObject__c'), it didn't work either, it got my object with namespace.


Source:
- sfdcfox: https://salesforce.stackexchange.com/a/219010/49417
- Adam: https://salesforce.stackexchange.com/a/346901/49417