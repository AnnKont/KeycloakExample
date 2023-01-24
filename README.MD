# Spring security with Keycloak

This is an example how to use Spring security with Keycloak.

## Pre-configuration

1) You need to login in Keycloak(http://localhost:8080/) using the admin credentials(by default **_username=admin_**, **_password=admin_**).
   These credentials can be overridden by environment variables: `KEYCLOAK_ADMIN` and `KEYCLOAK_ADMIN_PASSWORD`

2) Then you need to create new realm in Keycloak(in this example the realm is named "_demo_")
3) The next step is to configure your application to communicate with Keycloak. This can be done by editing the application.yml file and adding the following line:
```
spring.security.oauth2.resourceserver.jwt.jwk-set-uri= http://localhost:8080/realms/demo/protocol/openid-connect/certs
```
This line configures the application to use the JSON Web Key Set (JWKS) endpoint of the Keycloak server to validate JSON Web Tokens (JWT) used for authentication. The "_demo_" in the URL should be replaced with the name of the realm you created in step 2.
4) After creating a new realm, you should create a client within that realm. A client in Keycloak represents an application that wants to use Keycloak for authentication and authorization(in this example the client **_ID_** and **_name_** is named "backend")

## Test

1) After launching the app, it can be tested by sending a request to the Keycloak server using the curl command:
```commandline
curl --location --request POST 'http://localhost:8080/realms/demo/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=backend' \
--data-urlencode 'client_secret=YdBdkGS2T8ldeg2TbLCDKUbXhN0IbupQ' \
--data-urlencode 'grant_type=client_credentials'
```
It is requesting an access token from the Keycloak server using the client credentials flow. The client_id, client_secret and grant_type are required parameters(**_client_id_**=name of the client you created in step 3, **_client_secret_**=secret you configured for the client in the Keycloak admin console). The "_demo_" in the URL should be replaced with the name of the realm you created in step 2.
2) Once you have received an access token from the Keycloak server in step 5, you can use this token to test your application:
```commandline
curl --location --request GET 'http://localhost:8081/api/me' \
--header 'Authorization: Bearer <token_from_step_1>'
```