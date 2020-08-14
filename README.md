**Resources for use with Cribl AppStream**

# Introduction
AppStream is able to extract details from almost any application executing on a Linux x86_64 system. This repo provides example applications and various resources for use with AppStream.

This is initally intended for internal Cribl use. That can change. For now, the instructions assume internal use. If you have questions about using some of this let us know; cribl-community.slack.com 

# Go Applications
Several pre-built applications for Go are available. The source for the examples are also available. All executables have been built with Go 1.14.

## Execute Go apps with AppStream
In order to extract the details using AppStream from a Go app you will use the scope executable and the library libscope.so. Download scope and libscope.so from CDN or get a copy from the applicable developer. It's easiest, althought not required, to put the executable and the lib in the same directory. If needed, set the environment variable LD_LIBRARY_PATH to point to the path where libscope.so exists. This limitation will be removed soon.

Example: run the hello world app
   ./scope go/hello

Example: run the multiple Go Routine app
   ./scope go/fileThread

## Hello World
The go/hello app prints an obligatory "hello world" message. It also perfoms several file I/O operations and then reads from an HTTP server and displays status. 

## Multiple Go Routines
The app fileThread in go/thread creates 100,000 Go Routines each of which do some file I/O.

## Network
There are 2 web server example apps.
You will need to be in the go/net dir in order to run these apps:
    cd go/net

### HTTP server
The app go/net/plainServer handles HTTP 1.X requests and responds with a message.
Check out the source in go/net/plainServer.go for directions on how to use the app. 

### HTTPS server
The app go/net/tlsServer handles HTTPS 1.X requests and responds with a message.
Check out the source in go/net/tlsServer.go for directions on how to use the app. 

# Python Applications
The app python/testssl.py is a basic HTTP server. The app was created for python3.
Check out the source in python/testssl.py for directions on how to use the app. 

# Node Applications
The app node/nodehttp.ts is a basic HTTPS server. It will display results from an https request on stdout.
Check out the source in node/nodehttp.ts for directions on how to use the app. 

# Build Scope
There is a container build environment that builds everything you need from master.

## Build the Container
From the appstream_apps directory:
docker-compose build --build-arg ssh_prv_key="$(cat ~/.ssh/id_rsa)" --build-arg ssh_pub_key="$(cat ~/.ssh/id_rsa.pub)" scope

## Build Scope
From the appstream_apps directory:
docker run --attach STDIN --attach STDOUT --interactive --rm -v "$PWD":/scope/lib/linux -v "$PWD/..":/scope/scope -w /scope appstream_apps_scope /scope/scope.sh build

This will put libscope.so and the scope executable in the appstream_apps directory. You can run test apps form there.
