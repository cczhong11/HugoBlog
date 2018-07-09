---
title: "Solve NoSuchMethodError in Azure JAVA SDK "
date: "2018-07-05"
description: "This blog will tell you how to solve NoSuchMethodError in Java SDK "
categories:
    - "Azure"
    - "Cloud computing"
    - "Java"
---

# Problem

I met a NoSuchMethodError in Azure Java SDK. The error message is like the following:

```java
Exception in thread "main" java.lang.NoSuchMethodError: com.microsoft.azure.credentials.ApplicationTokenCredentials.proxy()Ljava/net/Proxy;
```

It was very strange and I searched on the [stackoverflow](https://stackoverflow.com/questions/41438600/java-lang-nosuchmethoderror-com-microsoft-azure-storage-core-storagecredentials?rq=1). And it shows the problem could be confliction in package version. Therefore I looked into the code to find the reason.

# Solution

In the beginning, I needed to go into the source code to see what happened there. 

```java
// https://github.com/Azure/autorest-clientruntime-for-java/blob/master/azure-client-authentication/src/main/java/com/microsoft/azure/credentials/ApplicationTokenCredentials.java
private AuthenticationResult acquireAccessToken(String resource) throws IOException {
        String authorityUrl = this.environment().activeDirectoryEndpoint() + this.domain();
        ExecutorService executor = Executors.newSingleThreadExecutor();
        AuthenticationContext context = new AuthenticationContext(authorityUrl, false, executor);
        if (proxy() != null) {
            context.setProxy(proxy());
        }
        ....
```

So the problem is it could not find `proxy()`, where it is? I also could not find it in this file. I looked into the javadoc and it extends `AzureTokenCredentials`. Therefore I got into this file and saw the proxy methods.

```java
//https://github.com/Azure/autorest-clientruntime-for-java/blob/master/azure-client-runtime/src/main/java/com/microsoft/azure/credentials/AzureTokenCredentials.java
	/**
     * @return the proxy being used for accessing Active Directory.
     */
    public Proxy proxy() {
        return proxy;
    }
```

So why it could not find it? Maybe it is because package confliction. So I used `mvn dependency:tree` to check all the package version.

```

[INFO] +- junit:junit:jar:3.8.1:test
[INFO] +- com.microsoft.azure:azure-keyvault:jar:1.0.0:compile
[INFO] |  +- com.microsoft.azure:azure-client-runtime:jar:1.0.0:compile
[INFO] |  |  \- com.microsoft.rest:client-runtime:jar:1.0.0:compile
[INFO] |  |     +- com.squareup.retrofit2:retrofit:jar:2.1.0:compile
[INFO] |  |     +- com.squareup.okhttp3:okhttp:jar:3.3.1:compile
[INFO] |  |     |  \- com.squareup.okio:okio:jar:1.8.0:compile
[INFO] |  |     +- com.squareup.okhttp3:logging-interceptor:jar:3.3.1:compile
[INFO] |  |     +- com.squareup.okhttp3:okhttp-urlconnection:jar:3.3.1:compile
[INFO] |  |     +- com.squareup.retrofit2:converter-jackson:jar:2.1.0:compile
[INFO] |  |     +- com.fasterxml.jackson.datatype:jackson-datatype-joda:jar:2.7.2:compile
[INFO] |  |     \- com.squareup.retrofit2:adapter-rxjava:jar:2.1.0:compile
[INFO] |  \- com.microsoft.azure:azure-keyvault-webkey:jar:1.0.0:compile
[INFO] +- com.microsoft.azure:azure:jar:1.8.0:compile
[INFO] |  +- com.microsoft.azure:azure-client-authentication:jar:1.3.1:compile
[INFO] |  |  \- com.microsoft.azure:azure-annotations:jar:1.2.0:compile
[INFO] |  +- com.microsoft.azure:azure-mgmt-resources:jar:1.8.0:compile
[INFO] |  |  +- org.slf4j:slf4j-simple:jar:1.7.5:compile
[INFO] |  |  \- io.reactivex:rxjava:jar:1.2.4:compile
....
```

You could see in `azure-keyvault`, it used `azure-client-runtime:jar:1.0.0`, but in `azure-client-authentication:jar`, it is 1.3.1 version. The `azure-client-runtime` is too old and  that's why it could not find `proxy()`. 

Therefore the solution is to upgrade the version of this `azure-keyvault` package. You could find more infomation in maven official [website](https://mvnrepository.com/artifact/com.microsoft.azure). 

## General Solution

1. Find the package contains that method or class
2. Use `mvn dependency:tree` to get all package information, find package version
3. Check dependency between package.