A little (poc) script too help you handle cookie/session over multiple domain.

  * It uses XSS AJAX.
  * It has a basic security layer (nonce).
  * It's full POO and pluggable.
  * It uses an asynchronous JS pattern to put events in a queue.
  * There is no big dependencies.

more infos : [README.txt](http://code.google.com/p/mudoco/source/browse/trunk/mudoco/README.txt)

demo [SiteA](http://sitea.mudoco.davidberlioz.net/testsite/) [SiteB](http://siteb.mudoco.davidberlioz.net/testsite/) [SiteC](http://sitec.mudoco.davidberlioz.net/testsite/)

Set the test cookie in sitea and refresh othe sites...

Compatibility : firefox, chrome, safari, ie8, ie9