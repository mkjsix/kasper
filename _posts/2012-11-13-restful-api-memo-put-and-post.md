---
layout: post
title: "RESTful API memo: PUT and POST differences"
date: 2012-11-13T11:38:00+01:00
comments: true
sharing: true
redirect_from:
 - /2012/11/restful-api-memo-put-and-post.html
categories:
 - technology
---

Before start designing a RESTful API, have a look at [Hypertext Transfer Protocol – HTTP/1.1, section 9](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#)

> The POST method is used to request that the origin server accept the entity enclosed in the request as a new subordinate of the resource identified by the Request-URI in the Request-Line.

In other terms, `POST` is meant to handle appends to existing resources or incremental creations of subordinate resources

> The actual function performed by the POST method is determined by the server and is usually dependent on the Request-URI. The posted entity is subordinate to that URI in the same way that a file is subordinate to a directory containing it, a news article is subordinate to a newsgroup to which it is posted, or a record is subordinate to a database.

`PUT` instead seems is more appropriate to handle one-shot creations, creating or replacing an entire resource in one single transaction

> The PUT method requests that the enclosed entity be stored under the supplied Request-URI. If the Request-URI refers to an already existing resource, the enclosed entity SHOULD be considered as a modified version of the one residing on the origin server. If the Request-URI does not point to an existing resource, and that URI is capable of being defined as a new resource by the requesting user agent, the origin server can create the resource with that URI.

### Differences between PUT and POST ###

> The fundamental difference between the POST and PUT requests is reflected in the different meaning of the Request-URI. The URI in a POST request identifies the resource that will handle the enclosed entity. That resource might be a data-accepting process, a gateway to some other protocol, or a separate entity that accepts annotations. In contrast, the URI in a PUT request identifies the entity enclosed with the request – the user agent knows what URI is intended and the server MUST NOT attempt to apply the request to some other resource.

Another remarkable difference is that `PUT` requests are required to be __idempotent__, while `POST` requests aren't

> Methods can also have the property of ‘idempotence’ in that (aside from error or expiration issues) the side-effects of N > 0 identical requests is the same as for a single request. The methods GET, HEAD, PUT and DELETE share this property. Also, the methods OPTIONS and TRACE SHOULD NOT have side effects, and so are inherently idempotent.
