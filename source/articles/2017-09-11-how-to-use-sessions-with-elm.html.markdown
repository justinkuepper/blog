---

title: How to Use Sessions with Elm
date: 2017-09-11 22:12 UTC
tags:

---

Elm is awesome for writing fast, easy-to-ready, and error-free JavaScript, but there are many cases where you'll need to interact with external things. Fortunately, Elm has a way to do this without introducing any error-ridden code to the application.

In this post, we will look at how to implement sessions with Elm using a Elm's JavaScript Interop.

# JavaScript Interop

Elm is great for writing clean code with no runtime errors, but there are instances when you need to use JavaScript. For example, you might want to use Stripe's JavaScript library to process payments or persist data to the browser using sessions.

You can accomplish these things using Elm ports and flags. Elm can send and receive data through ports that it can then process internally and update the user interface. Flags can be used to initialize an Elm program with external values.

A flag that sets a username might look something like this:

```const app = Elm.App.fullscreen({
  username: 'Bob'
});
```

We will take a look at how to implement a port in the remainder of this blog post to persist authentication tokens to the user's browser.

# Adding a Port

The first step in adding a port to an Elm application is adding the port keyword before the module name when defining the application file.

```port module App exposing (..)
```

The second step is adding two port commands for setting a token and getting a token. Note that you could (and maybe should) get the token using a flag rather than port, but we're going to use a port in this example to show how it's done.

```port setToken : String -> Cmd msg
port getToken : (String -> msg) -> Sub msg
```

The setToken port takes a string and returns a command message to set the token, while the getToken port returns a subscription that listens for a token to be sent.

The third step is creating the subscription that will set the token in the Elm app.

```subscriptions : Model -> Sub Msg
subscriptions model =
  getToken AddToken
```

The AddToken would simply be a message in the update method that adds the token to your model.

# Adding the JavaScript

Now that the Elm code is ready, we will write the JavaScript code in the HTML file where the Elm code is embedded. This code will listen for and send any necessary data to and from Elm.

Here's what that entire section will look like:

```<script type="text/javascript">
  const node = document.getElementById('elm');
  const app = Elm.App.embed(node);
  var currentToken = localStorage.getItem('myToken');
  if (currentToken) {
    app.ports.getToken.send(currentToken);
  }
  app.ports.setToken.subscribe(function(token) {
    localStorage.setItem('myToken', token)
  });
</script>
```

In this code, we initialize an Elm app and set the currentToken to the localStorage value for myToken. If the value is not nil, we use the getToken port to send the value to the Elm app. If the value is nil, we use the setToken port to listen for a token to be sent and then set it to localStorage.

# What's Next

These same basic concepts can be applied to all kinds of JavaScript Interop situations. If you want to get data from JavaScript, you create a port and a subscription using the subscribe method. If you want to send data to JavaScript, you create a port using the send method.
