# sw-precache-list

## Precache and remain in control of the service worker

Want to generate a list of static resources to precache with your service worker?

Want to be able to write your own service worker script?

`sw-precache-list` may be for you...

_Please note, this tool is in progress and not suitable for production usage yet._


## Example usage

### Command line

You can use globs directly on the command line:

```
sw-precache-list images/**/* css/*
```

You can also pass strings:

```
sw-precache-list 'images/**/*' 'css/*'
```

### As part of your build process

_Coming soon..._

```
var swPrecacheList = require('sw-precache-list');

swPrecacheList.write(`${rootDir}/precache-list.js`, ['images/**/*', 'css/*']);
```

## Output

The resulting file (default: `precache-list.js`) will output the resource list to a global variable 
called `precacheList`. You can use it in your service worker script via `importScripts`. Here is an 
example:

```
// service-worker.js

importScripts('precache-list.js');

self.addEventListener('install', function(event) {

  function onInstall () {
    return caches.open('myCacheName')
      .then(function(cache) {
        return cache.addAll( precacheList );
      });
  }

  event.waitUntil(onInstall(event));
});
```

Or even easier, using `sw-toolbox`:

```
importScripts('lib/sw-toolbox.js');
importScripts('precache-list.js');

toolbox.precache(precacheList);
```

## How does this compare to [sw-precache](https://github.com/GoogleChrome/sw-precache)?

`sw-precache` is designed to generate the whole service worker script for you. You cannot manually
edit it, because it will get overwritten next time you run it.

`sw-precache-list` is designed to just generate the precache resource list, which you can use
in a service worker that you write yourself.


## Still to come

### Cache-busting

`sw-precache-list` is currently very new and simple. It does not yet include cache-busting.

This means that right now you should only use it for static resources that _will not
be updated_ (except if you update the filenames).

For more information, see [sw-precache's note on cache busting here](https://github.com/GoogleChrome/sw-precache/#user-content-dontcachebusturlsmatching-regex)
and [Jake Archibald's note here](https://jakearchibald.com/2016/caching-best-practices/#a-service-worker-can-extend-the-life-of-these-bugs).

We expect to add cache-busting soon, or if you'd like to add it, please submit a PR!

### Config

We will also need to add the ability to e.g. strip a directory prefix, to convert local file paths
into resource URLs.
