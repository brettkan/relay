---
id: guides-network-layer
title: Network Layer
layout: docs
category: Guides
permalink: docs/guides-network-layer.html
next: guides-babel-plugin
---

Relay has a network layer abstraction that separates mutations and queries from the actual machinery that sends requests to the GraphQL server. This gives us the flexibility to configure or even completely replace the default network layer via injection.

## Default Network Layer

Relay is pre-configured to use a default network layer that works with [express-graphql](https://github.com/graphql/express-graphql). This default network layer is exposed via `Relay.DefaultNetworkLayer`.

By default, Relay assumes that GraphQL is served at `/graphql` relative to the origin where our application is served. This can be re-configured by injecting a custom instantiation of the default network layer.

```
Relay.injectNetworkLayer(
  new Relay.DefaultNetworkLayer('http://example.com/graphql')
);
```

Underneath the hood, the default network layer uses `fetch`. When it sends queries, it will automatically fail requests after a 15 second timeout. Also, failed requests are automatically retried twice, with a 1 second delay and a 3 second delay, respectively.

Like the GraphQL URI, the timeout and retry behavior can be configured:

```{3-4}
Relay.injectNetworkLayer(
  new Relay.DefaultNetworkLayer('http://example.com/graphql'),
  30000,    // Timeout after 30s.
  [5000]    // Only retry once after a 5s delay.
);
```

Unlike queries, failed requests for mutations are not automatically retried.

## Custom Network Layers

Relay also lets us completely replace the default network layer.

Custom network layers must conform to the following [RelayNetworkLayer](interfaces-relay-network-layer.html) interface. Although the default network layer is an instantiable class that accepts some configuration, this is not a requirement of an injected network layer.

For example, a network layer can be a simple object that conforms to the interface:

```
var myNetworkLayer = {
  sendMutation(mutationRequest) {
    // ...
  },
  sendQueries(queryRequests) {
    // ...
  },
  supports(...options) {
    // ...
  },
};

Relay.injectNetworkLayer(myNetworkLayer);
```

You can read more about the API [RelayNetworkLayer](interfaces-relay-network-layer.html) interface.
