# API Management - Hands-on Lab Script

[Home](README.md)

## Additional Topics - API Proxy to Serverless

Azure Serverless (Functions and Logic Apps) can be configured to benefit from the advantages of API Management.

### Azure Functions

- Create a simple function that is Triggered by an HTTP Request

Example:

![](Images/APIMFunctionExample.png)

```c#
    //string[] strColors = { "blue", "lightblue", "darkblue" };
    string[] strColors = { "green", "lightgreen", "darkgreen" };

    Random r = new Random();
    int rInt = r.Next(strColors.Length);

    return  (ActionResult)new OkObjectResult(strColors[rInt]);
```

Lets add the function to API Management.   In the API blade select [+Add API] and the [Function App] tile

![](Images/APIMFunctionAddAPI.png)

- Select the [Browse] button to get a list of Functions in the subscription

![](Images/APIMFunctionAddBrowse.png)

- Select the Function App and then the Function

![](Images/APIMFunctionSelect.png)

![](Images/APIMFunctionSelect2.png)

- Amend the Names / Descriptions, URL suffix and select the Products

![](Images/APIMFunctionCreate.png)

- As previously add CORS policy

- Validate the function works - either from the Azure management portal or the developer portal

![](Images/APIMFunctionTest1.png)

![](Images/APIMFunctionTest2.png)

### Azure Logic Apps

- Create a simple logic app that is Triggered by an HTTP Request

Example:

![](Images/APIMLogicAppExample1.png)

![](Images/APIMLogicAppExample2.png)

Use the following sample message to generate the schema of the Request body payload.  By specifying the schema, the individual fields (in this case `msg`) can be extracted and referred to in the subsequent logic

```json
{
  "msg": "text"
}
```

Lets add the function to API Managament. In the API blade select [+Add API] and the [Logic App] tile

![](Images/APIMLogicAppAddAPI.png)

- Select the [Browse] button to get a list of Logic Apps in the subscription

![](Images/APIMLogicAppAddBrowse.png)

- Select the Logic App

![](Images/APILogicAppSelect%20.png)

- Amend the Names / Descriptions, URL suffix  and select the Products

![](Images/APIMLogicAppCreate.png)

 As previously add CORS policy

- Validate the Logic App works - either from the Azure management portal or the developer poral

![](Images/APIMLogicAppTest1.png)

![](Images/APIMLogicAppTest2.png)

- Check the Logic App audit

![](Images/APIMLogicAppTest3.png)

- Check the email was sent

![](Images/APIMLogicAppTest4.png)

---
[Home](README.md)
