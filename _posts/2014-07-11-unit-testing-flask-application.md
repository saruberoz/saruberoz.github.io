---
layout: post
title: "Unit Testing Flask Application"
modified: 2014-07-11 23:43:30 -0700
tags: [unit-test, flask, python]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

# How to unit-testing Python Flask App

Lets say you have some production code which you want to test, you might wonder what is a good way to test it.

Here is some example how I have been doing it, hopefully it will be useful (ps. This is just one of many ways to do it)

Say you have some REST API routes

`main.py`

{% highlight python %}
@app.route('/environment', method=['GET'])
def get_current_environment():
 	''' Get current application environment '''
 	return current_app.get('environment')

@app.route('/environment/<type>', method=['POST'])
def log_message(type):
	''' Log message in the requested level
	@param type: log level
	'''
	if type not in ('error', 'warn', 'info', 'debug', 'trace'):
		return (False, 'Not a valid log type')
	param = request.json()
	message = param	.get('message')
	if not message:
		return (False, 'Invalid payload given')
	return (True, msg)

@app.route('/sum/', method=['PUT'])
def sum_data():
	''' Load data from text file and print the sum of all number '''
	# Assuming it is a valid file (to make the guidance short and clear)
	file_data = request.form.get('file', None)
	sum = 0
	if not file_data:
		return sum
	# do some parsing of the file
	return sum
{% endhighlight %}

Then to test the Flask routes you can either use:

1. **Using Flask `url_for` method to let it to decide which function to use.**
   *(Reason: Maybe routes will change but function name don't change, which in my opinion is bad design because for API, route usually dont change a lot of times.*
2. **Using actual routes.**
   *(Reason: Most of the time function name will get renamed but routes stay the same)*


`test_main.py`

{% highlight python %}
import unittest
from flask import url_for

class TestMain(unittest.TestCase):
    def test_get_current_environment(self):
		resp = self.client.get(url_for('<module_name>.get_current_environment'))
		# or you can do it this way
		resp = self.client.get('/environment')
		assert resp.data == <app env>, msg='<some debug msg, know why/where>'

	def test_log_message(self):
	    payload = <some_payload_here>
	    resp = self.client.post(url_for('<module_name>.log_message', level='debug'),
	                            data=json.dumps(payload), content_type='application/json')
	    # or you can do it this way too (its your option which one is better)
	    resp = self.client.post('/environment/debug', data=json.dumps(payload),
	                            content_type='application/json')
	    self.assertEqual(resp.data, <valid resp data>

	def test_sum_data(self):
		with open('count_sum.txt', 'r') as file:
			payload = {'file': file}
			resp = self.client.put('/sum', data=payload)
			# some assertion
{% endhighlight %}


You can do assertion however you want, I recommend using built in [python unittest framework](https://docs.python.org/2/library/unittest.html).


Note that to be able to use flask `url_for` you have to activate the [request context](http://flask.pocoo.org/docs/testing/#other-testing-tricks) which basically you can set with `self.app.test_request_context()`


Lets go to another case when you wanted to modify flask `session` to speed up testing.
Mocking session cookie data is one good way of doing it where the session cookie might store something like `user_id` for authorization checking,
you can access and modify flask session as shown [here](http://flask.pocoo.org/docs/testing/#accessing-and-modifying-sessions)

{% highlight python %}
with app.test_client() as c:
    with c.session_transaction() as sess:
        sess['a_key'] = 'a value'

    # once this is reached the session was stored
{% endhighlight %}


Other Guide that I find useful for doing unit-test using mock

- [Introduction to mocking](http://www.toptal.com/python/an-introduction-to-mocking-in-python#.)
- [More mocking article](http://www.drdobbs.com/testing/using-mocks-in-python/240168251?pgno=1)
