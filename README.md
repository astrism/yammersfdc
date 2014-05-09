#Yammer anywhere in Salesforce.com
##Why?
If your company has yammer and salesforce.com, this is relevant to you. Yammer has developed a very easy to integrate javascript sdk which allows you easily integrate any Yammer feed with very few lines of code. This is ideal for any company looking to make their internal software more tightly integrated. **internal user experience more tightly integrated**

##When to use embed?
You should use embed whenver you have multiple applications across your technology stack. At Nimbly we like to use 'the right tool for the job' principle. Imagine you have Yammer for collaboration, Zendesk for service requests, Salesforce for CRM, Xero for accounting, and Papyrs for knowledge management. Yammer Embed inside each of these tools is the perfect use case. Even if your companies infrastructure has 2 of these technologies it makes sense to use Embed.

Anything that is *easy* to setup is usually sacrifycing some flexibility and power, but Yammer does a good job exposing a lot of functionality through its javscript sdk. The embed feed is easy enough to configure. Below is a guide to step you through the process.
	[graphic of tech stack]

##Elephant in the room, Chatter
Isn't Yammer just like Chatter? Why do I need need this when I have chatter? Why do I need Chatter if I have Yammer?
We aren't trying to answer all these questions, but there are some differences between the two architectures which are important to point out. 

Yammer is built around the idea of Users, Groups, and Feeds. A user belongs to many groups which have their own individual feeds. Everyone belongs to the 'All Company' group by default. Our Yammer has Sales, Marketing, Accounting, and Engineering groups.

Salesforce (w/ Chatter) is composed of Users, Objects (fancy tables), Records, and Feeds. Salesforce specializes in CRM data and with chatter everything is related to a set of data or specific record. There is another layer of granularity with Chatter because it is designed to fight in snuggly within the *context* of CRM data.

Yammer is a great tool for promoting social behovior on your general business and specific departments/divisions of any business. The power of Yammer is in its simplicity. It does one thing, enable companies to be social about their business.

The measure of an apps longevity nowadays relys on the applications' ability to be easily integrated. How well does it play with others (i.e. is there an API)!? Yammer does a great job enabling itself to be 'easily' integrated.

##This is not a fully fledged application
The sample provided is only a demo of the functionality included in Yammer's Embe API. We've gotten a lot of interest in building a more robust appplication, so this is our starting point. As of now it is just duplicating the basic functionality of Yammer in any platform you choose (ours is Salesforce). There are some good

Here's the end result :
	+ theme of orgs. Bill Nye, Science Guy. Has customers and partners. Hayden Planetarium is his customer. He wants to check with his accountants to ensure that Neil Tyson received the invoice. He messages Ken G., accountanting department, "Hey Ken, when did we send out the invoice to Neil?"
	+ Ken answers, " Checking now.."
		+ "He downloaded the invoice in March"
	+ Bill, "Thanks bud."
	[screenshot]


##Guide for setup
Technology opinions are everywhere! I like to hear peoples tool recommendations, so there'll be some speckled throughout the guide. We'll also be jumping back and forth between Yammer and Salesforce.com platforms so I'll try to be explicit.

###Yammer signup
+ Assuming you have an existing yammer org
	+ The first thing you need to start is a Yammer domain. We are assuming you already have this setup because Yammer checks your email address. "Only people with a verified company email address can join your company network." - Yammer
	+ You can sign-up for Yamme here https://www.yammer.com/?return_home=true
+ create group, Sales (any group will be fine, just change 'Sales' to your group name)
+ links for yammer api , js sdk embed
	+ Here are the links to the documentation we are referencing throughout this guide.
