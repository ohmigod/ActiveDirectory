---
description: RESTful API Vulnerabilities
---

# REST

> A RESTful API is an architectural style for an application program interface (API) that uses HTTP requests to access and use data. That data can be used to GET, PUT, POST and DELETE data types, which refers to the reading, updating, creating and deleting of operations concerning resources.

### Checklist

* [ ] Change content-type from json to XML. Then try [XXE](../xxe.md).
* [ ] Find the API definition /xxx/?wadl&#x20;
* [ ] Try accessing /version.txt, /api/version.txt, /api/v1/version.txt...
* [ ] Find GUIs like swagger-ui or others&#x20;
* [ ] Add the \[] instead of a value (may trigger stack traces): /api/v1/books/\[]&#x20;
* [ ] Read the docs if it is an open source API. Might contain endpoints to perform OS execution, file read...&#x20;