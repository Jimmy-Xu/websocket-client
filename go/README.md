websocket client(golang) for Hyper.sh
=====================================

Websocket client example for use websocket api `/events/ws` of Hyper.sh

# Usage

There are two ways to run websocket client.
- run wsclient.go
- use ./util.sh (wrapper for wsclient.go)

```
//The first thing is : fetch client code
$ go get github.com/Jimmy-Xu/websocket-client/
$ cd $GOPATH/src/github.com/Jimmy-Xu/websocket-client/go
```

## run wsclient.go

### view help of wsclient.go

```
$ go run wsclient.go --help   
Usage of /tmp/go-build045314814/command-line-arguments/_obj/exe/wsclient:
  -accessKey string
    	Hyper.sh Access Key
  -addr string
    	ApiRouter entrypoint (default "us-west-1.hyper.sh:443")
  -filter value
    	Filter event by container,image,label,event, example 'container=test,image=busybox,label=key=value,event=start' (default string method)
  -pretty
    	Output result json as prettyprint JSON
  -secretKey string
    	Hyper.sh Secret Key
```

### prepare credential environment variable
```
//set HYPER_ACCESS_KEY and HYPER_SECRET_KEY of Hyper.sh
$ export API_ROUTER=147.x.x.x:6443
$ export HYPER_ACCESS_KEY=xxxxx
$ export HYPER_SECRET_KEY=xxxxxx
```

### Watch all events

`accessKey` and `secretKey` are required options

```
$ go run wsclient.go --addr=${API_ROUTER} --accessKey $HYPER_ACCESS_KEY  --secretKey $HYPER_SECRET_KEY
connecting to wss://147.x.x.x:6443/events/ws
connected, watching event now:
{"status":"start","id":"f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179","from":"xjimmyshcn/busybox","Type":"container","Action":"start","Actor":{"ID":"f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179","Attributes":{"":"","exitCode":"0","image":"xjimmyshcn/busybox","name":"test4","sh_hyper_instancetype":"s4","test1":"","test2":"test2","test3":"test3=test3"}},"time":1476375774,"timeNano":1476375774255155116}
{"status":"stop","id":"f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179","from":"xjimmyshcn/busybox","Type":"container","Action":"stop","Actor":{"ID":"f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179","Attributes":{"":"","exitCode":"0","image":"xjimmyshcn/busybox","name":"test4","sh_hyper_instancetype":"s4","test1":"","test2":"test2","test3":"test3=test3"}},"time":1476375778,"timeNano":1476375778304732322}
```

### Output pretty event json

use option `--pretty`

```
$ go run wsclient.go --addr=${API_ROUTER} --accessKey $HYPER_ACCESS_KEY  --secretKey $HYPER_SECRET_KEY --pretty
connecting to wss://147.x.x.x:6443/events/ws
connected, watching event now:
{
  "Action": "start",
  "Actor": {
    "Attributes": {
      "": "",
      "exitCode": "0",
      "image": "xjimmyshcn/busybox",
      "name": "test4",
      "sh_hyper_instancetype": "s4",
      "test1": "",
      "test2": "test2",
      "test3": "test3=test3"
    },
    "ID": "f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179"
  },
  "Type": "container",
  "from": "xjimmyshcn/busybox",
  "id": "f29698cac3f6f66e84790fb12b3e5e4f3455b89b3ff12150ac4d86b8b90d9179",
  "status": "start",
  "time": 1.476375852e+09,
  "timeNano": 1.4763758521916593e+18
}
```

### Watch event with filter

use option `--filter`, support filter by `container,image,label,event`
- **container**: container id or name
- **image**: imageid or name
- **label**: label of container
- **event**: `start|stop`

```
$ go run wsclient.go --addr=${API_ROUTER} --accessKey $HYPER_ACCESS_KEY  --secretKey $HYPER_SECRET_KEY  --filter=container=wstest2,image=alpine,event=stop,label=type,label=sh_hyper_instancetype=s1
```

### Watch event with debug

use option `--debug` to show curl/wscat command line

```
$ go run wsclient.go --addr=${API_ROUTER} --accessKey $HYPER_ACCESS_KEY  --secretKey $HYPER_SECRET_KEY --debug
connecting to wss://x.x.x.x:6443/events/ws
-----------------------------------------------curl command line begin-----------------------------------------------
curl -g -k -v \
  -H "Content-Type: application/json" \
  -H "X-Hyper-Date: 20161026T071934Z" \
  -H "X-Hyper-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855" \
  -H "Host: x.x.x.x:6443" \
  -H "Authorization: HYPER-HMAC-SHA256 Credential=xxxxxxxxxx/20161026/us-west-1/hyper/hyper_request, SignedHeaders=content-type;host;x-hyper-content-sha256;x-hyper-date, Signature=150f6019815ad5425669878bebd098d147b41391f63d6e95a05cee4b86881bbf" \
  -X GET \
  -H "Connection: Upgrade"\
  -H "Upgrade: websocket"\
  -H "Sec-Websocket-Version: 13" \
  -H "Sec-WebSocket-Key: MHg0MDQ4NTA=" \
  'https://x.x.x.x:6443/events/ws'
-----------------------------------------------curl command line end-----------------------------------------------
-----------------------------------------------wscat command line begin-----------------------------------------------
wscat -n \
  -H "Content-Type: application/json" \
  -H "X-Hyper-Date: 20161026T071934Z" \
  -H "X-Hyper-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855" \
  -H "Host: x.x.x.x:6443" \
  -H "Authorization: HYPER-HMAC-SHA256 Credential=xxxxxxxxxx/20161026/us-west-1/hyper/hyper_request, SignedHeaders=content-type;host;x-hyper-content-sha256;x-hyper-date, Signature=150f6019815ad5425669878bebd098d147b41391f63d6e95a05cee4b86881bbf" \
  -c 'wss://x.x.x.x:6443/events/ws'
-----------------------------------------------wscat command line end-----------------------------------------------
connected, watching event now:
```

