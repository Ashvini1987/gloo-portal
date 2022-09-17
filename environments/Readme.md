# Create an API Environment
## What is an Evironment from Gloo Portal perspective?
### An environment consists one or more API Products with one or more usage plans. Each usage plan will have two things: 
```
1. Auth Policy: This can be either api-key based auth or an Oauth based auth.
2. Rate Limit Policy: The rate limit has a rate limit number per unit. The unit can be SECOND, MINUTE, HOUR or DAY.
```
### An environment also has one or more domain names associated with it. The domain is the host name which will be used by the consumer to call the API. 

For creating or updating an Environment in Gloo Portal, update the values.yaml file to add a new environment entry or to update an existing environment entry.

Below are the details of different parameters in the values.yaml file.

|Parameter|Description|Sample values|
|---------|-----------|-------------|
|name|This is the name of the environment.|aplos-tls-sit|
|domain|Domain the host name to be used by API consumers to call the API. The domain name must resolve to the IP address of the API Gateway (Gloo Edge). An appropriate DNS entry must be available or needs to be added in the DNS.|aplos.internal-gateway.apim.india.airtel.itm|
|description|This is the description of the environment. It should give an understanding of what the environment is hosting.|This environment has the APLOS OIC APIs and should be used by internal consumer applications for SIT testing|
|apiProducts.names|This parameter will have an array of API Product names which are to be published on the given environment.|- airtel-iq-bulk-sms |
|apiProducts.usagePlans|This parameter takes in an array of usagePlans which are to be associated with the products in the environment.| - enterprise-plan|
|tls.enabled|This parameter decides whether the consumers will consume the APIs over TLS or not. If enabled consumers will access the given host(As provided in domains) on port 443 with TLS. Default value is false.|true|
|tls.oneWayTls|This parameter will only come into picture if tls.enabled is set to true. This parameter defines if the TLS will be simple TLS or mutual TLS. If set to true, it will be simple TLS, otherwise it will be mutual TLS. Default value is false, which means mutual TLS is default behaviour.|true|
|tls.certificate.commonName|If tls.enabled is set to true then we also need to configure certificate. The certificate will get provisioned automatically. However, we need to set some parameters like commonName. This is the field commonName in the certificate. | Aplos Env Gateway Cert|
|tls.certificate.expiresIn|This parameter defines the certificate validity. How long will the certificate be valid. | 720h|
|tls.renewBefore|The certificate is auto renewed by cert manager. This parameter instructs cert manager to renew the certificate x no of hours before the certificate expiry.|24h|
|usagePlans[].name|This is the name of usagePlan. A usage plan has two things, an auth policy and a rate limit policy. A usage plan name should give a hint of these policies. | platinum|
|usagePlans[].displayName| This is the name which gets displayed in Developer Portal. This should give information about the plan which we need to show to the consumer.| 10 API Calls Per Sec - INR 1 Per API Call|
|usagePlans[].authPolicy|Auth policy can be either apiKey or oauth|apiKey|
|usagePlans[].rateLimit.requestPerUnit| This parameters defines the number of request per unit allowed for the given usagePlan|1000|
|usagePlans[].rateLimit.unit|The unit to be used to measure rate limit. The allowed values are SECOND MINUTE HOUR DAY| SECOND|
|usagePlans[].authPolicy.oauth.authorizationUrl|This parameter is for authorization URL of identity provider. This will be displayed on developer portal as part of the documentation. It is not used by Gateway anywhere else. |https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/auth|
|usagePlans[].authPolicy.oauth.tokenUrl|This parameter is for the token URL of the identity provider. This will be displayed on the developer portal as part of the documentation. This is not used in the Gateway anywhere else.| https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/token|
|usagePlans[].authPolicy.oauth.jwtValidation.remoteJwks.url|This is the url to fetch the JWKS from the identity provider. The JWKS is used for token validation by the Gateway for the API calls. This must be a valid URL and must be accessible from the Gateway. In case we don't have the JWKS URL then choose the localJwks instead of remoteJwks option. | https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/certs|
|usagePlans[].authPolicy.oauth.jwtValidation.localJwks.inlineString|If the Jwks is not available over a URL we can use localJwks option and provide the Jwks inline.||
|usagePlans[].authPolicy.oauth.jwtValidation.scopes|The scopes are the scopes which needs to be validated by the gateway. If the given scope is not present in the incoming jwt token, then the API request will be rejected. | group:|

Below is a sample environment yaml with both apiKey and oauth usagePlans.
``` yaml
environments:
  - name: aplos-tls-sit
    domain: aplos.internal-gateway.apim.india.airtel.itm
    description: "For Dev and Test"
    apiProducts:
      - names:
          - employee360
        usagePlans:
          - large
          - small
          - unlimited
    tls:
      enabled: true
      oneWayTls: true
      certificate:
        commonName: "Aplos gateway cert"
        exiresIn: "720h"
        renewBefore: "24h"
usagePlans:
  - name: large-apikey
    displayName: 1000 RPS with apikey auth
    authPolicy:
      apiKey: {}
    rateLimit:
      requestPerUnit: 1000
      unit: SECOND
  - name: oauth-plan
    displayName: OAuth Plan with 20 RPS
    authPolicy:
      oauth:
        authorizationUrl: https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/auth
        jwtValidation:
          remoteJwks:
            refreshInterval: 5s
            url: https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/certs
        scopes:
          group:
            required: true
        tokenUrl: https://keycloak-keycloak.apps.pocokdclus01.india.airtel.itm/auth/realms/portal/protocol/openid-connect/token
    rateLimit:
      requestsPerUnit: 20
      unit: SECOND
```



