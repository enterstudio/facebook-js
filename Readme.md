# facebook-js

Minimalistic facebook API client

``` bash
npm install facebook-js
```

## Usage

facebook-js has just three methods.

  * `getAuthorizeUrl(options)`: Returns the authorize url.
  * `getAccessToken(key, secret, code, redirect_uri, callback[error, access_token, refresh_token])`: Uses oAuth module to callback the access_token.
  * `apiCall(http_method, path, params, callback[error, response, body])`: Does a call to facebook graph API and callbacks when its done.

## Example using express.js

``` javascript
var express = require('express')
  , fb = require('facebook-js')
  , app = express.createServer(
      express.bodyParser()
    , express.cookieParser()
    , express.session({ secret: 'some secret' })
    );

app.get('/', function (req, res) {
  res.redirect(fb.getAuthorizeUrl({
    client_id: 'appID',
    redirect_uri: 'http://yourhost.com:3003/auth',
    scope: 'offline_access,publish_stream'
  }));
});

app.get('/auth', function (req, res) {
  fb.getAccessToken('appID', 'appSecret', req.param('code'), 'http://yourhost.com:3003/auth'}, function (error, access_token, refresh_token) {
    res.render('client', {access_token: access_token, refresh_token: refresh_token});
  });
});

app.post('/message', function (req, res) {
  fb.apiCall('POST', '/me/feed',
    {access_token: req.param('access_token'), message: req.param('message')},
    function (error, response, body) {
      res.render('done', {body: body});
    }
  );
});

app.get('/messages', function (req, res) {
  var stream = fb.apiCall('GET', '/me/feed', {access_token: req.param('access_token'), message: req.param('message')});
  stream.pipe(fs.createWriteStream('backup_feed.txt'));
});

app.listen(3000);
```

## Test

To test and see this module working:

``` bash
make
```

## License

(The MIT License)

Copyright (c) 2010-2011 Pau Ramon masylum@gmail.com

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
