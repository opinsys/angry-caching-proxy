# Angry Caching Proxy

Make package downloads lightning fast for apt-get, npm and Rubygems!

Angry Caching Proxy is forwarding proxy for various package managers. It is
configured transparently to the clients using the `http_proxy` environment
variable. So there is no need to have customized sources.list, Gemfile etc.

It works by doing very angry caching (forever!) for the actual package files.
This should be ok as long as the package authors won't override already
uploaded packages. It is a very bad practice by the package authors and happens
very rarely. If it happens anyway you can always clear the cache from the web
interface of Angry Caching Proxy.


## Install

Get node.js and type

    sudo npm install -g angry-caching-proxy

or if you don't like sudoing random code you can install it locally too:

    npm install angry-caching-proxy

and execute it with

    node_modules/.bin/angry-caching-proxy

## Configuration

By default Angry Caching Proxy caches apt-get, npm and rubygems downloads. If
you want to add additional caching or disable build in cahces you can create
`/etc/angry-caching-proxy/triggers.js` file with your own caching function
triggers. It should export an object of functions that return `true` when the
request should be cached. The caching occurs only if the upstream responds with
http success status 200.

Example:

```javascript
module.exports = {
    "custom": function isMyCustomCacheRequest(req, res) {
        // Cache all requests that contain X-My-Cache header
        return req.headers["X-My-Cache"]);
    },

    // disable buildin npm caching
    "npm": null

};

```

See
[defaulttriggers.js](https://github.com/epeli/angry-caching-proxy/blob/master/defaulttriggers.js)
for additional examples.


## Usage

Create directory where to save cached requests

    mkdir cache

and start the server

    angry-caching-proxy --directory cache

You can inspect and clear the cache by browsing directly to the proxy address
<http://localhost:8000>

### apt-get

    http_proxy=http://localhost:8000 sudo -E apt-get install sl

### Bundler (Rubygems)

    http_proxy=http://localhost:8000 bundle install

### npm

With npm it is required to use the non-https version of the registry

    http_proxy=http://localhost:8000 npm install --registry http://registry.npmjs.org/


Cool guys can also set the proxy globally for everybody:

    export http_proxy=http://localhost:8000

