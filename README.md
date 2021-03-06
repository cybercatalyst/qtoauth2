[![Qt Pods](http://qt-pods.org/assets/logo.png "Qt Pods")](http://qt-pods.org)

QtOAuth2
=======

Qt OAuth 2.0 library

I am aware there is qt-oauth-lib from ICS, but there were reasons to write my
own OAuth2 client.

qt-oauth-lib is using a webview to display Google's authorization page, then
doing string parsing magic to catch the result. This is very tweaky and introduces an unnecessary dependency to the Qt's GUI module. Also, it somehow abuses
the "how it was meant to be"-way, since qt-oauth-lib predicts to be a website,
while it is an app performing the request.

Also, it is store the access token in the system settings, which is a violation of SoC.

QOAuth2 is different, as it only performs necessary network requests and
performs authorization as a "device". Practically, you're not bound to the GUI
module anymore, so for example, you could write a server side software querying
for data via the Google API. It is basically the "how it should be done"-way
for OAuth2 with Qt apps.

How to use
==========

qoauth2 is available as a pod through qt-pods. See here for more info:
https://github.com/cybercatalyst/qt-pods

```cpp
  // Create service
  GoogleOAuth2Service oAuth2Service(
    "{Client ID}",
    "{Client Secret}",
    "{Scope}");
  // Retrieve user code, ie. information for requesting access.
  // Asynchronous, listen for userAuthorizationRequired-signal.
  oAuth2Service.retrieveUserCode();
  
  // Once the user has granted access, you can retrieve an access token via the device token.
  // Asynchronous, listen for accessTokenReceived-signal.
  oAuth2Service.retrieveAccessToken("{Device Token}");
  
  // When the access token expires, you need to obtain a new access token via the provided refresh token.
  // Asynchronous, listen for accessTokenReceived-signal.
  oAuth2Service.refreshAccessToken("{Refresh Token}");

```
For reference, these are the signals emitted by the service:
```cpp
    /**
     * The server prompts you to authenticate your application.
     * @param deviceCode Store the device code to query API tokens.
     * @param userCode The user is supposed to enter the userCode at the given verificationUrl.
     * @param verificationUrl The url that the user code needs to be entered at.
     * @param expiresIn
     * @param interval
     */
    void userAuthorizationRequired(QString deviceCode,
                                   QString userCode,
                                   QString verificationUrl,
                                   int expiresIn,
                                   int interval);

    /**
     * The server sent you the queried access token.
     * @param accessToken The API access token. You will need this to access the queried API.
     * @param tokenType The token type.
     * @param expiresIn
     * @param refreshToken The refresh token. You will need this to renew the accessToken once it has expired. May be empty.
     */
    void accessTokenReceived(QString accessToken,
                             QString tokenType,
                             int expiresIn,
                             QString refreshToken);

    void tokenRetrieveError(QString error, QString errorDescription);
```
![Flow](https://github.com/cybercatalyst/qoauth2/blob/master/flow.png "Flow")
