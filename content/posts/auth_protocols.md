---
title: "My First Post"
date: 2020-05-28T13:44:54-04:00
---
# THIS NOT THE FINAL VERSION  
Hello everyone. As a security enthusiast who is still learning, I find a lot of the technologies which act as the backbone of Security tend to be ignored. I found my knowledge to be lacking in multiple areas and decided to start with "auth" protocols. This post is just to collect my understandings and may contain inaccuracies.

### **What is an auth protocol?**

Simplifying Wikipedia’s definition of an Auth Protocol. Authentication protocols are a way for two computers to communicate with each other, where one computer or it’s user is authenticating themselves to the other computer or the other way round as well. [Wikipedia Authentication Protocols](https://en.wikipedia.org/wiki/Authentication_protocol)

### **What do we refer by "auth" protocols?**

Initially it was just authentication protocols but soon protocols were able to handle authorization as well. Some protocols are able to handle Accounting as well.


## **Authentication Protocols in the Enterprise World**

While studying for my Security+ certification I learnt about some of the Authentication Protocols. Some of the common ones are:

**CHAP(Challenge-Handshake Authentication Protocol):** CHAP is an identity verification protocol. CHAP uses a “challenge” along with the user’s shared secret key. 

**EAP(Extensible Authentication Protocol):** EAP was originally developed for Point-to-Point protocol. Nowadays used in 802.11 and is a part of the 802.1x authentication framework. 

**RADIUS(Remote Authentication Dial-In User Service):** RADIUS is a AAA protocol which uses a client/server model. It uses a network access server(NAS), which accepts users’ requests. It then forwards them to the RADIUS server with the user’s details.

**LDAP(Lightweight Directory Access Protocol):** LDAP itself is not an Auth protocol but can be thought of as a language which helps programs and applications(on different platforms) communicate with Directory Service(Active Directory for example) Servers for information such as users, passwords and other computing resources.
+ An LDAP client is just a system or application with LDAP login enabled while an LDAP server provides a centralized user database, where a user can authenticate itself using a password to access network resources.
+ The aforementioned Directory Services servers are a type of network database which have a flexible schema. Active Directory is one of them
+ LDAP can also be configured to handle authorization as well. As it checks if the user has the authority to access that resource.
+ ***LDAP's Working Flow:*** 
    1. In short, a client sends a request for information stored within an LDAP database along with the user’s credentials to an LDAP server. 
    2. The LDAP server then authenticates the credentials submitted by the user against their core user identity, which is stored in the LDAP database.
    3. If the credentials submitted by the user match the credentials associated with their core user identity that is stored within the LDAP database, the client is granted access and receives the requested information. If not, the client is denied access to the LDAP database.

**Kerberos:** Unlike LDAP Kerberos is an out and out Authentication protocol which is the default method of authentication in Windows and supports Single-Sign-On authentication.
+ Kerberos can also be configured to handle authorization and accounting.
+ Kerberos and LDAP could be made to work together where Kerberos and it’s SSO is responsible for handling Authentication, while LDAP is responsible for handling Authorization.
+ This is considered as the best practice as it reduces the load on the Directory Services Server due to the decreased number of LDAP lookups for authentication.
+ ***Kerberos' Working Flow:*** 
    1. Client requests an authentication ticket(Ticket Granting Ticket) from the Key Distribution Center(KDC).
    2. The KDC verifies the credentials and sends back a TGT which is encrypted with the TGS session key.
    3. The client then sends the TGT to the Ticket Granting Service(TGS) along with the Service Principal Name of the resource that the client wants to access.
    4. The KDC verifies the TGT of the user and that the user has the rights to access the service.
    5. The TGS responds with a session key for the service.
    6. The client forwards the session key to the service that the user wants to access.

## **Authentication Protocols in the Web Services World**

**SAML(Security Assertion Markup Language):** is based on XML and works as a protocol for a user to authenticate to the web servers. SAML allows for Single-Sign-On which is one of it’s  most common use cases. 
SAML has two very important components:
1. Identity Provider(IdP): These are trusted third-party services who create, store and maintain user identities. IdPs are also responsible for authentication and also authorization to some extent. Ex. Okta, Auth 0, Azure Active Directory, G Suite, Azure  
2. Service Providers(SP): These are the services that the user wants to use and may request access to. Ex. GMail, Google DOCs, Hangouts
SAML provides a way for the user to authenticate itself at a single login page with its credentials and access other services that it has authority to without supplying the credentials again. 


**Deeper into SAML’s functioning:**
+ SAML Assertion: This is the response from the IdP to the SP which basically contains the authentication information of the user in an XML form. It can either be an assertion(user successfully authenticated) or an error.
+ There are three types of SAML assertions:
    1. Authentication assertions prove the identity of the user and provide additional identification information and the user’s details.
    2. Attribute assertion provides the attributes that the user is associated with. These attributes provide information regarding the user’s authentication.
    3. Authorization decision assertion is the information whether a user has been authorized to access the service or not.
+ SAML also makes use of Certificates to handle integrity. These certificates are base64 encoded. They may or may not be encrypted. 
+ There are 2 types of modes in which SAML generally operates:
    1. SP Initiated: In this mode the user likely tries to login to the SP and is prompted with a “login with SAML” button. When the user clicks on the button the SP sends an AuthnRequest to the IdP to authenticate the user. If the user already has an active session or the user successfully authenticates itself to the IdP then Authentication is asserted by the IdP. 
    2. IdP Initiated: In this mode the user initially authenticates itself to the IdP and if the authentication is successful then it is asserted to the SPs in future.
 
 
**How the data in SAML flows:**
1. The user tries to access a service or application. 
2. The application identifies some of the user’s details and redirects to the IdP.
3. The user can have either an existing active session or establishes a new session by logging in.
4. The IdP builds an authentication response with the user's method of identity(username or email address) and signs it using a X.509 certificate and sends this to the Service Provider.
5. The service provider retrieves the authentication response and validates it with the certificate.
6. Upon successful validation the user has been given access to the app.
 

## **Now where does OAuth 2.0 and OpenID Connect come in?**
In the earlier days of the internet if a user wanted to share his/her information from one service to another this would usually entail the user to provide the credentials of the first service to this service.
 
The problem from the user’s perspective is that there’s no guarantee how this service is going to use, store and manage these credentials.
 
The problem from the service’s end is management of such credentials is extremely risky and a breach could mean an extremely costly affair. 
 
OpenID Connect and OAuth 2.0 together solved this problem.
 
## **OAuth 2.0**
 
**OAuth 2.0:** It is an open-standard authorization protocol where a user can authorize an application to access their information from another application or service. 
+ OAuth 2.0 is the technology that lets you allow your favorite video game(e.g. FIFA20) to post your accomplishments on the social network(e.g. Twitter) of your choice without you having to share your social network credentials with the game’s software.
+ For understanding the flow we would follow our previous example where you would like to share a picture of the final scoreboard from FIFA20 on your Twitter feed. 
+ Before we can get into OAuth’s flow there are few components that we need to understand.
+ OAuth defines four roles:
  1. Resource Owner: Is the user. User owns the Identity and is responsible for authorizing an application to access their account.
  2. Client: The application(e.g. FIFA20) that wants to access your account(e.g. Twitter) and perform actions on behalf of the resource owner. 
  3. Authorization Server: The server(Twitter’s authorization server) that verifies the identity of the Resource Owner 
  4. Resource Server: The Application Programming Interface(API) or service the client wants to access.
+ Important OAuth Terminology:
  * Redirect URL: The URL that the Authorization Server redirects the Resource Owner to, once they grant permissions to the Client. This is usually defined during the Application Registration.
  * Scope: These are the finer set of permissions that Client needs to perform the necessary actions.
  * Client ID: The client’s identifier which is known to the Authorization Server.
  * Client Secret: It is a secret password that is used to authenticate the Client to the Authorization Server.
  * Authorization Code: This is a temporary code which the Client sends to the Authorization Server in return for the Access Token. Used to keep track of the auth process.
  * Access Token: This will be used as a key when the Client makes requests to the Resource Server API. This key denotes that the user has necessary permission to perform the actions.

**OAuth 2.0 Flow:**
1. The Resource Owner(i.e. You) want the Client(FIFA 20) to access your Resource Server(Twitter account) and post the final scoreboard to your feed.
2. The Client spawns a browser and redirects to the Authorization Server page with a URL consisting of the Client ID, Redirect URL, Response Type and the Scope.
3. If you do not have an active session the Authorization Server may ask you to login and verify who you are.
4. The Authorization Server prompts you for your Consent along with the requested Scopes by the Client, which you can approve or deny.
5. If Consent is granted then the authorization server Redirects back to the Client using the Redirect URL along with the Authorization Code.
6. The Client makes a final request to the Authorization Server with the Client ID, Client Secret and the Authorization Code.
7. The Authorization Server verifies the Client’s details and responds with an Access Token.
8. This Access Token is then used by the Client to make requests to the Resource Server to post the picture to your feed. 


What we just saw was one of most common and safest flows of OAuth 2.0. Called the Authorization Code flow.

Some other OAuth flow types:
* Implicit Flow: Implicit flow is a simplified version of the Authorization Code flow with the Auth Server instead of returning an Auth code it returns an Access Token. In this flow the clients are public and thus cannot be authenticated. As there is no verification of the Client it introduces a major risk. It is well suited for Mobile devices and webapps. 
* Resource Owner Password Credentials Flow: in ROPC the Resource Owner provides their credentials which are then forwarded by the Client to the Authorization Server. This beats the purpose of OAuth as the user has to share their credentials and should be kept as the last option.
* Client Credentials Flow: This flow is intended only for server to server communications with no user involved.

Any of the above OAuth 2.0 flows can be implemented depending on the use case of the application.

## **OpenID Connect**

+ We have now seen that OAuth takes care of Authorization but Authentication? That can be taken care of by the OpenID Connect(OIDC).
+ OpenID Connect is a layer above OAuth which handles the authentication of the user and maintains the identities of the users. It also maintains information regarding users who have already established a session.
+ When OpenID is implemented in an Authorization Server it can be thought of as Identity Provider similar to what we had seen in SAML. 
+ OpenID allows the user to login to a trusted service and use that login to access multiple services. Thus allowing for Single-Sign-On. 
+ The two main additions when OIDC is used with OAuth, is the Scope parameter includes “openid” and an ID token that is issued along with the Access Token. 
+ ID tokens are always in JWT format. These tokens are always base64url encoded. 
+ JSON Web Tokens are a standardized container format that is used to transfer data between two parties. These tokens can be signed and/or encrypted for the secure transmission of data.



