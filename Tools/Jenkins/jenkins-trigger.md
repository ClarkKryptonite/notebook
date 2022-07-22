# Jenkins使用

## 使用Java程序触发Trigger报403错误
网上很多都是错的，直接看官方文档
[https://www.jenkins.io/doc/book/system-administration/authenticating-scripted-clients/#Authenticatingscriptedclients-Javaexamplewithhttpclient4.3.x](https://www.jenkins.io/doc/book/system-administration/authenticating-scripted-clients/#Authenticatingscriptedclients-Javaexamplewithhttpclient4.3.x)
对应代码为:
```java
import java.io.IOException;
import java.net.URI;

import org.apache.http.HttpHost;
import org.apache.http.HttpResponse;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.AuthCache;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.protocol.HttpClientContext;
import org.apache.http.impl.auth.BasicScheme;
import org.apache.http.impl.client.BasicAuthCache;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

/**
需要引用apache的HttpClient库
implementation 'org.apache.httpcomponents:httpclient:4.5.13'
*/
public class JenkinsScraper {

    public String scrape(String urlString, String username, String password)
        throws ClientProtocolException, IOException {
        URI uri = URI.create(urlString);
        HttpHost host = new HttpHost(uri.getHost(), uri.getPort(), uri.getScheme());
        CredentialsProvider credsProvider = new BasicCredentialsProvider();
        credsProvider.setCredentials(new AuthScope(uri.getHost(), uri.getPort()),
            new UsernamePasswordCredentials(username, password));
        // Create AuthCache instance
        AuthCache authCache = new BasicAuthCache();
        // Generate BASIC scheme object and add it to the local auth cache
        BasicScheme basicAuth = new BasicScheme();
        authCache.put(host, basicAuth);
        CloseableHttpClient httpClient =
            HttpClients.custom().setDefaultCredentialsProvider(credsProvider).build();
        HttpGet httpGet = new HttpGet(uri);
        // Add AuthCache to the execution context
        HttpClientContext localContext = HttpClientContext.create();
        localContext.setAuthCache(authCache);

        HttpResponse response = httpClient.execute(host, httpGet, localContext);

        return EntityUtils.toString(response.getEntity());
    }

}
```