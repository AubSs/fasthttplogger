# FastHttpLogger
HTTP request logger middleware for FastHttp


##### Tiny / TinyColored
```
<method> <url> - <status> - <response-time us>
GET /hello - 200 - 11.925 us
```

#### Short / ShortColored
```
<remote-addr> | <HTTP/:http-version> | <method> <url> - <status> - <response-time us>
127.0.0.1:53324 | HTTP/1.1 | GET /hello - 200 - 44.8µs
```

#### Combined / CombinedColored
```
[<time>] <remote-addr> | <HTTP/http-version> | <method> <url> - <status> - <response-time us> | <user-agent>
[2017/05/31 - 13:27:28] 127.0.0.1:54082 | HTTP/1.1 | GET /hello - 200 - 48.279µs | Paw/3.1.1 (Macintosh; OS X/10.12.5) GCDHTTPRequest
```

## Examples

### FastHttp
```go
func Hello(ctx *fasthttp.RequestCtx) {
  fmt.Fprintf(ctx, "hello, %s!\n", ctx.UserValue("name"))
}

func main() {
	m := func(ctx *fasthttp.RequestCtx) {
		switch string(ctx.Path()) {
		case "/hello":
			Hello(ctx)
		default:
			ctx.Error("not found", fasthttp.StatusNotFound)
		}
	}

	fasthttp.ListenAndServe(":8080", logger.Tiny(m))
}
```

### FastHttp + FastHttpRouter
```go
func Hello(ctx *fasthttp.RequestCtx) {
	fmt.Fprintf(ctx, "hello, %s!\n", ctx.UserValue("name"))
}

func main() {
	router := fasthttprouter.New()
	router.GET("/hello/:name", Hello)
	s := &fasthttp.Server{
		Handler: logger.CombinedColored(router.Handler),
		Name: "FastHttpLogger",
	}
	log.Fatal(s.ListenAndServe(":8080"))
}
```
