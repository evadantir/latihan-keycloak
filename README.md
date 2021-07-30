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

# Summary

[Materi PPT](https://docs.google.com/presentation/d/1a7fBaLuAp6_8ONUKJPPi3sc45LXc_Cgq0H8vU-6yguk/edit?usp=sharing)

[Wiki](https://github.com/evadantir/latihan-keycolak/wiki)
