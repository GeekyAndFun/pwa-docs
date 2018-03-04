# pwa-docs
various documentation for developing progressive web apps


https://geekyandfun.github.io/PWA-workshop/

------------------------
# Workshop notes

## Friday

### Intro - native apps vs hybrid apps vs PWA

Advantages of an app over web app:
+ UX
+ accesibility
+ works offline
+ native apps APIs (eg: notifications)
+ background activity

What should a chat app have:
+ be app-like
+ have camera access
+ offline notifications
+ background send


### Architecture

+ How push notifications work in browsers ?
  + Every browser has an implementation for this...

+ using localStorage vs IndexedDB:
  + localStorage is synchronous
  + IndexedDB is async, therefore it's somewhat of a standard for PWA

How will we use IndexedDB ?
+ save unsent messages in this

`DevTools > Application tab > manifest.json`:
+ the manifest can exist without a service worker
+ prompt for app installation is received only on service worker register

+ why are push notifications rights handled differently than webcam rights ? why wouldn't an icon in the address bar show that the service worker has been disabled and you could enable it, the same as for webcam ?

**Interesting**: Sites sometimes use a "prenotification" for push notif., such as if you disable notification when it's asking the rights, there'll remain a chance to ask again (if there is only the notification, say no once and you'll never get prompt again)

+ Will `clear history` clear the service worker ? Or do you have to go to `chrome://serviceworker-internals` and `unregister` ?


------------------------

## Saturday
+ saturday branch - https://github.com/GeekyAndFun/PWA-workshop/tree/day11

**Interesting**: App.js uses import without webpack by using `type="module"`:
```html
<script src="./public/scripts/app.js" type="module"></script>
```


Fetch api example (api from https://github.com/toddmotto/public-apis)
```javascript
    // resp => resp.json()
    // it needs this in order to work, gives more control
    // you could make streams with this API (fetch API)
    fetch('https://dog.ceo/api/breeds/list/all')
            .then(resp => resp.json())
            .then(respJson => {
                console.log(respJson);
            })
            .catch(err => console.log(err)); // this triggers only at timeout, CORS, not on server-related errors (eg: http errors)
    //https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
```


**Interesting**: Current browser don't use XHR anymore behind-the-scenes, only fetch (**source ?**).



**Interesting**: Event interception by `service-worker.js`:
```javascript
self.addEventListener('fetch', (event) => {
    console.log('worker intercepted: ', event);
});
```

`Careful !`: Service worker offline tick box from devtools (application tab) doesn't work for sync event.



**Interesting**: web workers have shared buffers.


**caching of static resources, cache versioning** (using cache namespace w counter or timestamp, cache built on install):
```json
{
    "TO_PRECACHE": [
        "/",
        "public/style.css",
        ...
    ],
    "VERSION": 1
}
```

`Careful !`: Access DevTools > Console > Settings wheel > Tick preserve log (in order to have logs for service worker install - it happens before clear console log at F5 refresh)

```javascript
resp.json().then(jsonResp => {
    Promise.all(
        //code
    ).then(resolve);
})
```



### Reference
+ https://developers.google.com/web/ilt/pwa/caching-files-with-service-worker
+ Use manifest generator - site icon in different sizes - https://app-manifest.firebaseapp.com/ / https://tomitm.github.io/appmanifest/

+ https://developer.mozilla.org/en-US/docs/Web/API/Cache
+ https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers


+ relationship between web workers and service workers - https://stackoverflow.com/questions/38632723/what-can-service-workers-do-that-web-workers-cannot
+ animation worker might appear ???


------------------------

## Sunday

...

------------------------
