---
title: Requests
layout: post
description: Requests: HTTP for Humans
---

Hello, world!

> [Requests](https://github.com/kennethreitz/requests): HTTP for Humans



## Installation

```python
pip3 install requests
```



## Make a Request

```python
>>> import requests

>>> r = requests.get('https://api.github.com/events') # GET
>>> r = requests.post('http://httpbin.org/post', data = {'key':'value'}) # POST
>>> r = requests.put('http://httpbin.org/put', data = {'key':'value'}) # PUT
>>> r = requests.delete('http://httpbin.org/delete') # Delete
>>> r = requests.head('http://httpbin.org/get') # HEAD
>>> r = requests.options('http://httpbin.org/get') # OPTIONS
```



## Passing Parameters In URLs

Requests allows you to provide these arguments as a dictionary, using the params keyword argument

```python
>>> payload = {'key1': 'value1', 'key2': 'value2'}
>>> r = requests.get('http://httpbin.org/get', params=payload)

>>> print(r.url)
http://httpbin.org/get?key2=value2&key1=value1
    
>>> payload = {'key1': 'value1', 'key2': ['value2', 'value3']}

>>> r = requests.get('http://httpbin.org/get', params=payload)
>>> print(r.url)
http://httpbin.org/get?key1=value1&key2=value2&key2=value3
```



## Response Content

```python
>>> r = requests.get('https://api.github.com/events')
>>> r.text
u'[{"repository":{"open_issues":0,"url":"https://github.com/...
```



## Binary Response Content

```python
>>> r.content
b'[{"repository":{"open_issues":0,"url":"https://github.com/...
```



## JSON Response Content

```python
>>> r = requests.get('https://api.github.com/events')
>>> r.json()
[{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...
```



## Raw Response Content

```python
>>> r = requests.get('https://api.github.com/events', stream=True)

>>> r.raw
<requests.packages.urllib3.response.HTTPResponse object at 0x101194810>

>>> r.raw.read(10)
'\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'
```



## Custom Headers

```python
>>> url = 'https://api.github.com/some/endpoint'
>>> headers = {'user-agent': 'my-app/0.0.1'}

>>> r = requests.get(url, headers=headers)
```



## More complicated POST requests

```python
>>> payload = {'key1': 'value1', 'key2': 'value2'}

>>> r = requests.post("http://httpbin.org/post", data=payload)
>>> print(r.text)
{
  ...
  "form": {
    "key2": "value2",
    "key1": "value1"
  },
  ...
}
```

There are many times that you want to send data that is not form-encoded. If you pass in a string instead of a dict, that data will be posted directly

```python
>>> import json

>>> url = 'https://api.github.com/some/endpoint'
>>> payload = {'some': 'data'}

>>> r = requests.post(url, data=json.dumps(payload))

>>> url = 'https://api.github.com/some/endpoint'
>>> payload = {'some': 'data'}

>>> r = requests.post(url, json=payload)
```



## POST a Multipart-Encoded File

```python
>>> url = 'http://httpbin.org/post'
>>> files = {'file': open('report.xls', 'rb')}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "<censored...binary...data>"
  },
  ...
}

# You can set the filename, content_type and headers explicitly:
>>> url = 'http://httpbin.org/post'
>>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "<censored...binary...data>"
  },
  ...
}
```

If you want, you can send strings to be received as files:

```python
>>> url = 'http://httpbin.org/post'
>>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "some,data,to,send\\nanother,row,to,send\\n"
  },
  ...
}
```



## Response Status Codes

```python
>>> r = requests.get('http://httpbin.org/get')
>>> r.status_code
200

>>> r.status_code == requests.codes.ol
True
```



## Response Headers

```python
>>> r.headers
{
    'content-encoding': 'gzip',
    'transfer-encoding': 'chunked',
    'connection': 'close',
    'server': 'nginx/1.0.4',
    'x-runtime': '148ms',
    'etag': '"e1ca502697e5c9317743dc078f67693f"',
    'content-type': 'application/json'
}
```



## Cookies

If a response contains some Cookies, you can quickly access them:

```python
>>> url = 'http://example.com/some/cookie/setting/url'
>>> r = requests.get(url)

>>> r.cookies['example_cookie_name']
'example_cookie_value'
```

To send your own cookies to the server, you can use the cookies parameter:

```python
>>> url = 'http://httpbin.org/cookies'
>>> cookies = dict(cookies_are='working')

>>> r = requests.get(url, cookies=cookies)
>>> r.text
'{"cookies": {"cookies_are": "working"}}'

>>> jar = requests.cookies.RequestsCookieJar()
>>> jar.set('tasty_cookie', 'yum', site='httpbin.org', path='/cookies')
>>> jar.set('gross_cookie', 'blech', site='httpbin.org', path='/elsewhere')
>>> url = 'http://httpbin.org/cookies'
>>> r = requests.get(url, cookies=jar)
>>> r.text
'{"cookies": {"tasty_cookie": "yum"}}'
```



## Redirection and History

```python
>>> r = requests.get('http://github.com')

>>> r.url
'https://github.com/'

>>> r.status_code
200

>>> r.history
[<Response [301]>]

>>> r = requests.get('http://github.com', allow_redirects=False)

>>> r.status_code
301

>>> r.history
[]

>>> r = requests.head('http://github.com', allow_redirects=True)

>>> r.url
'https://github.com/'

>>> r.history
[<Response [301]>]
```



## Timeouts

```python
>>> requests.get('http://github.com', timeout=0.001)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)
```



## Errors and Exceptions

In the event of a network problem (e.g. DNS failure, refused connection, etc), Requests will raise a ConnectionError exception.

Response.raise_for_status() will raise an HTTPError if the HTTP request returned an unsuccessful status code.

If a request times out, a Timeout exception is raised.

If a request exceeds the configured number of maximum redirections, a TooManyRedirects exception is raised.

All exceptions that Requests explicitly raises inherit from requests.exceptions.RequestException.