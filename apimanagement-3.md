# API Management - Hands-on Lab Script - part 3

- [Part 1 - Create an API Management instance](apimanagement-1.md) 
- [Part 2 - Developer Portal and Product Management](apimanagement-2.md) 
- Part 3 - Adding API's (You are here)
- [Part 4 - Caching and Policy Expressions](apimanagement-4.md)
- [Part 5 - Versioning and Revisions](apimanagement-5.md)
- [Part 6 - Analytics and Monitoring](apimanagement-6.md)
- [Part 7 - Security](apimanagement-7.md)
- [Part 8 - DevOps](apimanagement-8.md)

### API Management

An API represents a set of operations that can be invoked. New APIs are defined and then the desired operations are added. An API is added to a product and can be published; it may then be subscribed to and used by developers.

#### APIs

On the left menu, open the [APIs] blade. You will see all APIs, the possibility to add new ones but also to customize existing ones

![](Images/APIMListAPIs.png)

![](Images/APIMAddAPIs.png)

#### Add API from scratch

Instead of coding an API, for this lab you will use the existing *Star Wars API* <https://swapi.dev>. 

- Click on [Add API]
  - Select [Add Blank API]
  - Select the [Full] option at the top of the dialog
  - Enter Display name, name and description
  - Enter back end Web Service - this is <https://swapi.dev/api>
  - Set API URL suffice to "sw"
  - Assign Products - Starter and Unlimited
  - Create

![](Images/APIMAddBlankAPI.png)

Once created, select [Start Wars API]

![](Images/APIMAddStarWars.png)

Lets declare two operations
  - **GetPeople** GET /people/  ... use lowercase
  - **GetPeopleById** GET /people/{id}/  ... use lowercase

![](Images/APIMAddSWGetPeople.png)

![](Images/APIMAddSWGetPeopleById.png)

![](Images/APIMAddSWOperations.png)

To get this to work from the Developer portal - we must overcome the CORS.  Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served. This involves setting up a Policy - a topic that we explain in more depth in the next section.

Select the Star Wars API | All Operations ... and in the Inbound processing select [Add policy]

![](Images/APIMSWCORS1.png)

Select [CORS]

![](Images/APIMCORS2.png)

Define the Policy as in the screenshot - this config is suitable for demo purposes only and ensures we are not hampered by CORS

![](Images/APIMCORS3.png)

Once the policy had been Saved, we can inspect it in [Code View]

![](Images/APIMCORS4.png)

Switch now to the Developer Portal
  - Sign in as a developer with a subscription
  - Select [Start Wars API]

![](Images/APIMSWTryIt1.png)

- Try the "GetPeople" operation
- Try the "GetPeopleById" operation ... with id = 2

![](Images/APIMSWTryIt2.png)

Examine Response and more detailed Trace information
  - Response 200 successful
  - Information about C-3PO in the Response body payload.

![](Images/APIMSWTryIt3.png)

#### Import API using swagger

Instead of importing operations one by one, you can also import a full API. The [OpenAPI specification](https://www.openapis.org/) (aka [Swagger](https://swagger.io)) is a definition format to describe RESTful APIs. The specification creates a RESTful interface for easily developing and consuming an API by effectively mapping all the resources and operations associated with it.

As a demo we will use an API that offers a simple calculator service : [Calc API](http://calcapi.cloudapp.net/)

![](Images/APIMCalcAPI.png)

Go to APIs blade and select [Add OpenAPI Specification]
- Specify swagger URL <http://calcapi.cloudapp.net/calcapi.json>
  - Some of the fields will be populated from the swagger definition
  - Use "calc" in URL for API suffix
  - Add products Starter and Unlimited

![](Images/APIMAddCalcAPI1.png)

![](Images/APIMAddCalcAPI2.png)

- As above set a CORS policy to allow access from the Developer portal

- Look at Calculator API in the Developer portal
  - Try the Add Two Integers operation
- Look at Response / Trace

![](Images/APIMCalcTryIt1.png)

![](Images/APIMCalcTryIt2.png)

We can inspect / edit the Open API definition by selecting the `Edit` icon from the Frontend block:

![](Images/APIMCalcSwagger.png)

![](Images/APIMCalcSwagger2.png)

Let's use another example - the [Colors API](https://markcolorapi.azurewebsites.net/swagger/)

![](Images/APIMColorAPI.png)

- Create a new API with OpenAPI specification and import swagger from > <https://markcolorapi.azurewebsites.net/swagger/v1/swagger.json>
  - Use "color" in URL for API

![](Images/APIMAddColorAPI1.png)

![](Images/APIMAddColorAPI2.png)

Remember to set a CORS policy.

The swagger file did not contain the name of the host so you need to update it manually

- Select [Settings]
- Amend Web Service URL to <https://markcolorapi.azurewebsites.net>

![](Images/APIMAddColorAPI3.png)

We can test this from the [Test] tab

![](Images/APIMAddColorAPI.png)

Switch to the Developer portal and look at Color API
  - Try the RandomColor operation
- Look at Response / Trace ... see the random color returned

![](Images/APIMColorTryIt1.png)

![](Images/APIMColorTryIt2.png)

#### Rate limit

Use [Colors website](https://markcolorweb.azurewebsites.net) which displays 500 lights.  Each light will at random intervals make a call to the RandomColor API - and then display the color returned.

![](Images/APIMColorWeb.png)

Via the menu - there is a configuration page to specify the API endpoint

![](Images/APIMColorWebConfig.png)

Open Developer portal, go in the Profile page and get API keys for Starter and Unlimited products
- Open Notepad - make note of URLs
  - <https://YOURAPIM.azure-api.net/color/api/RandomColor?key=> *Starter-Key*
  - <https://YOURAPIM.azure-api.net/color/api/RandomColor?key=> *Unlimited-Key*

![](Images/APIMColorWebKeys.png)

- To see that Unlimited product has no rate limits
  - Configure Color Website to use Unlimited URL
  - Select [Start]
  - Notice there is no Rate Limit - every light is randomly updated

![](Images/APIMColorWebUnlimited.png)

- To see that Starter product is limited to 5 calls per minute
  - Configure Color Website to use Starter URL
  - Select [Start]
  - Notice that only 5 lights get colored
- Try URL with the Starter key, directly in the web browser address bar
  - Notice the error status / message returned
  - Example: *{ "statusCode": 429, "message": "Rate limit is exceeded. Try again in 54 seconds." }*

![](Images/APIMColorWebStarter.png)

---
[Home](README.md) | [Prev](apimanagement-2.md) | [Next](apimanagement-4.md)