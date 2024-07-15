# Unique name of the service which is registered in the Eureka server
serviceName: PAYMENTS.BILL.bill-adapter

#Brief description of this component
description: Bill Adepter

#The domain this component belongs to
domainName: PAYMENTS

#Space name where service belongs to
productName: BILL

#Source code repository url
bitbucketUrl:myurl

# Intended usage of this component:
#     'private' -— only for use by the owner
#     'public'  -- exposes an api for other domains
# Services that are under development, experimental, not
# supported, replaced, liable to change without warning, etc.
# should generally be marked as private.
visibility: public

# A list of declared dependencies on which
# this component depends. Must be valid service discovery names
# registered in the Eureka server.
dependencies:
  platformServices:
    - ARCHITECTURE.SERVICES.ServiceRegistry
    - PAYMENTS.BILL.ConfigServer
    - PAYMENTS.BILL.Dynatrace
    - PAYMENTS.BILL.redis
    - PAYMENTS.BILL.RabbitMQ
