# pwa-docs
### Various documentation for developing progressive web apps

🚀 [Live practice app](https://geekyandfun.github.io/PWA-workshop/) 🚀

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
Most of those app-like functionalities are only available due to a new type of worker: [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)


How will we use IndexedDB in the context of our chat app?
+ save unsent messages in this

`DevTools > Application tab > manifest.json`:
+ the manifest can exist without a service worker
+ prompt for app installation is received only on service worker register

+ why are push notifications rights handled differently than webcam rights ? why wouldn't an icon in the address bar show that the service worker has been disabled and you could enable it, the same as for webcam ?

**Interesting**: 
+ once a user denies the [Notification](https://developer.mozilla.org/en-US/docs/Web/API/notification) permission, it stays "denied" forever. That's why some sites show a "custom notification" dialogue and only if you click "Yes" will they show you the real push notification permission. This way, if you say no you didn't deny the Notification permission but that custom dialog.

+ Will `clear history` clear the service worker ? Or do you have to go to `chrome://serviceworker-internals` and `unregister` ?
  + No, clearing only the history will not clear the service worker.
  + If you want to clear everything, including unregistering the Service Worker, go to `Dev Tools => Application Tab => Clear Storage => Clear site data` (in Chrome)

### References
+ [**Slides**](http://slides.com/iampava/pwa-workshop#/)

------------------------

## Saturday
Caching static files so that the app works even without connection. End of day branch: https://github.com/GeekyAndFun/PWA-workshop/tree/day11


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

Intercepting network requests by `service-worker.js`:
```javascript
self.addEventListener('fetch', (event) => {
    console.log('worker intercepted: ', event);
});
```

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

⚠ `Careful ` ⚠ 
+ Access DevTools > Console > Settings wheel > Tick preserve log (in order to have logs for service worker install - it happens before clear console log at F5 refresh)
+ Service worker offline tick box from devtools (application tab) doesn't work for sync event.

### Interesting
+ we can emulate the `multi-threading` concept in JavaScript by using Workers (Web Workers, Shared Workers or Service Workers)
+ Workers and the script that spawned them can share data (eg: [SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)) 
+ `app.js` uses import without webpack by using `type="module"` | [JavaScript modules](https://jakearchibald.com/2017/es-modules-in-browsers/)
```html
<script src="./public/scripts/app.js" type="module"></script>
```
+ modern browsers use Fetch behind the scenes when you do an old-fashioned XHR

### Reference
+ [Service Worker Cookbook](https://serviceworke.rs/)
+ https://developers.google.com/web/ilt/pwa/caching-files-with-service-worker
+ Use manifest generator - site icon in different sizes - https://app-manifest.firebaseapp.com/ / https://tomitm.github.io/appmanifest/

+ https://developer.mozilla.org/en-US/docs/Web/API/Cache
+ https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers


+ relationship between web workers and service workers - https://stackoverflow.com/questions/38632723/what-can-service-workers-do-that-web-workers-cannot
+ animation worker might appear [Worklets](https://developer.mozilla.org/en-US/docs/Web/API/Worklet)

------------------------

## Sunday

...

------------------------
