---
layout: post
title: "Flask session cookie manager"
description: "Session cookie decoder/encoder for flask"
modified: 2014-08-03 16:14:09 -0700
tags: [flask, python, session cookie]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

What is a good way to test a flask web application routes/page that requires a user to login first? In my opinion there is two ways of doing it.

1. do a `setup` and `teardown` where you login and logout after each test
2. Inject `session cookie` for all request (This will help speed up the test suite you have, because you don't have to set the login/logout at the beginning and the end of test)

Below is a simple python script to deal with Flask session cookie

What you will need the:

1. flask app secret key (for encoding)
2. the session cookie structure/data for (decoding/encoding)

How to use the python script:

1. Use the `session_cookie_decoder` to get session cookie data/structure
`python session_cookie_manager.py <decode> <session_cookie_value>`
2. Use the `session_cookie_encoder` to setup a stub/mock `session cookie data`
`python session_cookie_manager.py <encode> <secret_key> <session_cookie_structure>`

`session_cookie_manager.py`

{% highlight python %}
""" Flask Session Cookie Decoder/Encoder """
__author__ = 'Wilson Sumanang'

# standard imports
import sys
import zlib
from itsdangerous import base64_decode

# external Imports
from flask.sessions import SecureCookieSessionInterface


class MockApp(object):

    def __init__(self, secret_key):
        self.secret_key = secret_key


def session_cookie_encoder(secret_key, session_cookie_structure):
    """ Encode a Flask session cookie

    Example:
        cookie_structure = dict(
        	gplus_id     = 1285135705050360459231,
            email        = john.doe@gmail.com,
            user_info    = dict(
                            full_name = john doe,
                        )
        )
        session_cookie_encoder(b'development key', cookie_structure)

    Args:
        secret_key (string): Flask App secret key
        session_cookie_structure (dict): Flask session cookie structure

    Return:
        value (string): Flask session cookie
    """
    try:
        app = MockApp(secret_key)

        session_cookie_structure = dict()
        si = SecureCookieSessionInterface()
        s = si.get_signing_serializer(app)

        return s.dumps(session_cookie_structure)
    except Exception as e:
        return "[Encoding error]{}".format(e)


def session_cookie_decoder(session_cookie_value):
    """ Decode a Flask cookie

    Args:
        session_cookie_value (string): Flask session cookie to decode
    """
    try:
        compressed = False
        payload = session_cookie_values

        if payload.startswith(b'.'):
            compressed = True
            payload = payload[1:]

        data = payload.split(".")[0]

        data = base64_decode(data)
        if compressed:
            data = zlib.decompress(data)

        return data
    except Exception as e:
        return "[Decoding error]{}".format(e)


if __name__ == "__main__":
    encoder_guide = "Usage: session_cookie_manager.py <encode> <secret_key> <session_cookie_structure>"
    decoder_guide = "Usage: session_cookie_manager.py <decode> <session_cookie_value>"
    print sys.argv
    if len(sys.argv) == 3:
        status, session_cookie_value = sys.argv[1:]
        if status.lower() == 'decode':
            print session_cookie_decoder(session_cookie_value)
        else:
            print decoder_guide
            sys.exit(1)
    elif len(sys.argv) == 4:
        status, secret_key, session_cookie_structure = sys.argv[1:]
        if status.lower() == 'encode':
            print session_cookie_encoder(secret_key, session_cookie_structure)
            sys.exit(1)
        else:
            print encoder_guide
    else:
        print decoder_guide
        print encoder_guide
        sys.exit(1)

{% endhighlight %}


By default Flask uses URL-safe signed serializer called itsdangerous to encoded its client-side session cookie. A flask app uses a secret key to sign the session cookie so that the client can't modify it.[^1]

[^1]: <https://www.kirsle.net/wizards/flask-session.py>
