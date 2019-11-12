---
layout: post
categories: salesforce, sfdx
---
Some of the salesforce dx commands that I use often.

{% highlight shell %}
// Setup an org and give a alias for it.
sfdx force:auth:web:login -a Dev2

//This creates a org and install a package
vTries=1159 &&
vVersion=v358 &&
vPackageID=XXXXXXX &&
sfdx force:org:create -f config/test-enterprise.json -a 'Enterprise-'$vVersion -v DevHub &&
sfdx force:user:create --targetusername 'Enterprise-'$vVersion --setalias 'qa-user-'$vVersion --definitionfile config/user-admin.json Username=$vTries'qafs-'$vVersion'@xxtest.ca' &&
sfdx force:user:password:generate -u 'qa-user-'$vVersion &&
sfdx force:package:install --targetusername 'qa-user-'$vVersion --package $vPackageID --wait 3 --noprompt &&

sfdx force:org:open --targetusername 'qa-user-'$vVersion --path lightning &&
sfdx force:org:display -u 'qa-user-'$vVersion
{% endhighlight %}

