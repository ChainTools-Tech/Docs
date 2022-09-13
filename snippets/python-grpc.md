# Python gRPC

```python
import grpc

from grpc_reflection.v1alpha.proto_reflection_descriptor_database import ProtoReflectionDescriptorDatabase
from google.protobuf.descriptor_pool import DescriptorPool


# To use Server Reflection with ProtoReflectionDescriptorDatabase, first initialize an instance with a channel.
server_address = "grpc.juno.chaintools.tech:443"
creds = grpc.ssl_channel_credentials()
with grpc.secure_channel(server_address, creds) as channel:
    reflection_db = ProtoReflectionDescriptorDatabase(channel)

    # Then use this instance to feed a DescriptorPool
    desc_pool = DescriptorPool(reflection_db)

    # Then use the Reflection Database to list all the services
    services = reflection_db.get_services()

    for service in services:
        service_details = desc_pool.FindServiceByName(service)
        print(f'Service: {service}')
        for method in service_details.methods_by_name:
            print(f' - Method: {method}')
        del service_details
```
