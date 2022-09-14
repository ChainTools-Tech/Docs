# Python gRPC

Code snippet, which lists all services and methods using reflection of gRPC service.

```python
import grpc

from grpc_reflection.v1alpha.proto_reflection_descriptor_database import ProtoReflectionDescriptorDatabase
from google.protobuf.descriptor_pool import DescriptorPool


server_address = "grpc.juno.chaintools.tech:443"
creds = grpc.ssl_channel_credentials()
with grpc.secure_channel(server_address, creds) as channel:
    reflection_db = ProtoReflectionDescriptorDatabase(channel)

    desc_pool = DescriptorPool(reflection_db)

    services = reflection_db.get_services()

    for service in services:
        service_details = desc_pool.FindServiceByName(service)
        print(f'Service: {service}')
        for method in service_details.methods_by_name:
            print(f' - Method: {method}')
        del service_details
```
