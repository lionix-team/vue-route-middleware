# vue-route-middleware &nbsp;[![](https://img.shields.io/npm/dt/vue-route-middleware.svg)](https://www.npmjs.com/package/vue-route-middleware) [![](https://img.shields.io/npm/v/vue-route-middleware.svg)](https://www.npmjs.com/package/vue-route-middleware)


Make complex global route middleware logic look easy and readable!  


## Installation:

```bash
npm i vue-route-middleware
```


## Basic Usage:

Set `middleware` meta key to your route and add our component to any vue router guard hook.

```js
import VueRouteMiddleware from 'vue-route-middleware';
...
const Router = new VueRouter({
    mode: 'history',
    routes: [{
        path: '/dashboard',
        name: 'Dashboard',
        component: Dashboard,
        meta: {
            middleware: (to, from, next) => {
                let auth = fakeUser.isLogged();
                if(!auth){
                    next({ name: 'Login' });
                }
            }
        }
    }]
});
...
Router.beforeEach(VueRouteMiddleware());
```


**NOTICE:** Middleware function will retrieve all the variables normally passed to the router guards  
Example: (`to`, `from`, `next`) in case of `beforeEach` or (`to`, `from`) in case of `afterEach` guard.

### Chain middlewares with an array of functions:

#### Example:

```js
middleware: [
    (to, from, next) => {
        let auth = fakeUser.isLogged();
        if(!auth){
            next({ name: 'Login' });
            return false; // if false is returned, middleware chain will break!
        }
    },
    (to, from, next) => {
        let hasPaied = fakeUser.madePayment();
        if(!hasPaied){
            next({ name: 'Payment' });
        }
    }
]
```


**NOTICE:** If function returns `false` then the middleware chain will break and no longer execute.


### Separated middleware file example:

#### ./route/middleware/auth.js

```js
export default (to, from, next) => {
    let auth = fakeUser.isLogged();
    if(!auth){
        next({ name: 'Login' });
        return false;
    }
}
```

#### router.js

```js
import AuthMiddleware from './route/middleware/auth';
...
meta: {
    middleware: [ AuthMiddleware ]
}
...
```


## Advanced:

Another way to define middlewares is to pass them in the object as the first parameter to  
**VueRouteMiddleware** function and pass an array of middleware key names to the meta of the route.

#### Example:

```js
import AuthMiddleware from './route/middleware/auth';
import PaymentMiddleware from './route/middleware/payment';
...
meta: {
    middleware: [ 'AuthMiddleware', 'PaymentMiddleware' ]
}
...
Router.beforeEach(VueRouteMiddleware({ AuthMiddleware, PaymentMiddleware }));
```

This way we can differentiate middlewares that will be applied with different guards.  
For example you want to add tracking middleware to `afterEach` guard:

#### Example:

```js
import AuthMiddleware from './route/middleware/auth';
import PaymentMiddleware from './route/middleware/payment';
import TrackingMiddleware from './route/middleware/tracking';
...
meta: {
    // define all applied middleware to the route
    middleware: [ 'AuthMiddleware', 'PaymentMiddleware', 'TrackingMiddleware' ]
}
...
Router.beforeEach(VueRouteMiddleware({ AuthMiddleware, PaymentMiddleware }));
// Pass the tracking function to afterEach guard
Router.afterEach(VueRouteMiddleware({ TrackingMiddleware }));
```

At the example above `beforeEach` guard will apply chained `AuthMiddleware` and `PaymentMiddleware` ignoring the `TrackingMiddleware` that will be applied on `afterEach` guard.


## Credits:
- [Lionix Team](https://github.com/lionix-team)
- [Stas Vartanyan](https://github.com/vaawebdev)