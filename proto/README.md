# Raw Forecasts protobuf Sources

This directory contains the raw protobuf sources for the Anko Investor gRPC Forecasts Service.

These sources may either be used to generate third party clients, extend Anglo Korean clients, or alongside tools such as [grpcurl](https://github.com/fullstorydev/grpcurl).

## gRPC Reflection

The Anko Investor gRPC Forecasts Service implements the [gRPC reflection protocol](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) which may also be used to generate clients:

```bash
$ grpcurl -vv forecasts.anko-investor.com:443 describe Forecasts.Stream
Forecasts.Stream is a method:
rpc Stream ( .Metadata ) returns ( stream .Forecast );
$ grpcurl -vv forecasts.anko-investor.com:443 describe Metadata
Metadata is a message:
message Metadata {
  string ua = 1;
  repeated .Tag tags = 2;
}
$ grpcurl -vv forecasts.anko-investor.com:443 describe Forecast
Forecast is a message:
message Forecast {
  string id = 1;
  string ric = 2;
  double score = 3;
  .Symbol symbol = 4;
  .Label label = 5;
}
```
*however*... these descriptions do not include the docstrings that the raw protobuf sources do, and so can be a little opaque.
