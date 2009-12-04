GopherBlock
===========

GopherBlock (a play on the name WebBrick) is a toolkit for creating gopher applications.

It can happily run as a plain gopher server, serving up files and directories, or you can use it to build dynamic applications over the gopher protocol

Warning
-------
This is still very much in development, don't assume anything will work


Examples
--------

The following will serve anything it finds in the current directory on port 70:
    require 'gopherblock'
    server=GopherBlock.new
    server.start

when initializing a server, you can pass it a hash with the following options:
     {
	:port => 70,
	:dir => '/usr/local/gopherfiles',
	:host => 'gopher.myhost.com',
	:logfile => '/var/log/gopher.log'
     }

All of these are optional.  Logging is only enabled if you specify a logfile

GopherBlock also supports mounting procs on a certain path:

    server.mount_proc("testapp") do |url,args,params|
     	"Hello Wold!"
    end

When a client calls: gopher://servername.com/0/testapp, they will see the text "Hello World!"

The url is whatever url the client requested, args are an array of url arguments.  In the previous example, this would be empty, if they called instead:
gopher://servername/0/testapp/foo/bar, then args would contain ["foo","bar"].  Normal gopher paramaters are sent in params as a string.  A complete example:

    server.mount_proc("hello") do |url,args,params|
        "Hello #{args[0]} you called #{url} with the paramaters: #{params}"
    end

If the client were to call: gopher://servername/0/hello/bob?extra params, they would see:

Hello bob you called /hello/bob with the paramaters: extra params


Mounted procs are called in the order they were defined,  so if you define the same one twice, only the first will be called.  If you supply a blank
proc name, then it will be mounted as the root directory.

There is however a path limitation,  proc names must be defined as root items.  You can create a procedure for /test, but not /test/app.

A complete example of a basic gopher server:
    require 'gopherblock'
    server=GopherBlock.new({ :host => 'gopher.robsayers.com', :dir => '/home/rsayers/gopher' })
    server.start

This is the code running my own personal server.  It handles executables just fine, and renders gophermaps properly
