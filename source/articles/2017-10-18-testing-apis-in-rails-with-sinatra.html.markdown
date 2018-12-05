---

title: Testing APIs in Rails with Sinatra
date: 2017-10-18 21:19 UTC
tags:

---

Most modern web applications consume one or more APIs, but testing external services can cause a host of problems. For example, connecting to external services can slow down a testing suite; many APIs have rate limits; and, some APIs may not have testing (sandbox) functionality.

There are several different approaches to testing external APIs - ranging from creating fake stubs to recording live responses with VCR - but there's one approach that I have come to favor over the others due to its simplicity, speed, and robustness.

# Stubbing a Request

The most common solution to this problem is to simply stub any API requests. With libraries like WebMock, you can disable external connections and then fake the response for testing purposes. This way, you can pass the tests and ensure that you properly handle the responses.

For example, an RSpec feature might look something like this:

```feature 'Test API Response' do
  it 'runs a get query somewhere' do
    url = URI('https://someapi.com')
    response = Net::HTTP.get(uri)
    expect(response).to include('something')
  end
end
```

We can then stub the request using an RSpec before block:

```config.before(:each) do
  stub_request(:get, '/someapi.com/').
    to_return(status: 200, body: 'something', headers: {})
  end
end
```

The problem with this approach is that stubs for a single external API could be spread out across many tests. If your application relies heavily on an API, this can turn into a nightmare scenario to keep updated if that external API changes.

# Creating Your Own Server

I came across a ThoughtBot [blog post](https://robots.thoughtbot.com/how-to-stub-external-services-in-tests) several years ago that discussed different ways of testing external APIs. One of these methods involved creating a small Sinatra app within a Rails app to essentially recreate an external API. Ever since, I've been using this technique in cases where I heavily use an API.

To set it up, you first need to route all requests to the Sinatra app via Rack in Rspec.config:

```config.before(:each) do
  stub_request(:any, /someapi.com/).to_rack(FakeApi)
end
```

You can then create a basic Sinatra app to deliver the responses:

```require 'sinatra/base'
class FakeApi < Sinatra::Base
  get '/some/route/here' do
    json_response 200, 'endpoint.json'
  end
  private
  def json_response(response_code, file_name)
    content_type :json
    status response_code
    File.open(File.dirname(__FILE__) + '/fixtures/' + file_name, 'rb')
  end
end
```

I usually keep this file in /rspec/support/. Finally, you can copy-and-paste a JSON response from the API documentation and put it into the /rspec/fixtures/ folder. You can then handle these JSON responses in your tests, knowing that it's an accurate response.

The benefit of this approach is that you can simply copy-and-paste any changes to the API into files and everything involved with the API is held in a single file that contains the Sinatra app.

# Next Steps

There are many different ways to test external APIs and there are no one-solution-fits-all approaches. That said, developing a simple Sinatra app is a great way to keep code organized and easily copy-and-paste responses from an API's documentation to ensure accuracy.
