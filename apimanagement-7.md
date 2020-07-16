# API Management - Hands-on Lab Script - part 7

- [Part 1 - Create an API Management instance](apimanagement-1.md)
- [Part 2 - Developer Portal and Product Management](apimanagement-2.md)
- [Part 3 - Adding API's](apimanagement-3.md)
- [Part 4 - Caching and Policy Expressions](apimanagement-4.md)
- [Part 5 - Versioning and Revisions](apimanagement-5.md)
- [Part 6 - Analytics and Monitoring](apimanagement-6.md)
- Part 7 - Security (You are here)
- [Part 8 - DevOps](apimanagement-8.md)

## Security

### JSON Web Tokens (JWT)

JSON Web Tokens are an open-industry standard method for representing claims securely between two parties. More info at <https://jwt.io/>

- Use the following sites
  - <https://jwt.io/> to create a JWT
    - Use a key that matches the value in the policy expression e.g. 123412341234123412341234
  - <https://www.unixtimestamp.com/index.php>
    - i.e. 01/01/2020  = 1577836800

![](Images/APIMJWT.png)

In this lab, we are going to see how to use the token with your APIs

#### JSON Web Tokens (JWT) - validate

Open the Calculator API 'Code View'
- Add the inbound policy to validate that JWT is valid
  - The example shows the use of variables in an expression - useful if a value is repeated

```xml
<!-- Inbound -->
<set-variable name="signingKey" value="123412341234123412341234" />
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized">
    <issuer-signing-keys>
        <key>@((string)context.Variables["signingKey"])</key>
    </issuer-signing-keys>
</validate-jwt>

```

- Invoke the API ... should get a [401 Unauthorized error]
- Invoke the API with a request header containing the security token (got above from <https://jwt.io/>) ... should get a 200 success
  - Name: Authorization
  - Value: bearer `JWT token`  (space between bearer and token)

No JWT:

![](Images/APIMRequestJWTnone.png)

Valid JWT in the header:

Note the bearer token in the Request payload.
Make sure your JWT token has an expiry date in the future.

![](Images/APIMRequestJWTvalid.png)

#### JSON Web Tokens (JWT) - check a claim exists

- Open the Calculator API 'Code View'
- Add the inbound policy to validate that JWT is valid and that the claim 'admin' exists
- Invoke the API - with Authorization header as above ... should get a 200 success
- Amend the policy with a claim name that doesn't exist e.g. 'adminx'
- Invoke the API - with Authorization header as above ... should get a 401 Unauthorized error

```xml
<!-- Inbound -->
        <set-variable name="signingKey" value="123412341234123412341234" />
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized">
            <issuer-signing-keys>
                <key>@((string)context.Variables["signingKey"])</key>
            </issuer-signing-keys>
            <required-claims>
                <claim name="admin" match="any">
                    <value>true</value>
                </claim>
            </required-claims>
        </validate-jwt>
```

Checking for admin claim:

![](Images/APIMRequestJWTclaimvalid.png)

Checking for adminx claim:

```xml
                <claim name="adminx" match="any">
```

![](Images/APIMRequestJWTclaiminvalid.png)

#### JSON Web Tokens (JWT) - extract claim and pass to backend

- Open the Calculator API 'Code View'
- Add the inbound policy to:
  - validate the JWT (as above)
  - extract the 'name' claim and set in header (below)
- Invoke the API - with Authorization header as above ... should get a 200 success
- Use the Trace feature to inspect what was passed to backend ... should see the use name from JWT

```xml
<!-- Inbound -->
<set-header exists-action="override" name="username">
    <value>@{
        Jwt jwt;
        context.Request.Headers.GetValueOrDefault("Authorization","scheme param")
                            .Split(' ').Last().TryParseJwt(out jwt);
        return jwt.Claims.GetValueOrDefault("name", "?");
        }
    </value>
</set-header>
```

![Claim in header](Images/APIMHeaderJWTClaimBackend.png)
![Claim in trace](Images/APIMTraceJWTClaimBackend.png)

### Managed Service Identity

In Azure, an Active Directory identity can be assigned to a managed resource such as an Azure Function, App Service or even an API Management instance. Once an identity is assigned, it has many capabilities to work with other resources that leverage Azure AD for authentication, much like a service principal.

#### Register API Management with Active Directory

![Register APIM](Images/APIMSecurityRegisterPrincipal.png)

#### Key Vault - Create Key Vault and add a secret

- Create a Key Vault instance
- Add a [secret](https://docs.microsoft.com/en-us/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) to the Key Vault instance
  - Name:`favoritePerson`
  - Value: `3`

![Create Key Vault](Images/APIMSecurityCreateKV.png)

#### Key Vault - Access policy and principal assignment

Create an access policy

![Create Key Vault](Images/APIMSecurityKV1.jpg)

Select the `Get` operation from the list of Secret permissions

![Create Key Vault](Images/APIMSecurityKV2.jpg)

Select the principal and search for the name of your API Management instance

![Create Key Vault](Images/APIMSecurityKV3.jpg)

![Create Key Vault](Images/APIMSecurityKV4.jpg)

Remember to click **Save**

![Create Key Vault](Images/APIMSecurityKV5.jpg)

#### API Management, Key Vault and Managed Service Identity

- Add a new operation to the Star Wars API
- Update the policies for the new operation

![New operation](Images/APIMSecurityAddOperation.png)

```xml
<!-- Inbound -->
<base />
<send-request mode="new" response-variable-name="secretResponse" timeout="20" ignore-error="false">
    <set-url>https://{your-apim-instance}.azure.net/secrets/favoritePerson/?api-version=7.0</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="https://vault.azure.net" />
</send-request>
<set-variable name="favoritePersonRequest" value="@{
    var secret = ((IResponse)context.Variables["secretResponse"]).Body.As<JObject>();
    return "/people/" + secret["value"].ToString() + "/";
}" />
<rewrite-uri template="@((string)context.Variables["favoritePersonRequest"])" />
```

#### Test the operation

- Test the operation (use the developer portal, Azure portal or tools like Postman and curl)
- Notice the request URL will be similar to: `https://{your-apim-instance}.azure-api.net/sw/favorite`

---
[Home](README.md) | [Prev](apimanagement-6.md) | [Next](apimanagement-8.md)
