# freelock/reststate-vuex

**This package is forked from @reststate/vuex, which is no longer maintained, and updated to support Drupal JSONAPI.**

`@reststate/vuex` allows you to access data from a [JSON:API](http://jsonapi.org/) web service via [Vuex](https://vuex.vuejs.org/) stores. Because of JSON:API's strong conventions, in most cases all you should need to do is tell `@reststate/vuex` the base URL of your web service, and which resources to access, and you should be set. No manual web request juggling!

## Synopsis

```javascript
const store = new Vuex.Store({
  modules: {
    'node--article': resourceModule({
      name: 'node--article',
      httpClient: axios.create(...),
    }),
  },
});

const component = {
  methods: {
    ...mapActions({
      loadAllArticles: 'node--article/loadAll',
    }),
  },
  computed: {
    ...mapGetters({
      'articles': 'node--article/all',
    }),
  },
};
```

## Installation

```
# npm install --save @reststate/vuex
```

## Setup

To create a Vuex module corresponding to a resource on the server, call `resourceModule()`:

```javascript
import { Store } from 'vuex';
import { resourceModule } from '@reststate/vuex';
import api from './api';

const store = new Store({
  modules: {
    'node--article': resourceModule({
      name: 'node--article',
      httpClient: api,
    }),
  },
});
```

If you are accessing multiple resources, you can use `mapResourceModules()`:

```javascript
import { Store } from 'vuex';
import { mapResourceModules } from '@reststate/vuex';
import api from './api';

const store = new Store({
  modules: {
    ...mapResourceModules({
      names: ['node--article', 'commerce_order--order'],
      httpClient: api,
    }),
  },
});
```

The `httpClient` accepts an object with a signature similar to the popular [Axios](https://github.com/axios/axios) HTTP client directory. You can either pass in an Axios client configured with your base URL and headers. Note that spec-compliant servers will require a `Content-Type` header of `application/vnd.api+json`; you will need to configure your HTTP client to send that.

```javascript
import axios from 'axios';

const httpClient = axios.create({
  baseURL: 'http://api.example.com/',
  headers: {
    'Content-Type': 'application/vnd.api+json',
    Authentication: `Bearer ${token}`,
  },
});

const module = resourceModule({
  name: 'node--article',
  httpClient,
});
```

Or else you can pass in an object that exposes the following methods:

```javascript
const httpClient = {
  get(path) {
    // ...
  },
  post(path, body) {
    // ...
  },
  patch(path, body) {
    // ...
  },
  delete(path, body) {
    // ...
  },
};
```

That's all you need to do--the JSON:API spec takes care of the rest!

## Usage

For more information on usage, see the [`@reststate/vuex` docs](/docs/README.md).

## Changes

4/21/2022 - changed the usage pattern for Drupal JSON:API. In older versions, you would declare a VueX module using "<EntityType>/<Bundle>", and this library would automatically convert relationships listed as "<EntityType>--<Bundle>" to match. However, this pattern does not work in VueX 4, especially in the VueX dev tools.

With release 0.4.0, we reverse this pattern -- now you should declare all modules as "<EntityType>--<Bundle>", and an update to @freelock/reststate-client will auto-convert this pattern to a URL of "<EntityType>/<Bundle>" when querying the server.

## License

Apache 2.0
