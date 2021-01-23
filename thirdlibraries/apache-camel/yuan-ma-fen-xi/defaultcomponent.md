# DefaultComponent

Camel 默认组件实现。

## createEndpoint

此方法是一个模板方法：

```java
//本类中的模板方法
public Endpoint createEndpoint(String uri) throws Exception {
}

//需要在子类重写
protected abstract Endpoint createEndpoint(String uri, String remaining, Map<String, Object> parameters)
        throws Exception;
```

```java
public Endpoint createEndpoint(String uri) throws Exception {
        ObjectHelper.notNull(getCamelContext(), "camelContext");
        // check URI string to the unsafe URI characters
        String encodedUri = preProcessUri(uri);
        URI u = new URI(encodedUri);
        String path;
        if (u.getScheme() != null) {
            // if there is a scheme then there is also a path
            path = URISupport.extractRemainderPath(u, useRawUri());
        } else {
            // this uri has no context-path as the leading text is the component name (scheme)
            path = null;
        }

        Map<String, Object> parameters;
        if (useRawUri()) {
            // when using raw uri then the query is taking from the uri as is
            String query;
            int idx = uri.indexOf('?');
            if (idx > -1) {
                query = uri.substring(idx + 1);
            } else {
                query = u.getRawQuery();
            }
            // and use method parseQuery
            parameters = URISupport.parseQuery(query, true);
        } else {
            // however when using the encoded (default mode) uri then the query,
            // is taken from the URI (ensures values is URI encoded)
            // and use method parseParameters
            parameters = URISupport.parseParameters(u);
        }
        // parameters using raw syntax: RAW(value)
        // should have the token removed, so its only the value we have in parameters, as we are about to create
        // an endpoint and want to have the parameter values without the RAW tokens
        URISupport.resolveRawParameterValues(parameters);

        // use encoded or raw uri?
        uri = useRawUri() ? uri : encodedUri;

        validateURI(uri, path, parameters);
        if (log.isTraceEnabled()) {
            // at trace level its okay to have parameters logged, that may contain passwords
            log.trace("Creating endpoint uri=[{}], path=[{}], parameters=[{}]", URISupport.sanitizeUri(uri), URISupport.sanitizePath(path), parameters);
        } else if (log.isDebugEnabled()) {
            // but at debug level only output sanitized uris
            log.debug("Creating endpoint uri=[{}], path=[{}]", URISupport.sanitizeUri(uri), URISupport.sanitizePath(path));
        }
        Endpoint endpoint = createEndpoint(uri, path, parameters);
        if (endpoint == null) {
            return null;
        }

        endpoint.configureProperties(parameters);
        if (useIntrospectionOnEndpoint()) {
            setProperties(endpoint, parameters);
        }

        // if endpoint is strict (not lenient) and we have unknown parameters configured then
        // fail if there are parameters that could not be set, then they are probably misspell or not supported at all
        if (!endpoint.isLenientProperties()) {
            validateParameters(uri, parameters, null);
        }

        afterConfiguration(uri, path, endpoint, parameters);
        return endpoint;
    }
```

* 构建URI；
* 检查
* 创建EndPoint；
* 配置EndPoint属性；
* 后置调用。