# Test filter with util.sh

./util.sh makes start websocket client with filter easier.

## view help of util.sh
```
$ ./util.sh
Usage: ./util.sh <ACTION> [OPTION]

<ACTION>:
 - ps                        : list test container
 - run                       : run test container
 - stop                      : stop test container
 - start                     : start test container
 - rm                        : remove test container
 - watch <FILTER> [CASE_NO]  : run watch with filter

<FILTER>:
 - container : use container.lst
 - image     : use image.lst
 - label     : use label.lst
 - event     : use event.lst

[CASE_NO]:
 - <empty>     : show watch filter list.
 - <not empty> : start websocket client to watch with filter

Example:
  ./util.sh run
  ./util.sh watch container
  ./util.sh watch container 1
  ./util.sh stop
```

## config

modify the following parameter in etc/config:

- **G_API_ROUTER**: default is "us-west-1.hyper.sh:443"

## manage test container
```
//run test container
./util.sh run

//list test container
./util.sh ps

//stop test container
./util.sh stop

//start test container
./util.sh start

//remove test client
./util.sh rm
```

## manage test case
```
//list test case NO.
./util.sh watch container

//start specified test client.
./util.sh watch container 1
```


# FAQ:

## wrong Hyper.sh Credential
```
$ go run wsclient.go --addr=${API_ROUTER} --accessKey xxx --secretKey xxx
connecting to wss://us-west-1.hyper.sh:443/events/ws
Error:websocket: bad handshake
exit status 1
```

## how to watch docker events via remote api
```
$ curl -g 'http://127.0.0.1:2375/events?filters={"container":{"test":true},"image":{"busybox":true},"label":{"test1":true,"test2=test2":true,"test3=test3=test3":true},"event":{"start":true,"stop":true}}'

{"status":"start","id":"6c0902c750c73d4bee6cecc82b1a6e3f36f625f65cfd5417fdb09e5b9a2f7d16","from":"busybox","Type":"container","Action":"start","Actor":{"ID":"6c0902c750c73d4bee6cecc82b1a6e3f36f625f65cfd5417fdb09e5b9a2f7d16","Attributes":{"image":"busybox","name":"test","sh_hyper_instancetype":"s4","test1":"","test2":"test2","test3":"test3=test3"}},"time":1476419660,"timeNano":1476419660534483726}
{"status":"stop","id":"6c0902c750c73d4bee6cecc82b1a6e3f36f625f65cfd5417fdb09e5b9a2f7d16","from":"busybox","Type":"container","Action":"stop","Actor":{"ID":"6c0902c750c73d4bee6cecc82b1a6e3f36f625f65cfd5417fdb09e5b9a2f7d16","Attributes":{"image":"busybox","name":"test","sh_hyper_instancetype":"s4","test1":"","test2":"test2","test3":"test3=test3"}},"time":1476419662,"timeNano":1476419662997733372}
```

## how to debug websocket with command line

use `wscat` as websocket client

```
//install wscat
$ npm install -g ws

//use wscat to connect websocket server
$ wscat -n \
  -H "Host: x.x.x.x:6443" \
  -H "Authorization: HYPER-HMAC-SHA256 Credential=xxxxxxxxxx/20161026/us-west-1/hyper/hyper_request, SignedHeaders=content-type;host;x-hyper-content-sha
256;x-hyper-date, Signature=b5e73397a5c374f98bfec4b24a93bc336b54808103d8eff3fabbe9070d63ad1e" \
  -H "Content-Type: application/json" \
  -H "X-Hyper-Date: 20161026T070322Z" \
  -H "X-Hyper-Content-Sha256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  -c 'wss://x.x.x.x:6443/events/ws'

connected (press CTRL+C to quit)
  < {"status":"start","id":"96fe510588e4bd2e14ff2a1dcdc0ec62acf5a2da5d83506efc795aa0299ff033","from":"xjimmyshcn/busybox","Type":"container","Action":"start","Actor":{"ID":"96fe510588e4bd2e14ff2a1dcdc0ec62acf5a2da5d83506efc795aa0299ff033","Attributes":{"":"","empty":"","exitCode":"0","id":"test1","image":"xjimmyshcn/busybox","key":"test1=test1","name":"wstest1","sh.hyper.fip":"","sh_hyper_instancetype":"s1","test1":"","test2":"test2","test3":"test3=test3","type":"test"}},"time":1477465526,"timeNano":1477465526775991826}
>
```

# REF:

- WebSocket:
  - https://github.com/gorilla/websocket/blob/master/client_server_test.go#L322
- Kubernates Watch:
  - https://github.com/kubernetes/kubernetes/blob/master/pkg/client/cache/reflector.go#L362
- Browser aws4 example
  - https://github.com/mhart/aws4/tree/master/browser
- Filter
  - https://github.com/docker/engine-api/blob/master/types/filters/parse_test.go
- Docker Event
  - https://github.com/docker/engine-api/blob/master/client/events_test.go#L56
- Connection number limit
  - http://www.cnblogs.com/zhangqingping/p/4344278.html
- Connection rate limit
  - https://github.com/golang/go/wiki/RateLimiting
