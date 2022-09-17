# Create an API Product

## What is an API Product?
### An API Product is a bundling of API operations. An API Product can be created by choosing one or more API operations from one or more API Docs (Open API Specification). An API product typically provides one business functionality. For example, bulk-sms product will consist of all APIs required by a consumer to get the bulk-sms capability.

To create an API Product, update the values.yaml file with appropriate values for your product. Add a new product entry to create a new product.

Below are the parameters to be updated.

| Parameter | Description | Sample Value |
|-----------|-------------|--------------|
|name|Name of the apiproduct. Must be alphanumeric with lower case letters. Should be meaningful and should depict what the product offers.|airtel-iq-bulk-sms|
|title|This will be displayed on the developer portal as the Apiproduct name. It can have spaces and it can have mix of lower case and captal case letters. Should be meaningful and should depict what the product offers.|Airtel IQ Bulk SMS|
|description|Brief description about the product.|Airtel IQ Bulk SMS product offers APIs with capability to send sms to more than one destination mobile number with a single API call.|
|lob|Line of business. The gateway being a shared kubernetes cluster, some logical boundries are necessary to enable isolation. An lob translates to a namespace in the kubernetes cluster and will have dedicated compute resources like cpu and memory. Currently supported values are prepaid, postpaid, dth, b2b, iot, iq, commons |iq|
|apiOwnerEmail|Email address of the API Owner. This email address will be displayed on the developer portal and can be used to contact API Owners for any assistance or support related to the API.|airtel-iq-support@airtel.com|
|apiOwnerName|Name of the API Owner|Airtel IQ Support|
|tags|Tags are used to filter APIs on the developer portal. There is no other significance of tags. These work as filters on the developer portal for easier discovery.|sms{}|
|version|A product can have one or more versions. Each version might have its own bundling of APIs. This field denotes the version of the product.|v1.0.0|
|apiDocs.apiDocName|A product is a bundle of APIs. A product can have one or more API operations from one or more API Docs(Open API Specs). apiDocName is the name of API Doc object.|airtel-iq-messaging-api|
|openApi.operations.id|This is the id of any API operation which will be part of the product. This is the id of operation defined in the API Doc (Open API Spec)|getEmployees|
|gatewayConfig.route.routeRef.name|The API needs to have a backed route configured. Route is an object in Gloo Portal where we define backend IPs/Hosnames and routing rules and other policies. Since we are referencing a route here, the route must already be there. Hence, we need to create a route before we can assign it to an operation.|airtel-iq-bulk-sms-route|

Below is a sample yaml for API product.
``` yaml
apiProducts:
- name: my-product
  title: "My Product Title"
  description: "API Product description"
  lob: lob-name
  apiOwnerEmail: my-email@airtel.com
  apiOwnerName: Airtel Support
  tags:
     tag1: {}
     tag2: {}
  versions:
    - version: 'v1.0.0'
      apiDocs:
       - apiDocName: oas-doc-api-1
         openApi: {} # Having {} in openApi means all operations in the Open API Spec must be included.
         gatewayConfig:
            route:
              routeRef:
                 name: backend-route-1
                 namespace: route-ns-1
       - apiDocName: oas-doc-api-2
         openApi:
          operations:
          - id: operation1
            gatewayConfig:
                route:
                  routeRef:
                    name: backend-route-2
                    namespace: route-ns-2
```                    