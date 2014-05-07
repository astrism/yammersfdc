#Yammer anywhere in Salesforce.com
##Why?
If your company has yammer and salesforce.com, this is relevant to you. Yammer has developed a very easy to integrate javascript sdk which allows you easily integrate any Yammer feed with very few lines of code. This is ideal for any company looking to make their internal software more tightly integrated. **internal user experience more tightly integrated**

##When to use embed?
You should use embed whenver you have multiple applications across your technology stack. At Nimbly we like to use 'the right tool for the job' principle. Imagine you have Yammer for collaboration, Zendesk for service requests, Salesforce for CRM, Xero for accounting, and Papyrs for knowledge management. Yammer Embed inside each of these tools is the perfect use case. Even if your companies infrastructure has 2 of these technologies it makes sense to use Embed.
Anything that is *easy* to setup is usually sacrifycing some flexibility and power, but Yammer does a good job exposing a lot of functionality through its javscript sdk. TThe embed feed is easy enough to configure. Below is a guide to step you through the process.
	[graphic of tech stack]

##Elephant in the room, Chatter
Isn't Yammer just like Chatter? WHy do I need need this when I have chatter? Why do I need Chatter if I have Yammer?
We aren't trying to answer all these questions, but there are some differences between the two architectures that are important to point out. 
Yammer is built around the idea of Users, Groups, and Feeds. A user belongs to many groups which have their own individual feeds. Everyone belongs to the 'All Company' group by default. Our Yammer has Sales, Marketing, Accounting, and Engineering groups.
Salesforce (w/ Chatter) is composed of Users, Objects (fancy tables), Records, and Feeds. Salesforce specializes in CRM data and with chatter everything is related to a set of data or specific record. There is another layer of granularity with Chatter because it is designed to fight in snuggly within the *context* of CRM data.
Yammer is a great tool for promoting social behovior on your general business and specific departments/divisions of any business. The power of Yammer is in its simplicity. It does one thing, enable companies to be social about their business.
The measure of an apps longevity nowadays relys on the applications' ability to be easily integrated. How well does it play with others (i.e. is there an API)!? Yammer does a great job enabling itself to be 'easily' integrated.

##This is not a fully fledged application
The sample provided is only a demo of the functionality included in Yammer's Embe API. We've gotten a lot of interest in building a more robust appplication, so this is our starting point. As of now it is just duplicating the basic functionality of Yammer in any platform you choose (ours is Salesforce). There are some good

##Guide for setup
Technology opinions are.. everywhere! I like to hear peoples tool recommendations, so there'll be some speckled throughout the guide. We'll also be jumping back and forth between Yammer and Salesforce.com platforms so I'll try to be explicit.

###Yammer signup
+ Assuming you have an existing yammer org
The first thing you need to start is a Yammer domain. We are assuming you already have this setup because Yammer checks your email address. "Only people with a verified company email address can join your company network." - Yammer
You can sign-up for Yamme here https://www.yammer.com/?return_home=true
+ create group, Sales (any group will be fine, just change 'Sales' to your group name)
+ links for yammer api , js sdk embed
Here are the links to the documentation we are referencing throughout this guide.
+ create app
CORS is important. Here is some good documentation on why we need to reference them here [http://www.html5rocks.com/en/tutorials/cors/]. TLDR; when using client side javascript to make callouts to external sources (XHR to Yammer API), the requested server has to be able to accept Cross-Origin requests. We won't go into more detail as it is out of the scope of this article.
[screen shot of app config'd]
Now we're ready to create a place for our Yammer application to live. On to Salesforce.com

###Salesforce setup
+ create a new salesforce instance (DE)
+ links for vf api
+ create visualforce page (mavens and )
+ view page in sfdc
+ copy / paste yammer js sdk and embed sample
+ -snippet for embed sales feed
+ copy / paste public token
+ add vf detail component
+ add page override on account
+ add CORS in yammer javascript origins
+ add collapsible

###Salesforce deploy to production


##Gotchas of APIs
###Salesforce.com
###Yammer

##Inexpertise
We are by no means experts on the Yammer platform, but the API makes it very easy to understand. If there are any mistakes in this article, please be kind and point the out. We'll get them fixed and everyone will learn something.