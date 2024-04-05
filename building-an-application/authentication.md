# Authentication

So our product owner has come to us with a new feature request. They want us to add authentication to our API. More specifically, they want us to add an API key in front. This means that when a user hits our API with a valid API key in the request they should be served with the response as per normal. But if they make a request to our API either without an API key or without a valid key, then they should be served an [HTTP 401 Unauthorized](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401) error response code.

Sound reasonable enough? For the purposes of our demonstration, we're gonna cut a couple of corners but we'll make it very clear when we do so you know to not repeat that in practice.

***

