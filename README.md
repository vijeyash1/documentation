**Documentation for becoming a OIDC / 0Auth2.0 provider**

***Before Going further lets look into some of the important Terminologies***


*whats social login ?*
>   Social login is a single sign-on (SSO) technology that allows users to authenticate themselves on various applications and sites by connecting through a site which implements Oauth or OIDC **(in our case Intelops implements hydra which is a Oauth , OIDC provider)**  rather than typing a separate ID and password on each website.

*What is Authentication?*

> authentication means, **the user or computer has to prove its identity to the server or client**. Usually, authentication by a server entails the use of a user name and password. Other ways to authenticate can be through cards, retina scans, voice recognition, and fingerprints

*What is Authorization?*

>   Authorization is  **the process of giving someone permission to have access to something**.


**Some Terminologies around 0auth**


 - **Authorization server**: The server that issues access tokens to clients after successfully authenticating the resource owner and obtaining authorization.
 - **Resource owner**: The entity that can grant access to a protected resource. This is typically the end-user.
 - **Client**: An application that requests access to a protected resource on behalf of the resource owner.
 - **Resource server**: The server hosting the protected resources and capable of accepting and responding to protected resource requests using access tokens.
 - **Access token**: A token that is issued by the authorization server and used by the client to access the protected resources on the resource server.
 - **Refresh token**: A token that is issued by the authorization server and used by the client to obtain a new access token when the original access token becomes invalid or expires.
 - **Scope**: A list of permissions that the client can request to access protected resources on the resource server.
 - **Grant types**: The OAuth 2.0 specification defines several grant types, which indicate how the client can obtain an access token. Examples include authorization code, implicit, resource owner password credentials, and client credentials.
 - **Authorization code**: A grant type in which the client obtains an authorization code from the authorization server and exchanges it for an access token. This grant type is commonly used in server-side web applications.
 - **Implicit**: A grant type in which the authorization server issues an access token directly to the client. This grant type is commonly used in client-side web applications and mobile apps.
 - **Resource owner password credentials**: A grant type in which the client obtains an access token by presenting the resource owner's username and password to the authorization server.
 - **Client credentials**: A grant type in which the client obtains an access token by presenting its own credentials (client ID and secret) to the authorization server.


