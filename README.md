**Resources for use with Cribl AppStream**

# Introduction
AppStream is able to extract details from almost any application executing on a Linux x86_64 system. This repo provides example applications and various resources for use with AppStream.

This is initally intended for internal Cribl use. That can change. For now, the instructions assume internal use. If you have questions about using some of this let us know; cribl-community.slack.com 

# Pull the objects from CDN
Until the lib and executable are added to the cribl distro, or if you need to get the latest versions, they are availble from CDN:
```console
      http://cdn.cribl.io/dl/scope/latest/linux/libscope.so
      http://cdn.cribl.io/dl/scope/latest/linux/scope
```

Note that libscope.so is no longer strictly required. All you need is the scope executable. The library is available for a number of scenarios, as needed.

# Configuration
It is no longer required to set any environment variables in order to use AppStream. In fact, when using the scope executable (or 'cribl scope'), you need to ensure that LD_PRELOAD is not set. 

There are a number of env vars that can be set to manage configuration. All are optional. Refer to the help text emitted by executing the library libscope.so from the command line. This will display all of the configuration options:
```console
         libscope.so configuration
```
For example, in order to enable all events add the following environment variable:
```console
    export SCOPE_EVENT_METRIC=true
```

By default all events are sent over a TCP conenction on port 9109.
## Examples
### Extract HTTP headers as events
```console
    SCOPE_EVENT_HTTP=true
```
### Extract all metrics as events
```console
    SCOPE_EVENT_METRIC=true
```
Extract only network metrics as events
```console
    SCOPE_EVENT_HTTP_NAME=net.*
```
### Extract log data from files as events
```console
    SCOPE_EVENT_LOGFILE=true
```
Extract file data from specific files
```console
SCOPE_EVENT_LOGFILE_NAME=*.log
```
### Extract all console data as events
```console
    SCOPE_EVENT_CONSOLE=true
```
Extract only stderr data
```console
    SCOPE_EVENT_CONSOLE_NAME=stderr
```
### Change the destination of events from TCP port 9109 to a file
```console
    SCOPE_EVENT_DEST=file:///tmp/events.log
```
# Any Native Linux Application
You can extract details from any Linux app using the scope executable. For example;
```console
    scope /usr/bin/ps -ef
```

Note that, for example, “scope top” doesn’t work, but “scope /usr/bin/top” does. You need to provide the full path name to your application.

# Go Applications
Several pre-built applications for Go are available. The source for the examples are also available. All executables have been built with Go 1.14.

## Execute Go apps with AppStream
In order to extract the details using AppStream from a Go app you will use the scope executable. Download scope from CDN or get a copy from the applicable developer.

## Hello World
The go/hello app prints an obligatory "hello world" message. It also perfoms several file I/O operations and then reads from an HTTP server and displays status. 
Example: run the hello world app
```console
    ./scope go/hello
```

## Multiple Go Routines
The app fileThread in go/thread creates 100,000 Go Routines each of which do some file I/O.
Example: run the multiple Go Routine app
```console
    ./scope go/thread/fileThread
```

## Network
There are 2 web server example apps.
You will need to be in the go/net dir in order to run these apps:
```console
    cd go/net
```

### HTTP server
The app go/net/plainServer handles HTTP 1.X requests and responds with a message.
The server app uses port 80. In some environments it must be executed with root permissions. It's easiest to add the setuid bit to the file so that you don't need to use sudo.
Execute the app:
```console
      cd go/net
      scope ./plainServer 80
```
You may need to use sudo as the server binds to port 80 (the -E causes sudo to use the existing env vars):
```console
    sudo -E scope ./plainserver 80
```

Ping the server and retreive the test string:
```console
     curl http://localhost/hello
```

### HTTPS server
The app go/net/tlsServer handles HTTPS 1.X requests and responds with a message.
The server app uses port 443. In some environments it must be executed with root permissions. It's easiest to add the setuid bit to the file so that you don't need to use sudo.
Run from the go/net directory:
```console
     cd go/net
```

Create keys:
```console
     openssl genrsa -out server.key 2048
     openssl ecparam -genkey -name secp384r1 -out server.key
     openssl req -new -x509 -sha256 -key server.key -out server.crt -days 3650 -subj "/C=US/ST=California/L=San Francisco/O=Cribl/OU=Cribl/CN=localhost"
```

Run the web server:
```console
     scope ./tlsServer 4430
```

You may need to use sudo as the server binds to port 443:
```console
     sudo -E scope ./tlsServer 4430
```

Ping the server and retreive the test string:
```console
     curl -k --key server.key --cert server.crt https://localhost:4430/hello
```

# Python Applications
The app python/testssl.py is a basic HTTP server. The app was created for python3.
Execute the app:
```console
        cd python
        ./testssl.py create_certs
        scope ./testssl.py start_server
        scope ./testssl.py run_client
```

# Node Applications
The app node/nodehttp.ts is a basic HTTPS server. It will display results from an https request on stdout.
Execute the app:
```console
        cd node
        scope node nodehttp.ts
```

#PHP Applications
The app php/sslclient.php is an HTTPs client. It simply does an https get from cribl.io.

```console
        cd node
        scope php sslclient.php 
```


# Build Scope
There is a container build environment that builds everything you need from master.

## Build the Container
From the appstream_apps directory:
```console
docker-compose build --build-arg ssh_prv_key="$(cat ~/.ssh/id_rsa)" --build-arg ssh_pub_key="$(cat ~/.ssh/id_rsa.pub)" scope
```

## Build Scope
From the appstream_apps directory:
```console
docker run --attach STDIN --attach STDOUT --interactive --rm -v "$PWD":/scope/lib/linux -v "$PWD/..":/scope/scope -w /scope appstream_apps_scope /scope/scope.sh build
```

This will put libscope.so and the scope executable in the appstream_apps directory. You can run test apps from there.
