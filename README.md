# Bug reproducer
Used to reproduce a bug in OpenLiberty mpOpenAPI UI. 
Reported here: https://github.com/OpenLiberty/open-liberty/issues/11137

## OL server
* Prepare the server `mvn clean verify`
* Start it: `mvn liberty:start`

The OpenAPI UI will be on http://localhost:9080/openapi/ui/

## Reverse proxy
* Prepare the image in the `reverseproxy` folder:
`docker build -t rp .`
* Start it: `docker run --name rp -d --network="host" rp`

Try to access the OpenAPI UI on http://localhost:9081/backend1/openapi/ui/

The UI uses an absolute path to load the OpenAPI document from `/openapi` instead of using a relative one.
