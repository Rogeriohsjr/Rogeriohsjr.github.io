---
layout: post
---
When we try to do an operation in salesforce we can get this message:

Insert failed. First exception on row 0; first error: STORAGE_LIMIT_EXCEEDED, storage limit exceeded: [] 
An unexpected error has occurred. Your development organization has been notified.

This happens because we have reach the Salesforce limit for Storage. You can check it on :

Setup >> Storage Usage
![storage usage](/assets/images/posts/storage-usage.png)

My Case I had to clean some objects, so I run this script.

{% highlight apex %}
List<attachment> ListObjects =
    [SELECT id from attachment];

System.debug(
    ListObjects.size()
);

Database.delete(ListObjects, false);

ListObjects =
    [SELECT id from attachment];

System.debug(
    ListObjects.size()
);
    
    
List<ContentDocument> ListOfDocument =
    [SELECT id from ContentDocument];

System.debug(
    ListOfDocument.size()
);

Database.delete(ListOfDocument, false);

ListOfDocument =
    [SELECT id from ContentDocument];

System.debug(
    ListOfDocument.size()
);
{% endhighlight %}

Clean the Bin as well

Home >> Recycle Bin >> Empty Your organization's recycle bin
