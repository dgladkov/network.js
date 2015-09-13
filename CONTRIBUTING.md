# Contributing

Before you submit any pull request, please check if your code can pass all the tests by running:

```shell
npm test
```

If your code contains new features, add the corresponding tests.

Respect the coding style, 4 spaces, no whitespaces at the end of lines, prefer single quotes, etc…

## Server specification

Currently, the server part is implemented only in PHP. The goal is to provide a few servers for various platforms (PHP, Node, .Net, Python, etc...) and you can help for this!

The server part has only one job: respond to the client. It must provide appropriate headers for the latency tests, configure the platform to allow large uploads and return large chunks of data for the download tests.

If you want to contribute by creating a server in any language or with any platform you want, here's some simple guidelines to help you:

* Don't let HTTP connections opened (disabled them, or use HTTP 1.0, the latest isn't recommended). The latency calculations are based on the time it takes for the client to establish an HTTP connection with the server, if a connection is still open you will get a 0 latency for each test.
* Disable every caching capabilities of client browsers or proxies. Take a look at the `Cache-Control` and `Pragma` headers.
* Disable all types of compression, like __gzip__.

You will need to handle a single endpoint, it must return an empty response with a `200` HTTP code when a `GET` request is made. At this point, latency calculations _should work_. Your server __must__ support binary file uploads on this endpoint, check your environment configuration if you have any issues.

Finally, the endpoint should return generated data when the client measures the download bandwidth. You can identify this scenario by checking if the `module` query parameter is equal to `"download"`.

The easiest way to generate some data is to return a simple string concatenated multiple times to finally get the appropriate size and return it to the client. If you can easily generate some random binary data, prefer this option. You should, by default, return a `20MB` size but the client can override this value by providing a size __in Bytes__ through the `size` query parameter. Since the client can define this value, make sure to define a maximum value, I recommend `200MB`.

That's it for the server part, [check my PHP implementation](server/server.php) if you need some context.
