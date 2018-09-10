# Capacitor OAuth 2 client plugin

[![npm](https://img.shields.io/npm/v/@teamconductor/capacitor-oauth2.svg)](https://www.npmjs.com/package/@teamconductor/capacitor-oauth2)
[![npm](https://img.shields.io/npm/dt/@teamconductor/capacitor-oauth2.svg?label=npm%20downloads)](https://www.npmjs.com/package/@teamconductor/capacitor-oauth2)

This is a simple OAuth 2 client plugin.

It let you configure the oauth parameters yourself instead of using SDKs. Therefore it is usable with various providers.

## Installation

`npm i @teamconductor/capacitor-oauth2`

## Configuration

This example shows the common process of configuring this plugin.

Although it was taken from a Angular 6 application, it should work in other frameworks as well.

### Register plugin

Find the init component of your app, which is in Angular `app.component.ts` and register this plugin by

```
import {registerWebPlugin} from "@capacitor/core";
import {OAuth2Client} from '@teamconductor/capacitor-oauth2';

@Component()
export class AppComponent implements OnInit {

    ngOnInit() {
        console.log("Register custom capacitor plugins");
        registerWebPlugin(OAuth2Client);
        // other stuff
    }
}
```

### Use it

```typescript
import {
  Plugins
} from '@capacitor/core';

@Component({
  template: '<button (click)="onFacebookBtnClick()">Login with Facebook</button>',
})
export class SignupComponent {
    onFacebookBtnClick() {
        Plugins.OAuth2Client.authenticate({
            appId: "YOUR_FACEBOOK_APP_ID",
            authorizationBaseUrl: "https://www.facebook.com/v2.11/dialog/oauth",
            accessTokenEndpoint:  "https://graph.facebook.com/v2.11/oauth/access_token",
            resourceUrl: "https://graph.facebook.com/v2.11/me",
            web: {
                redirectUrl: "http://localhost:4200/",
                // https://www.w3schools.com/jsref/met_win_open.asp
                windowOptions: "height=600,left=0,top=0"
            }
        }).then(resourceUrlResponse => {
            let oauthUserId = resourceUrlResponse["id"];
            let name = resourceUrlResponse["name"];
            // go to backend
        }).catch(reason => {
            console.error("OAuth rejected", reason);
        });
    }
}
```

Other working examples are:

**Google**

```typescript
Plugins.OAuth2Client.authenticate({
    appId: "YOUR_GOOGLE_APP_ID",
    authorizationBaseUrl: "https://accounts.google.com/o/oauth2/auth",
    accessTokenEndpoint: "https://www.googleapis.com/oauth2/v4/token",
    scope: "email profile",
    resourceUrl: "https://www.googleapis.com/userinfo/v2/me",
    web: {
        redirectUrl: "http://localhost:4200/"
    },
}).then(resourceUrlResponse => {
    let oauthUserId = resourceUrlResponse["id"];
    let name = resourceUrlResponse["name"];
    let email = resourceUrlResponse["email"];
    let fn = resourceUrlResponse["given_name"];
    let ln = resourceUrlResponse["family_name"];
    // go to backend
}).catch(reason => {
    console.error("Google OAuth rejected", reason);
});
```

## Platform: Web/PWA

This implementation just opens a browser window to let users enter their credentials.

As there is no provider SDK used to accomplish OAuth, no additional javascript files must be loaded and so there is no performance
impact using this plugin in a web applications.

- Available since version: **1.0.0-alpha.16**

## Platform: Android

Register the plugin in `your.domain.app.MainActivity#onCreate`

```
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        List<Class<? extends Plugin>> additionalPlugins = new ArrayList<>();
        // Additional plugins you've installed go here
        // Ex: additionalPlugins.add(TotallyAwesomePlugin.class);
        additionalPlugins.add(OAuth2ClientPlugin.class);

        // Initializes the Bridge
        this.init(savedInstanceState, additionalPlugins);
    }
```

**Google**

For Google and hopefully most other providers we use a customScheme on Android to configure it add it to the plugins options.

```
android: {
    // overwrite your default appId with a android specific. e.g. Google needs that.
    appId: "your_android_client_id"
    customScheme: "com.companyname.appname:/"
}
```

and add the following your app `android/app/build.gradle`

```gradle
android.defaultConfig.manifestPlaceholders = [
    'appAuthRedirectScheme': 'your.domain.app'
]
```

**Facebook**

Facebook is special. They do not allow a standard oauth flow on Android. Instead they force us to use their SDK for Android.

Therefore this plugin can load a handler class from your app to retrieve the accessToken and hand it back to the plugin.
You configure that in the plugin options like that:

```
android: {
    customHandlerClass: "com.companyname.appname.FacebookOAuth2Handler",
}
```

This handler class `com.companyname.appname.FacebookOAuth2Handler`
has to implement `com.byteowls.capacitor.oauth2.handler.OAuth2CustomHandler`.
and handles the login.

See https://developers.facebook.com/docs/facebook-login/android/ for details.

- Available since version: **Work in progress**

## Platform: iOS

- ETA November 2018

## Platform: Electron

- No ETA yet

## Contribute

### Fix a bug or create a new feature

Please do not mix more than one issue in a feature branch. Each feature/bugfix should have its own branch and its own Pull Request (PR).

1. Create a issue and describe what you want to do at [Issue Tracker](https://github.com/moberwasserlechner/capacitor-oauth2/issues)
2. Create your feature branch (`git checkout -b feature/my-feature` or `git checkout -b bugfix/my-bugfix`)
3. Test your changes to the best of your ability.
5. Commit your changes (`git commit -m 'Describe feature or bug'`)
6. Push to the branch (`git push origin feature/my-feature`)
7. Create a Github pull request

### Code Style

This repo includes a .editorconfig file, which your IDE should pickup automatically.

If not please use the sun coding convention. Please do not use tabs at all!

Try to change only parts your feature or bugfix requires.

## License

MIT. Please see [LICENSE](https://github.com/moberwasserlechner/capacitor-oauth2/blob/master/LICENSE).

## Team Condcutor

This plugin is powered by [Team Conductor](https://team-conductor.com/en/) - Next generation club management platform.

