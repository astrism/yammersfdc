#Yammer anywhere in Salesforce.com
##Why? 

If your company has Yammer and Salesforce.com, this is relevant to you. Yammer has developed a very easy to integrate javascript sdk which allows you to easily integrate any Yammer feed to Salesforce with very few lines of code. This is ideal for any company looking to make their internal software more tightly integrated.

The measure of an application's longevity relies on its ability to be easily integrated. How well does it play with others (i.e. is there an API)!? Yammer does a great job enabling itself to be 'easily' integrated.

##Elephant in the room, Chatter
Isn't Yammer just like Chatter? Why do I need Chatter if I have Yammer? We aren't trying to answer all these questions, but there are some differences between the two architectures which are important to point out. 

Yammer is built around the idea of Users, Groups, and Feeds. A user belongs to many groups, which have their own individual feeds. Everyone belongs to the 'All Company' group by default. Our Yammer has Sales, Marketing, Accounting, and Engineering groups.

Salesforce (w/ Chatter) is composed of Users, Objects (fancy tables), Records, and Feeds. Salesforce specializes in CRM data and with chatter everything is related to a set of data or specific records. There is another layer of granularity with Chatter because it is designed within the *context* of CRM data. This is the main difference between the two.

Yammer is a great tool for promoting social interactions about your general business and specific departments/divisions of any business. The power of Yammer is in its simplicity. It does one thing best, enable companies to be social about their business.

##The Story
+ Bill Nye, Science Guy, has customers and partners. From Salesforce, Bill wants to check with his accountants (who use only Yammer) to ensure that Neil Tyson received the invoice. He messages Ken Gene, accounting department, "Hey Ken, when did we send out the invoice to Neil?"
+ Ken answers from Yammer, " Checking now.."
	+ "He downloaded the invoice in March"

####In Salesforce:

![yammer conversation](img/yammer_story_account_sfdc.png?raw=true)

##Guide for setup
Technology opinions are everywhere! I like to hear peoples' tool recommendations, so there'll be some speckled throughout the guide. We'll also be jumping back and forth between Yammer and Salesforce.com platforms, so I'll try to be explicit.

###Yammer sign-up
The first thing you need to [start is a Yammer domain](https://www.yammer.com/?return_home=true). We'll assume you already have this setup since you are still reading, so let's get started.
+ Log into your Yammer instance
+ Create a group - we'll use 'Accounting' as an example (we'll reference this group later)

