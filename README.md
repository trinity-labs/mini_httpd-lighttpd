<div align="center">
<img src="https://github.com/trinity-labs/trinity-skin/assets/45216746/9a759ca2-6133-4325-ae2d-0f90a443ec98" width="40%">
</div>
<br>
<br>
<h2>

**Run Alpine Linux ACF Dashboard App on Lighttpd**

 </h2>
 
Light-weight, fast and highly customizable server - Perfect for a strong `ACF` backend and keep `Alpine ACF` philosophy to split OS side : 
- Front end servers like `nginx`, `apache` or `docker app`
- Webmin server like `Alpine ACF`

<h2> 

**[Lighttpd Features](https://en.wikipedia.org/wiki/Lighttpd#Features)**

</h2> 

- **Load balancing, `CGI`, `FastCGI`, `SCGI`, `HTTP proxy`, `Servlet AJP`, `WebSocket Tunnel` support**
- `chroot` support
- Web server event mechanism performance – `select()`, `poll()`, and `epoll()`
- Support for more efficient event notification schemes like `kqueue` and `epoll`
- Conditional URL rewriting `(mod_rewrite)`
- **TLS/SSL with SNI support, via` OpenSSL`, `GnuTLS`, `Mbed TLS`, `NSS`, `WolfSSL`.**
- Authentication against an `LDAP` or `DBI` server
- `RRDtool` statistics
- Rule-based downloading with possibility of a script handling only authentication
- Server Side Includes support (but not server-side `CGI` from `SSI`)
- Flexible virtual hosting
- Modules support
- **`Lua` programming language scripts via mod_magnet**
- `WebDAV` support
- `HTTP` compression using mod_deflate (`zlib`, `brotli`, `zstd`)
- **Light-weight (less than 1 MB)**
- Single-process design with only several threads. No processes or threads started per connection.
- `HTTP/2` support since lighttpd 1.4.56
- `HTTP/2` WebSocket support since lighttpd 1.4.65<br>
*<sub>(bold for ACF requirement Lua is ACF native but optional)</sub>*
 
 <h2>

 **Install & Config**

 </h2>
 
 **Assuming your server key is under `/etc/lighttpd/ssl/server.pem`**
 
 Install `lighttpd`
 
 ```bash
  $: apk add lighttpd
  ```
  
 Stop `mini_httpd` (take off your ACF runtimes before stop it)
 
 ```bash
  $: rc-service mini_httpd stop
  ```
  
 Edit `/etc/lighttpd/lighttpd.conf` and replace with all these line
 
 ```bash
###############################################################################
# Default lighttpd.conf for Gentoo.
# $Header: /var/cvsroot/gentoo-x86/www-servers/lighttpd/files/conf/lighttpd.conf,v 1.3 2005/09/01 14:22:35 ka0ttic Exp $
###############################################################################

# {{{ variables
var.basedir  = "/var/www/localhost"
var.logdir   = "/var/log/lighttpd"
var.statedir = "/var/lib/lighttpd"
# }}}

# {{{ modules
# At the very least, mod_access and mod_accesslog should be enabled.
# All other modules should only be loaded if necessary.
# NOTE: the order of modules is important.
server.modules = (
	 "mod_openssl",
#    "mod_rewrite",
#    "mod_redirect",
#    "mod_alias",
	 "mod_access",
#	 "mod_dirlisting",
#    "mod_auth",
#    "mod_status",
	 "mod_setenv",
#    "mod_proxy",
#    "mod_simple_vhost",
#    "mod_evhost",
#    "mod_userdir",
     "mod_deflate",
#    "mod_ssi",
     "mod_expire",
#    "mod_rrdtool",
#    "mod_webdav",
     "mod_accesslog",
)
# }}}

# {{{ includes
include "mime-types.conf"
# uncomment for cgi support
include "mod_cgi.conf"
# uncomment for php/fastcgi support
#   include "mod_fastcgi.conf"
# uncomment for php/fastcgi fpm support
#   include "mod_fastcgi_fpm.conf"
# }}}

# {{{ server settings
server.username      = "lighttpd"
server.groupname     = "lighttpd"

server.document-root = var.basedir + "/htdocs"
server.pid-file      = "/run/lighttpd.pid"

server.errorlog      = var.logdir  + "/error.log"
# log errors to syslog instead
#   server.errorlog-use-syslog = "enable"

index-file.names     = ("index.html")

# Hide server name & version
server.tag           = ""

# event handler (defaults to OS-specific event framework)
# see performance.txt
# 
# for >= linux-2.6
#   server.event-handler = "linux-sysepoll"
# for FreeBSD
#   server.event-handler = "freebsd-kqueue"

# chroot to directory (defaults to no chroot)
# server.chroot      = "/"

# bind to port (defaults to 80)
server.port          = 443

# bind to name (defaults to all interfaces)
# server.bind          = "grisu.home.kneschke.de"

# error-handler for status 404
# server.error-handler-404 = "/error-handler.html"
# server.error-handler-404 = "/error-handler.php"

# Format: <errorfile-prefix><status-code>.html
# -> ..../status-404.html for 'File not found'
# server.errorfile-prefix    = var.basedir + "/error/status-"

# support for caching stat() calls (default "simple")
# for Linux
#   server.stat-cache-engine = "inotify"
# for FreeBSD
#   server.stat-cache-engine = "kqueue"
# }}}

# {{{ mod_staticfile

# which extensions should not be handled via static-file transfer
# (extensions that are usually handled by mod_cgi, mod_fastcgi, etc).
static-file.exclude-extensions = (".php", ".pl", ".cgi", ".fcgi")
# }}}

# {{{ mod_accesslog
accesslog.filename   = var.logdir + "/access.log"
# }}}

# {{{ mod_dirlisting
# enable directory listings
#dir-listing.activate = "disable"
#
# don't list hidden files/directories
#   dir-listing.hide-dotfiles = "enable"
#
# use a different css for directory listings
#   dir-listing.external-css  = "/path/to/dir-listing.css"
#
# list of regular expressions.  files that match any of the
# specified regular expressions will be excluded from directory
# listings.
#   dir-listing.exclude = ("^\.", "~$")
# }}}

# {{{ mod_access
# see access.txt

url.access-deny = ("~", ".inc")
# }}}

# {{{ mod_userdir
# see userdir.txt
#
# userdir.path = "public_html"
# userdir.exclude-user = ("root")
# }}}

# {{{ mod_ssi
# see ssi.txt
#
# ssi.extension = (".shtml")
# }}}

# {{{ mod_ssl
# see ssl.txt
#
ssl.engine    = "enable"
ssl.pemfile   = "/etc/lighttpd/ssl/server.pem"
# }}}

# {{{ mod_status
# see status.txt
#
# status.status-url  = "/server-status"
# status.config-url  = "/server-config"
# }}}

# {{{ mod_simple_vhost
# see simple-vhost.txt
#
#  If you want name-based virtual hosting add the next three settings and load
#  mod_simple_vhost
#
# document-root =
#   virtual-server-root + virtual-server-default-host + virtual-server-docroot
# or
#   virtual-server-root + http-host + virtual-server-docroot
#
# simple-vhost.server-root   = "/home/weigon/wwwroot/servers/"
# simple-vhost.default-host  = "grisu.home.kneschke.de"
# simple-vhost.document-root = "/pages/"
# }}}

# {{{ mod_deflate
# see compress.txt
#
deflate.cache-dir   = "/etc/lighttpd/cache/acf"
deflate.mimetypes = ("text/html", "text/plain", "text/css", "text/xml", "text/javascript", "application/css")
deflate.allowed-encodings = ( "bzip2", "gzip", "deflate" ) # "bzip2" and "zstd" also supported
# }}}

# {{{ mod_proxy
# see proxy.txt
#
# proxy.server               = ( ".php" =>
#                               ( "localhost" =>
#                                 (
#                                   "host" => "192.168.0.101",
#                                   "port" => 80
#                                 )
#                               )
#                             )
# }}}

# {{{ mod_auth
# see authentication.txt
#
# auth.backend               = "plain"
# auth.backend.plain.userfile = "lighttpd.user"
# auth.backend.plain.groupfile = "lighttpd.group"

# auth.backend.ldap.hostname = "localhost"
# auth.backend.ldap.base-dn  = "dc=my-domain,dc=com"
# auth.backend.ldap.filter   = "(uid=$)"

# auth.require               = ( "/server-status" =>
#                               (
#                                 "method"  => "digest",
#                                 "realm"   => "download archiv",
#                                 "require" => "user=jan"
#                               ),
#                               "/server-info" =>
#                               (
#                                 "method"  => "digest",
#                                 "realm"   => "download archiv",
#                                 "require" => "valid-user"
#                               )
#                             )
# }}}

# {{{ mod_rewrite
# see rewrite.txt
#
# url.rewrite = (
#	"^/$"		=>		"/server-status"
# )
# }}}

# {{{ mod_redirect
# see redirect.txt
#
# url.redirect = (
#	"^/wishlist/(.+)"		=>		"http://www.123.org/$1"
# )
# }}}

# {{{ mod_evhost
# define a pattern for the host url finding
# %% => % sign
# %0 => domain name + tld
# %1 => tld
# %2 => domain name without tld
# %3 => subdomain 1 name
# %4 => subdomain 2 name
#
# evhost.path-pattern        = "/home/storage/dev/www/%3/htdocs/"
# }}}

# {{{ mod_expire
# expire.url = (
#	"/buggy/"		=>		"access 2 hours",
#	"/asdhas/"		=>		"access plus 1 seconds 2 minutes"
# )
# }}}

# {{{ mod_rrdtool
# see rrdtool.txt
#
# rrdtool.binary  = "/usr/bin/rrdtool"
# rrdtool.db-name = var.statedir + "/lighttpd.rrd"
# }}}

# {{{ mod_setenv
# see setenv.txt
# setenv.add-request-header  = ( "TRAV_ENV" => "mysql://user@host/db" )
# setenv.add-response-header = ( "X-Secret-Message" => "42" )
# }}}

# {{{ mod_webdav
# see webdav.txt
#
# $HTTP["url"] =~ "^/dav($|/)" {
#     webdav.activate = "enable"
#     webdav.is-readonly = "enable"
# }
# }}}

# {{{ extra rules
#
# set Content-Encoding and reset Content-Type for browsers that
# support decompressing on-thy-fly (requires mod_setenv)
 $HTTP["url"] =~ "\.gz$" {
     setenv.add-response-header = ("Content-Encoding" => "x-gzip")
     mimetype.assign = (".gz" => "text/plain")
 }

 $HTTP["url"] =~ "\.bz2$" {
     setenv.add-response-header = ("Content-Encoding" => "x-bzip2")
     mimetype.assign = (".bz2" => "text/plain")
 }
# }}}

# {{{ debug
# debug.log-request-header   = "enable"
# debug.log-response-header  = "enable"
# debug.log-request-handling = "enable"
# debug.log-file-not-found   = "enable"
# }}}

# vim: set ft=conf foldmethod=marker et :

########################################
#			CUSTOM CONFIG			   
########################################

# Cache based on suffix
$HTTP["url"] =~ "\.(jpg|png|css|js|html)$" {
     expire.url = ( "" => "access plus 1 years" )
}

# Allow only local IP
$HTTP["remoteip"] != "192.168.1.0/24" {
  url.access-deny = ("")
}
  ```
  
 Enable CGI script module and replace this line in `/etc/lighttpd/mod_cgi.conf`
  
  ```bash
###############################################################################
# mod_cgi.conf
# include'd by lighttpd.conf.
# $Header: /var/cvsroot/gentoo-x86/www-servers/lighttpd/files/conf/mod_cgi.conf,v 1.1 2005/08/27 12:36:13 ka0ttic Exp $
###############################################################################

#
# see cgi.txt for more information on using mod_cgi
#

server.modules += ("mod_cgi")

$HTTP["url"] =~ "^/cgi-bin/" {
    # only allow cgi's in this directory
cgi.assign = ( "" => "" )
}
 ```
 Start `lighttpd` server and check everything work 
 
  ```bash
  $: rc-service lighttpd start && rc-update add lighttpd
  ```
  
<h2>

**Options**

</h2>


- Remove `mini_httpd` for ever
 
```bash
 $: apk del mini_httpd
```
  
- Enable `cache` and `Bzip2` / `Gzip` compression

 ```bash
  $: cd /etc/lighttpd && mkdir -p cache/acf
  $: chown -R lighttpd:lighttpd /etc/lighttpd/cache/
  $: chmod 700 -R cache/
  ```
  Refresh browser and see `var` folder in `acf` 
  
- Filter local IP only (already define in our custom lighttpd.conf)

 ```bash
# Allow only local IP
$HTTP["remoteip"] != "192.168.1.0/24" {
url.access-deny = ("")
}
  ```


