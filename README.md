# pwa-docs
### Various documentation for developing progressive web apps

♥ [Geek Alert - the practice app](https://geekyandfun.github.io/PWA-workshop/) ♥

------------------------

# Workshop notes

## Friday

### Intro - native apps vs hybrid apps vs PWA

Advantages of an app over web app:
+ UX
+ accesibility
+ works offline
+ access to Native APIs (eg: notifications, NFC, fingerprint sensor)
+ background activity
+ faster (statistically speaking)

What would an ideal chat app look like:
+ be app-like 
  + full screen (no browser bar)
  + home-screen icon 
+ works offline
  + shows the UI 
  + shows the latest received messages 
+ blazing fast
+ push notifications upon new messages
+ background send of messages (no further interaction required. It automatically sends them when it finds a connection)

### Architecture
+ How to make it app-like?
  + You need to include an [App Manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest)
  + In order for Chrome to automatically suggest adding it to the homescreen, the app also needs to register a [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
+ How to make it work offline?
  + We [cache](https://developer.mozilla.org/en-US/docs/Web/API/Cache) all the UI-needed files (eg: index.html, style.css ...) and then respond with them from the cache instead of network. This way, even when we're offline we can see the UI
  + We also store the latest messages in [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) and show them on the UI in case of no connection
  + These 2 strategies make the app boot super fast even on no connection or very slow ones.
+ How do Push Notifications work in browsers ?
  + Every browser vendor (eg: Chrome, Firefox, Edge ...) has it's own dedicated server handling push notifications
  + Sending a push notification to a certain device means sending an authorized request to that vendor's server
  + [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) is a great way to test Push Notifications without having to implement/write the underlying request logic
+ How to send the messages in the background?
  + In case of no connection we store the message in IndexedDB with a **unsent** flag. When the connection returns, we send all the unsent messages from the Service Worker
+ using localStorage vs IndexedDB:
  + localStorage is synchronous and accepts only strings for both key & value
  + IndexedDB is async, therefore can be used from both a Worker and Main thread. That's why it's somewhat of a standard for PWA storage

### How is this all possible?
+ Most of those app-like functionalities are only available due to a new type of worker: [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)


How will we use IndexedDB in the context of our chat app?
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
