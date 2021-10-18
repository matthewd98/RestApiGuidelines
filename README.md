## RestApiGuidelines

REST API Guidelines

# 1- Hypermedia driven

RESTful APIs should be hypermedia driven. Clients should be expected to discover other URLs from the resources themselves. They should only be aware of a small number of well-known links. Clients shouldn’t be burdened with building URLs from parameters (usually from the field values in a returned resource representation). This means resource representations should contain URLs to related resources or to perform operations on resources.

It is recommended to include a link to the URL for the resource itself. Links can be grouped together under one field or they can be individual fields in the resource representation. For example:

  {
      "name": "hello world",
      "childrenUrl": "url",
      "_self": "url"
  }

  OR

  {
      "resourceName": "hello world",
      "links": {
          "children": "url",
          "_self": "url"
      }
  }

A well-behaved server should not change URLs for related resources between versions so clients can bookmark them should they wish to. However, a well-behaved client should be able to re-discover a URL from the initial well-known URLs if a bookmarked URL is no longer valid.

# 2- Versioning

Two possible schemes: (1) Media type versioning (+ content negotiation) and (2) URI versioning

Media type versioning (+ content negotiation) should be favored. It allows for independent evolution of server and clients.

Media type names should have the following format: application/vnd.myname.v1+json

The server should use a unique media type for each different representation of a resource. This means that GET and POST operations for the same resource should have different media types as they usually have different representations. PUT operations will generally have the same media type as their GET operation counterparts.

When a client makes a request, they should specify a an accept header for the representation they are expecting to receive back. If the header value isn’t specified, or an inexistant or generic media type is specified, e.g. application/json, then the server can choose which representation to return or can respond with a 406 (Not Acceptable) error code. The server will always respond with a content type header value when it returns a response with a body. Clients are always expected to verify the content type header value before de-serializing the response’s body.

# 3- Exposing CQRS (if applicable)

Queries naturally map to GET while commands need to be mapped to POST, PUT, DELETE and PATCH.

When mapping a command to a HTTP verb, it is recommended to choose the proper verb not just semantically, but also in terms of idempotency. PUT, PATCH and DELETE are idempotent while POST is not.

Here’s an example of a request:

  POST /Entities
  Content-Type: application/vnd.AddEntityCommand.v1+json
  {
      "name": "hello world"
  }

# 4- Optimistic Concurrency Control with Etags 

Servers should always return an ETag in the response to a GET request. If a server supports ETags it must also honor the If-Match header on PUT and PATCH requests. Servers which support ETags should also support If-None-Match for GET or HEAD methods. 
