# Bug reproducer
Used to reproduce a bug in OpenLiberty mpOpenAPI UI. 
Reported here: https://github.com/OpenLiberty/open-liberty/issues/11137

## OL server
* Prepare the server `mvn clean verify`
* Start it: `mvn liberty:start`

The OpenAPI UI will be on http://localhost:9080/openapi/ui/
And the metrics on http://localhost:9080/metrics

## Reverse proxy
* Prepare the image in the `reverseproxy` folder:
`docker build -t rp .`
* Start it: `docker run --name rp -d --network="host" rp`

Try to access the OpenAPI UI on http://localhost:9081/backend1/openapi/ui/

The UI uses an absolute path to load the OpenAPI document from `/openapi` instead of using a relative one.


## Test with combination 1

```xml
    <featureManager>
        <feature>jaxrs-2.1</feature>
        <feature>mpMetrics-2.3</feature>

        <feature>mpOpenAPI-1.1</feature>
<!--        <feature>openapi-3.1</feature>-->
    </featureManager>
```

:heavy_check_mark: The metrics are available

:heavy_check_mark: The direct mpOpenAPI is available and working (http://localhost:9080/openapi/ui/)
 
:x: But through the reverse proxy it isn't 



## Test with combination 2

```xml
    <featureManager>
        <feature>jaxrs-2.1</feature>
        <feature>mpMetrics-2.3</feature>

<!--        <feature>mpOpenAPI-1.1</feature>-->
        <feature>openapi-3.1</feature>
    </featureManager>
```

:x: The metrics are *not* available. The error during startup is:
```
"java.lang.IllegalArgumentException: Unable to load conflicting versions of features "com.ibm.websphere.appserver.mpConfig-1.4" and "com.ibm.websphere.appserver.mpConfig-1.2".  The feature dependency chains that led to the conflict are: com.ibm.websphere.appserver.mpMetrics-2.3 -> com.ibm.websphere.appserver.mpConfig-1.4 and com.ibm.websphere.appserver.openapi-3.1 -> com.ibm.websphere.appserver.mpOpenAPI-1.0 -> com.ibm.websphere.appserver.mpConfig-1.2 com.ibm.ws.kernel.feature.internal.FeatureManager reportErrors"
```

:heavy_check_mark: The direct IBM OpenAPI is available and working (http://localhost:9081/backend1/openapi/ui/) 

:heavy_check_mark: And it also works through the reverse proxy 



