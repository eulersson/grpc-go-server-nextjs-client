FROM golang:1.22.0-alpine as base

EXPOSE 9976

FROM base as builder

RUN apk add --update --no-cache protoc protobuf-dev \
 && go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28 \
 && go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2

WORKDIR /src

COPY server/go.mod server/go.sum ./
RUN go mod download

COPY yourcoolapp_grpc.proto .

COPY server/server/main.go ./server/main.go

RUN mkdir -p ./grpc_definition && \
  protoc \
  --go_out=./grpc_definition \
  --go_opt=paths=source_relative \
  --go-grpc_out=./grpc_definition \
  --go-grpc_opt=paths=source_relative \
  yourcoolapp_grpc.proto

RUN CGO_ENABLED=0 GOOS=linux go build -o /bin/yourcoolapp-rpc-server server/main.go

FROM builder as development

WORKDIR /src

CMD yourcoolapp-rpc-server

FROM base as production

COPY --from=builder /bin/yourcoolapp-rpc-server /bin

CMD yourcoolapp-rpc-server