Create a Yammer App
+ [Guide](http://developer.yammer.com/introduction/#gs-registerapp) to getting started
+ Navigate to ['Created Apps'](https://www.yammer.com/client_applications)
+ 'Register New App'
	+ App Name: salesforceyammin
	+ Organization: example llc
	+ Support email: help@examplellc.com
	+ Website: http://www.examplellc.com
+ Done!

We will need the Client ID when creating the html/scripts of our app in Salesforce later, so just keep it handy. In order to use the Yammer Embed API we'll have to specify our 'Javascript Origins' in the 'Basic Info' section of the app. We don't know the urls yet, so we'll be coming back later.
<br>
Javascript Origins is for CORS. [CORS](http://www.html5rocks.com/en/tutorials/cors/) is an important part of the javascript sdk. TLDR; when using client side javascript to make callouts to external sources (XHR to Yammer API), the requested server has to be able to accept Cross-Origin requests. We won't go into more detail as it is out of the scope of this article.

Now we're ready to create a place for our Yammer application to live. On to Salesforce.com

###Salesforce setup
Create a new [salesforce instance (DE)](https://developer.salesforce.com/)
+ sign up -> fill out form -> sign me up

Or

+ use your existing Salesforce.com sandbox


####Salesforce setup

[Quick start VF page doc](http://www.salesforce.com/us/developer/docs/pages/Content/pages_quick_start_hello_world.htm)
<br/>
[Full VF doc](http://www.salesforce.com/us/developer/docs/pages/index.htm)

#####Create Visualforce page
Once logged into your new Salesforce.com org
+ From the Salesforce UI
	+ Setup -> Create -> Visualforce
+ Our prefered Salesforce IDE is [Mavens Mate for Sublime Text](http://mavensmate.com/)
	+ cmd+shift+p -> 'New Visualforce Page'
	+ Name it 'accountyammer'

Visualforce code:

		<apex:page showHeader="true" sidebar="true" standardController="Account" >
			Hello Visualforce
		</apex:page>

Preview the page in sfdc. https://SALESFORCE_DOMAIN.com/apex/accountyammer

####Yammer widget in Visualforce page
[Embed API ref](https://developer.yammer.com/connect/)

We have a Visualforce page setup, let's drop in our Yammer Embed widget. In your visualforce page, include the Yammer js sdk. The data-app-id is the public token generated when you create your Yammer App. Find the Id through Yammer.com, Created Apps -> app_name -> Client Id.

		<script type="text/javascript" data-app-id="YOUR_CLIENT_ID" src="https://assets.yammer.com/assets/platform_js_sdk.js"></script>

The first thing to do after we have the widget is to authenticate with the Yammer servers. [Yammer Client-side OAuth 2](http://developer.yammer.com/authentication/#a-button)
		
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

Refresh your page in your browser. You should receive a javascript exception in your console (I'm testing with Chrome and Mac, so cmd+alt+j toggles the Javascript Console). The error should say that 'Allow-Access-Control-Origin'. This is the CORS I was mentioning above. To fix, define the salesforce domain in Javascript Origins portion of your Yammer App. (Ex: https://c.na15.visual.force.com/). Refresh and the error should be gone.
+ Created Apps -> My Apps -> sfdcyammin -> Basic Info -> Javascript Origins
+ https://c.na15.visual.force.com/

Add the html for the embed widget and the javascript to render your feed.
		
		<script> 
			yam.connect.loginButton('#yammer-login', function (resp) { 
				if (resp.authResponse) { 
					console.log('pass');
					yam.connect.embedFeed({
						container: '#embedded-feed',
						network: 'examplellc.com',
						feedType: 'group',
						feedId: 'Accounting',
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

The Embed component works best with some limited context. It would be nice to see the feed while looking at an Account record in Salesforce.com. You can accomplish this with Visualforce pre-built components, specifically the ['detail' component](ref: https://www.salesforce.com/us/developer/docs/pages/Content/pages_compref_detail.htm). 
		
		<apex:detail subject="{!account.Id}" />

As of now we have a standalone page that just shows your yammer feed. We need to override the account object's 'view' to give the application a record's context with Yammer feed. In Salesforce.com:

+ Navigate to Setup -> Customize -> Accounts -> Buttons, Links, and Actions
+ Edit the action labeled 'View' 
+ Override with your Visualforce page, 'accountyammer'. Note: it is available in the drop down because we defined our Visualforce page to have the 'Account' as the standardController attribute.
+ If you don't have a record in the Account object, you can create one now. Navigate to the Accounts tab and click New.
+ Otherwise navigate to an Account record and you should see your Yammer feed 'embedded' in your Account detail page.

This works fine, but it would be nice if we could hide the feed when we want to only see the account details. Wrap the 'embedded-feed' tag in a div and add an element we can click.

		<div id="feed">Yammer Feed</div>
		<div id="feedHolder">
			<div id="embedded-feed" style="width: 100%; height: 300px;"></div>
		</div>

We can make this super easy by including jquery to toggle the display of the feed. Jquery is a bit overkill, but is fine for this demo.

		<script type="text/javascript" src="https://code.jquery.com/jquery-2.1.1.min.js"></script>
		<script> 
			//yammer sdk uses jquery as a dependency, so I want to eliminate any possible conflicts
			var $j = jQuery.noConflict();
			//necessary for adding events after dom elements loaded
			$j( function() {
				$j("#feed").click( function() { $j("#feedHolder").toggle() });
			});
			...
		</script>


Your demo app is now complete. Yammer's API has a lot more to it than just the Embed widget, so be sure to check out all the documentation.


##Learns of the APIs
###Context is important
Having a comment on a specific record/data in any system is a baseline for any internal system claiming to be 'social'. The reasons are obvious, because an app without the proper context is guaranteed to display irrelevant information. And irrelevant information, with some useful pieces speckled throughout, is actually worse for users than no information at all.

The Yammer API is great to get up and running quickly, but it still only resolves a simple use case of social interactions in your CRM system. It does NOT make it easy to enable context aware social interactions in your systems. 

Context awareness (with Yammer in Salesforce) requires a middle-tier database to hold/manage relationships between business data (accounts/contacts/billings) and social data. I'd be interested in seeing a simple nosql application to prove out this architecture.

###Advice on the "middle-tier"
In this case it would be a great time to take advantage of Salesforce.com's Canvas'ing capabilities. Which allow you to host a Heroku application authenticated within Salesforce.com user interface. Use your technology of choice (Java, Ruby, Node.js) to integrate Salesforce and Yammer.

##You're done!
We think this is a great start for anyone interested in beginning Yammer development on the Salesforce platform. Let us know if you have any questions or comments.

Happy coding!