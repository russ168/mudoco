MuDoCo - A Multi Domain Cookie PHP Script

A little (poc) script too help you handle cookie/session over multiple domain.

It uses XSS AJAX.
It has a basic security layer (nonce).
It's full POO and pluggable.
It uses an asynchronous JS pattern to put events in a queue.
There is no big dependencies.


** Source tree **

- includes/ : MuDoCo classes
- mudoco/server/ : the central server to hold cookie/session
- mudoco/client/ : some dropin files to put on the client website server
- testsite/ : a basic test site
- plugins/ : a dummy plugin


** Howto testsite **

Requirements for default config :

- session
- allow_url_fopen 1 (php.ini)
- sqlite3 (to hold nonce database)

Install :

* setup mudoco server : cookie.loc
 - setup /etc/hosts, vhosts, etc (or something else) to have something up and running like :
   - http://cookie.loc/mudoco/server
 - copy and adapt mudoco/server/etc/config-dist.php into mudoco/server/etc/config.php
 NB : mudoco/server/restricted should be restricted to site access only !
 
 * setup 2 client sites : sitea.loc and siteb.loc.
 - setup /etc/hosts, vhosts, etc (or something else) to have something up and running like :
   - http://sitea.loc/testsite
   - http://sitea.loc/mudoco/client/cb.php
   - http://siteb.loc/testsite
   - http://siteb.loc/mudoco/client/cb.php
 - copy and adapt mudoco/client/etc/config-dist.php into mudoco/client/etc/config.php
 - copy and adapt testsite/etc/config-dist.php into testsite/etc/config.php
 
 Test :
 
 launch http://sitea.loc/testsite :
 - hello link should answer : World + date.
 - foo = : you can set a value in sitea.loc and refresh siteb.loc
 
 See testsite/index.php !
 
 
 ** Config **
 
 Client side :
 - $mudoco_conf['MUDOCO_CLIENT_SALT']
 - $mudoco_conf['MUDOCO_SERVER_BASE'] (mandatory) URL to MuDoCo server
 - $mudoco_conf['MUDOCO_CLIENT_COOKIENAME'] default is 'MDCL'
 
 Server side :
 - $mudoco_conf['MUDOCO_STORAGE_SESSION_SALT']
 - $mudoco_conf['MUDOCO_STORAGE_NONCE_SALT']
 - $mudoco_conf['MUDOCO_PLUGIN_SESSION_STORAGE_CLASS'] default 'MuDoCo_Storage_Session_Default'
 - $mudoco_conf['MUDOCO_STORAGE_SESSION_COOKIENAME'] default is 'MDCID'
 - $mudoco_conf['MUDOCO_STORAGE_SESSION_LIFETIME'] default is 365*24*3600
 - $mudoco_conf['MUDOCO_SERVER_NONCE_STORAGE_CLASS'] default 'MuDoCo_Storage_Nonce_Sqlite'
 - $mudoco_conf['MUDOCO_STORAGE_NONCE_SQLITE_FILE'] (mandatory) path to rhe sqlite3 file (will be created)
 - $mudoco_conf['MUDOCO_SERVER_PLUGINS_DIR'] path to external plugins
 - $mudoco_conf['MUDOCO_SERVER_INIT'] list of plugins for init() function.
 
 
 ** Plugins **
 
You can extend MuDoCo features using plugins.
The session feature is already a plugin (see includes/MuDoCo/Plugin/Session.php).

 A plugin has 2 sides :

 - a PHP class implementing MuDoCo_Plugin_Interface (see includes/MuDoCo/Plugin/Interface.php) :
   - you have to implement the main query() function (see plugins/Hello.php)

 - a JS callback to react in the navigator (see testsite/index.php).
   function(mode, params, success, error)
    - mode [string] run, success or error
    - params [mixed]
      - in run mode : the params to send in the xss call
      - in success mode : the data returned by the PHP plugin
    - success : JS callback, takes 1 param (the data returned by the PHP plugin)
    - error JS callback
 
 You can push the query in the queue : _mdcq.push({query: 'hello', ... })
 You can trigger the query : MuDoCo.me().query('hello')
 
 
 ** Interface / Customization **
 You can provide your own class.
 
 - MuDoCo_Plugin_Interface : see plugins
 - MuDoCo_Storage_Nonce_Interface : provide your own nonce database
 - MuDoCo_Storage_Session_Interface : provide your own session handler
 - MuDoCo_Client::httpRequest() : modify the way http request are done
 
 
 ** Security **
 
 To make it simple :
 
 - the client site sitea.loc calls cb.php wich retrieve a nonce from mudoco server (in a server to server http request).
 - This nonce is put in a cookie in sitea.loc
 - mdc.js uses this nonce to authenticate xss calls in the navigator
 - when the nonce is too old MuDoCo gets a new one
 
 In fact it's a bit more complex because we use a client nonce to get a hashed nonce => md5(cnonce+nonce)...
 
 
 author : berliozdavid@gmail.com
 