+ Create app
	+ CORS is important. Here is some good documentation on why we need to reference them here [http://www.html5rocks.com/en/tutorials/cors/]. TLDR; when using client side javascript to make callouts to external sources (XHR to Yammer API), the requested server has to be able to accept Cross-Origin requests. We won't go into more detail as it is out of the scope of this article.
	+ [screen shot of app config'd]

Now we're ready to create a place for our Yammer application to live. On to Salesforce.com

###Salesforce setup
Create a new salesforce instance (DE)

+ https://developer.salesforce.com/
+ sign up -> fill out form -> sign me up


####Create visualforce page

Quick start VF page doc http://www.salesforce.com/us/developer/docs/pages/Content/pages_quick_start_hello_world.htm
<br/>
VF doc http://www.salesforce.com/us/developer/docs/pages/index.htm

+ Assuming you are doing this in your new Developer Edition instance of Salesforce
+ we typically use Mavens Mate and Sublime Text
	+ cmd+shift+p -> 'New Visualforce Page'
	+ Name it 'accountsyammer'
+ From Salesforce UI
	+ Setup -> Create -> visualforce

Visualforce code:

		<apex:page showHeader="true" sidebar="true" standardController="Account" >
			Hello Visualforce
		</apex:page>

Preview the page in sfdc. http://SALESFORCE_DOMAIN.com/apex/accountyammer

####Yammer widget in Visualforce page
(ref: https://developer.yammer.com/connect/)

We have a Visualforce page setup, let's drop in our Yammer Embed widget. In your visualforce page, include the Yammer js sdk. The data-app-id is the public token genereated when you create your Yammer App. Find the Id through Yammer.com, Created Apps -> app_name -> Client Id.

		<script type="text/javascript" data-app-id="*****" src="https://assets.yammer.com/assets/platform_js_sdk.js"></script>

The first thing to do after we have the widget is to authenticate with the Yammer servers.
		
		<script> 
			yam.connect.loginButton('#yammer-login', function (resp) { 
				if (resp.authResponse) { 
					console.log('pass');
				}
				else {
					console.log('fail');
				}
			}); 
		</script>
		<span id="yammer-login"></span>

Refresh your page. You should receive a javascript exception in your console (I'm testing with Chrome and Mac, so cmd+alt+j toggles the Javascript Console). The errro should say that 'Allow-Access-Control-Origin'. This is the CORS I was mentioning above. To fix, define the salesforce domain in Javascript Origins portion of your Yammer App. (Ex: https://c.na15.visual.force.com/). Refresh and the error should be gone.

Now add the html for the embed widget and the javascript to render your feed.
		
		<script> 
			yam.connect.loginButton('#yammer-login', function (resp) { 
				if (resp.authResponse) { 
					console.log('pass');
					yam.connect.embedFeed({
						container: '#embedded-feed',
						network: 'gonimbly.com',
						feedType: 'group',
						feedId: 'all',
						config: {
							use_sso: true,
							header: false,
							footer: false
						}
					});
				}
				else {
					console.log('fail');
				}
			}); 
		</script>
		<div id="embedded-feed" style="height:400px;width:500px;"></div>
		...

Your Yammer feed should now load automatically, after you've logged in once through Yammer.

The Embed component works best with some limited context. It would be nice to see the feed while looking at an Account record in Salesforce.com. You can accomplish this with Visualforce pre-built components, specifically the 'detail' component. (ref: https://www.salesforce.com/us/developer/docs/pages/Content/pages_compref_detail.htm)
		
		<apex:detail subject="{!account.Id}" />

As of now we have a standalone page that just shows your yammer feed. We need to override the account object's 'view' to give the application a record's context with Yammer feed. In Salesforce.com:

+ Navigate to Setup -> Customize -> Accounts -> Buttons, Links, and Actions
+ Edit the action labeled 'View' 
+ Override with your Visualforce page, 'accountsyammer'. Note: it is available in the drop down because we defined our Visualforce page to have the 'Account' as the standardController attribute.
+ If you don't have a record in the Account object, you can create one now. Navigate to the Accounts tab and click New.
+ Otherwise navigate to an Account record and you should see your Yammer feed 'embeded' in your Account detail page.
+ add collapsible
	+ //TBD

Your demo app should be complete. Yammer's API has a lot more to it then just the Embed widget. We think this is great start for anyone interested in breaking into Yammer development on the Salesforce platform. Happy coding.

###Salesforce deploy to production
		//TBD


##Gotchas of APIs
###Salesforce.com
		//TBD
###Yammer
		//TBD

##Inexpertise
We are by no means experts on the Yammer platform, but the API makes it very easy to understand. If there are any mistakes in this article, please be kind and point the out. We'll get them fixed and everyone will learn something.