AuthBridge is a server written in ASP.NET/C# using [WIF](http://msdn.microsoft.com/en-us/security/aa570351.aspx) and [DotNetOpenAuth](http://www.dotnetopenauth.net), that speaks WS-Federation and SAML tokens on one side and OpenID, OAuth, WS-Federation or any other protocol on the identity provider. 

![](http://puu.sh/GzU1)

**DISCLAIMER:** AuthBridge has not gone through any rigorous testing and you are responsible for hosting it yourself. If you are looking for a production ready service Windows Azure Active Directory would be a good choice. 

##Features

* Support Social Identity Providers: for Facebook (OAuth 2), Google (OpenID), Yahoo (OpenID), Twitter (OAuth 1.0a), Windows Live (OAuth 2). More can be added easily.
* Support for Enterprise Identity Providers like ADFS or IdentityServer using WS-Federation Protocol and SAML 1.1 or 2.0 Tokens. SAML 2.0 *protocol* could be added easily using the WIF SAML Extensions
* Support for Single Sign On
* Extensibility points to add more protocols
* Attribute transformation rule engine to normalize attributes coming from different identity providers

##Demo

AuthBridge has been deployed to AppHarbor together with a sample application

###https://authbridge-sample.apphb.com/

##FAQ

### How it compares with [DotNetOpenAuth](http://www.dotnetopenauth.net)?

[DotNetOpenAuth](http://www.dotnetopenauth.net) is a library that simplifies writing OAuth and OpenID clients and servers. AuthBridge builds on top of it and adds support for the WS-Federation protocol with SAML tokens which is generally used across Microsoft products like SharePoint, Windows Azure Active Directory, ASP.NET, etc. WS-Federation is also the only protocol supported by WIF (in the release version). AuthBridge is a library and a server at the same time that will run on its own host to act as a federation hub between your applications and identity providers.

### How it compares with [SocialAuth.NET](http://code.google.com/p/socialauth-net/)?

[SocialAuth.NET](http://code.google.com/p/socialauth-net/) is a library that focuses on social integration using OAuth. It is similar to DotNetOpenAuth but it was born as a port of its Java counterpart. They also have a separate STS project that implements WS-Federation to be able to integrate SocialAuth with SharePoint. In that sense, AuthBridge is similar to SocialAuth, except that AuthBridge currently supports more protocols on the identity provider side (so that you can integrate with enterprise identity providers like ADFS, SiteMinder, Ping, etc.)

### How it compares with [Windows Azure Active Directory](https://www.windowsazure.com/en-us/home/features/identity/) (previously known as Windows Azure Access Control Service)?

[Windows Azure Active Directory](https://www.windowsazure.com/en-us/home/features/identity/) (WAAD) is a cloud service run by Microsoft. It's similar to what AuthBridge in the ultimate purpose: it's a federation provider that sits between your apps and identity providers. However, WAAD is a cloud service and that means that it has been thoroughly tested in terms of performance, security and scalability; and it is also operated by Microsoft. AuthBridge has not gone through any of that rigorous testing and you are responsible for hosting it yourself. WAAD is being used extensively by millions of customers (Office365 and Windows Azure). If you are looking for a production ready service, then WAAD would be a good choice. 

Also, in the future WAAD will also provide a "Graph" API and syncronization capabilities with an on-premises AD. If you want to know more about it read ["What is Windows Azure Active Directory"](http://blog.auth10.com/2012/06/13/what-is-windows-azure-active-directory/) and ["Reimagining Active Directory for the Social Enterprise Part I"](http://blogs.msdn.com/b/windowsazure/archive/2012/05/23/reimagining-active-directory-for-the-social-enterprise-part-1.aspx) and ["Reimagining Active Directory for the Social Enterprise Part II"] (http://blogs.msdn.com/b/windowsazure/archive/2012/06/19/reimagining-active-directory-for-the-social-enterprise-part-2.aspx). 

### How it compares with [Auth10](http://auth10.com)?

[Auth10](http://auth10.com) is a set of tools that aim at dramatically simplifying the adoption of federated idenitity solutions. It provides automation for the most common scenarios (e.g. SharePoint, cloud, mobile, different platforms and languages, different identity providers, etc.). Think of Auth10 as a really smart dashboard on top of a Federation Provider. It currently runs on top of Windows Azure Active Directory but it could potentially run on top of other "Fedeation Providers" like ADFS or even AuthBridge.

### How it compares with IdentityServer?

[IdentityServer](http://identityserver.codeplex.com/) is an identity provider that supports Membership provider databases and various protocols to get tokes out of it. It's complimentary to AuthBridge. If IdentityServer is the open source Identity Provider, AuthBridge is the open source Federation Provider. AuthBridge can be configured to trust IdentityServer as well as ADFS.

##Deploy it on your own

### Deploy locally on IIS

1. Download the code
```
git clone https://github.com/auth10/authbridge.git
```
2. Create a site pointing to `AuthBridge.Web` in IIS and assign it a host header like `identity.bridge.com` (otherwise the social identity providers won't work). If it's production use SSL.

3. Create your own certificate. Instructions: https://gist.github.com/3066840

4. Edit the [Web.Config] (https://github.com/auth10/authbridge/blob/master/src/AuthBridge.Web/Web.config) 
  * Change the certificate file  [signingCertificateFile](https://github.com/auth10/authbridge/blob/master/src/AuthBridge.Web/Web.config#L64) (you can also use a certificate in the machine store)
  * Change the [app ids and secrets](https://github.com/auth10/authbridge/blob/master/src/AuthBridge.Web/Web.config#L65) for the identity providers 
  * Add an application as a new [scope](https://github.com/auth10/authbridge/blob/master/src/AuthBridge.Web/Web.config#L93). Note: `identifier` needs to match the "realm" paremeter sent by the application and the uri is the endpoint where the token will be POSTed to.
  * You can decide to use or not the attribute rule transformation engine (`useClaimsPolicyEngine`) per application. If you do, the rule are stored in an XML file on `App_Data`  
5. Create an ASP.NET application with Visual Studio or use the SampleRP that is part of the code and create a trust relationship with AuthBridge. FederationMetadata is available @ http://yourhostname/FederationMetadata/2007-06/FederationMetadata.xml

### Deploy to the Cloud

AuthBridge is ready to be deployed to .NET PaaS like AppHabor or Windows Azure Web Sites. Although currently there is an issue with Google OpenID provider and the discovery process in DotNetOpenAuth that throws an exception in Windows Azure Web Sites.

Follow the instructions 3 and 4 above before deploying it.

Note: there is no database dependency since the configuration is stored on Web.config. There is an extensibility point to change that but no db provider implemented yet.

## TODO

* Exception handling can be enhanced with more detailed errors
* No performance/stress test has been done
* No threats and countermeasures analysis has been done
* Allow configuring allowed identity providers per application
* Signing federation context cookie to avoid tampering
* Implement a config provider that uses blob storage (like s3, azure)
* Implement SAML protocol provider
* Implement a database store for claim rules
* Implement other protocols

## Credits

Some of the code was extracted from a proof of concept we did a couple of years ago with Microsoft, together with @jpgd and @anero79.

## License

MIT

Copyright (2012) Auth10 LLC