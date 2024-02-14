# gRPC Demo: Go (server) Next.js (client)

> [!NOTE]
> The service that is demonstrated is taken from the gRPC Go official docs.

```
docker-compose up
```

Then visit `http://localhost:3000/api/hello?name=John`, an API endpoint that reaches the
Go greeter service via RPC.

Those files listed below are automatically generated from the `myservice.proto`
Protobuf, therefore must not be edited. If you want to regenerate them or update the
`myservice.proto` then check the `protoc` calls on the `client/Dockerfile` and
`server/Dockerfile` to see how to generate the languge-specific interfaces:

- `client/lib/rpc/yourcoolapp_grpc.ts`
- `server/grpc_definition/yourcoolapp_grpc_grpc.pb.go`
- `server/grpc_definition/yourcoolapp_grpc.pb.go`

There is a `production` mode that would deliver thinner containers, removing
intermediary files. For instance in the case of Next.js the production container would
not include all `node_modules` and instead would benefit from the dependency tracing
that would only bring it what's needed (see the [output standalone Next.js
feature](https://nextjs.org/docs/app/api-reference/next-config-js/output#how-it-works)):

```
docker-compose -f docker-compose.yaml -f docker-compose.production.yaml build
```
