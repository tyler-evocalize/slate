# Errors

We always attempt to return the appropriate HTTP Status code when we return an error. Additionally we do our best to provide a human readable error message to help unblock you.


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is malformed or invalid.
401 | Unauthorized -- Request signature is invalid, expired or you are not registed in our system.
404 | Not Found -- The specified resource could not be found.
500 | Internal Server Error -- We had a problem with our server. Try again later.
