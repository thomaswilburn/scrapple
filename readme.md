Scrapple
========

Scrapple is a simple, tiny hashbang router useful for news applications or other simple web apps. It allows you to register callbacks for URL patterns, as well as adding catch-all event handlers for all hits or misses on the routing list, in less than 2KB uncompressed.

Usage
-----

To use Scrapple, first create an instance:

```js
var router = new Scrapple();
```

Next, you'll want to register some routes. These can be simple constants--for example, you might want to have a view that automatically triggers for "yourpage.html#!/about":

```js
router.add("/about", function() {
  console.log("You've triggered the about page");
});
```

Technically, Scrapple is not picky about how you prefix your internal URLs. You can visit "#!/about", "#/about", or "#about", and all of them will fire this route callback. Trailing slashes are also removed. Multiple routes can be registered in a single call, by passing in an array:

```js
router.add(["/", "/home"], function() {
  console.log("Both routes will trigger this function");
});
```

However, routes do not have to be constant. They can also include parameters in the URL, which will automatically be extracted and passed through to your callback function as the `params` property of the event. The event object will also contain a `url` parameter, which is useful for logging.

```js
router.add("/pets/:name", function(e) {
  console.log(`Hello, ${e.params.name}`);
});

router.add("/pets/:name/feed/:food", function(e) {
  var { name, food } = e.params;
  console.log(`${name} is grateful for the ${food}!`);
})

// visiting "/pets/Neko" => "Hello, Neko"
// visiting "/pets/Neko/feed/kibble" => "Neko is grateful for the kibble!"
```

Although Scrapple is very small and simple, it shines when creating template-driven data visualizations and applications, such as the [Seattle Times' mayoral primary guide in 2017](http://projects.seattletimes.com/2017/mayoral-primary-guide/). Jumping to a new route is as simple as writing a link, with no need to manually add event listeners or mark up your HTML:

```html
<a href="#!/go-to/route">This is a router link!</a>
```

Scrapple is an effort to go with the grain of the web platform: by working with hash events, it correctly handles the back button (because it leaves it up to the browser) and makes it easy for your users to bookmark pages or open them in new tabs.

If you want to be notified on any route, or when Scrapple can't find a route to match the current URL hash string, set functions to the router's `onhit` and `onmiss` properties, respectively:

```js
router.add("/route", function() {
  console.log("Route callback");
});

router.onhit = function(e) {
  console.log(`Hit for ${e.url}`);
};

router.onmiss = function(url) {
  console.log(`Visited ${url} but no matching route!`);
};

// visit "/route" => "Hit for /route" and "Route callback"
// visit "/missing" => "Visited /missing but no matching route!"
```

Note that the `onhit` callback fires before the actual route--this can be useful if you need to do any cleanup for the current application state before proceeding to the next route.