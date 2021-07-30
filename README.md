# Docker-compose

> Docker-compose configuration for setting up keycloak and it's database (in this project we use Postgres as it's database)

```bash
# to run docker-compose
docker-compose up
```

# App-vue

> Sample application vue app secured with keycloak

## Vue Configuration (if you want to make the vue app from scratch)

### Install Vue - Cli
```bash
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```
Addon init plugin
```bash
npm install -g @vue/cli-init
# OR
yarn global add @vue/cli-init
```

### Init Vue App
```bash
# Let’s create a simple Vue application using Vue-CLI

vue init webpack-simple app-vue

# Adding official keycloak js adapter

npm i keycloak-js --save

# Adding logging library

npm i vuejs-logger --save
```

### Vue App Configuration

- Replace `App.vue` codes with this code [App.vue](https://github.com/evadantir/latihan-keycloak/blob/main/src/App.vue)

- Add this below snippet in your `src/main.js`

```js
// initOptions variable contain configuration for Keycloak Adapter
let initOptions = {
  // url: 'linkkeycloak', realm: 'namarealm', clientId: 'namaclient', onLoad: 'login-required'
  url: 'http://127.0.0.1:8080/auth', realm: 'keycloak-demo', clientId: 'app-vue', onLoad: 'login-required'
}

let keycloak = Keycloak(initOptions);

keycloak.init({ onLoad: initOptions.onLoad }).then((auth) => {
  if (!auth) {
    window.location.reload();
  } else {
    Vue.$log.info("Authenticated");

    new Vue({
      el: '#app',
      render: h => h(App, { props: { keycloak: keycloak } })
    })
  }


//Token Refresh
  setInterval(() => {
    keycloak.updateToken(70).then((refreshed) => {
      if (refreshed) {
        Vue.$log.info('Token refreshed' + refreshed);
      } else {
        Vue.$log.warn('Token not refreshed, valid for '
          + Math.round(keycloak.tokenParsed.exp + keycloak.timeSkew - new Date().getTime() / 1000) + ' seconds');
      }
    }).catch(() => {
      Vue.$log.error('Failed to refresh token');
    });
  }, 6000)

}).catch(() => {
  Vue.$log.error("Authenticated Failed");
});
```

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).

# Keycloak Setup & Client Configuration
Ref: https://www.keycloak.org/securing-apps/vue

## Login to the admin console

Go to the Keycloak Admin Console and login with the username and password you created earlier.

## Create a realm

A realm in Keycloak is the equivalent of a tenant. It allows creating isolated groups of applications and users. By default there is a single realm in Keycloak called master. This is dedicated to manage Keycloak and should not be used for your own applications.

Let’s create our first realm.

1. Open the Keycloak Admin Console

2. Hover the mouse over the dropdown in the top-left corner where it says `Master`, then click on `Add realm`

3. Fill in the form with the following values:

        Name: `keycloak-demo`

4. Click Create

![image](https://user-images.githubusercontent.com/11324759/127603745-371a1c60-6f94-43c2-adce-dab797dbf5b4.png)

## Create a User



Initially there are no users in a new realm, so let’s create one:

1. Open the Keycloak Admin Console

2. Click Users (left-hand menu)

       - Click `Add user` (top-right corner of table)

3. Fill in the form with the following values:

       - Username: `myuser`

       - First Name: Your first name

       - Last Name: Your last name

4. Click `Save`

![image](https://user-images.githubusercontent.com/11324759/127603922-2fc44828-e979-422e-8ea0-6f1ad6530315.png)



The user will need an initial password set to be able to login. To do this:

1. Click `Credentials` (top of the page)

2. Fill in the `Set Password` form with a password

3. Click `ON` next to `Temporary` to prevent having to update password on first login

![image](https://user-images.githubusercontent.com/11324759/127603964-0a029fd0-dffa-48b9-820a-9804b6679f3f.png)


## Client Configuration



Clients tab allows you to manage your allow application clients

1. Adding a Client: It is simple to add a client, the same process works for any type of application.

     -   Client ID: You can give any suitable name for your application

     -   Protocol: ‘OpenID-Connect/SAML’

     -   Root Url: Application Hostname

![image](https://user-images.githubusercontent.com/11324759/127604031-81b53a3d-cd4b-4003-90c1-84ec9a7f1f8e.png)



Configure these two fields are important:

1. Valid Redirect Url: After Authentication where should keycloak redirect? (dependent upon Web Origin config, otherwise you will see an error: Invalid request URI)

2. Web Origin: * or + ( * for wildcard redirection and + for domain-specific(validate all sub-domains): eg: *.redhat.com)





# Summary

[Materi PPT](https://docs.google.com/presentation/d/1a7fBaLuAp6_8ONUKJPPi3sc45LXc_Cgq0H8vU-6yguk/edit?usp=sharing)

[Wiki](https://github.com/evadantir/latihan-keycolak/wiki)
