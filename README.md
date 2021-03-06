# Go API [![License](https://img.shields.io/:license-apache-blue.svg)](https://opensource.org/licenses/Apache-2.0) [![GoDoc](https://godoc.org/github.com/micro/go-api?status.svg)](https://godoc.org/github.com/micro/go-api) [![Travis CI](https://api.travis-ci.org/micro/go-api.svg?branch=master)](https://travis-ci.org/micro/go-api) [![Go Report Card](https://goreportcard.com/badge/micro/go-api)](https://goreportcard.com/report/github.com/micro/go-api)


Go API is a library that includes a set of helper methods for composing API services

This includes the ability to define methods to associate with HTTP endpoints served by the micro api.

## Usage

When defining your API service also include the endpoint mapping

### Example

This example serves `/greeter` with http methods GET and POST to the Greeter.Hello RPC handler.

```go
type Greeter struct 

// Define the handler
func (g *Greeter) Hello(ctx context.Context, req *proto.Request, rsp *proto.Response) error {
	log.Print("Received Greeter.Hello API request")

	// make the request
	response, err := g.Client.Hello(ctx, &hello.Request{Name: req.Name})
	if err != nil {
		return err
	}

	// set api response
	rsp.Msg = response.Msg
	return nil
}

// A greeter service
service := micro.NewService(
	micro.Name("go.micro.api.greeter"),
)
// Parse command line flags
service.Init()

// Register handler and the endpoint mapping
proto.RegisterGreeterHandler(service.Server(), new(Greeter), api.WithEndpoint(&api.Endpoint{
	// The RPC method
	Name: "Greeter.Hello",
	// The HTTP paths. This can be a POSIX regex
	Path: []string{"/greeter"},
	// The HTTP Methods for this endpoint
	Method: []string{"GET", "POST"},
	// The API handler to use
	Handler: api.Rpc,
})

// Run it as usual
service.Run()
```

