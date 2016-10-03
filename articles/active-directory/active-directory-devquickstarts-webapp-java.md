<properties
	pageTitle="Azure AD Java の概要 | Microsoft Azure"
	description="サインインに職場または学校アカウントを使用する Java Web アプリケーションを作成する方法。"
	services="active-directory"
	documentationCenter="java"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>


# Azure AD を使用した Java Web アプリのサインインおよびサインアウト

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD を使用すると、数行のコードを追加するだけで、Web アプリの ID 管理を外部委託し、シングル サインインおよびサインアウトを提供することが、簡単に実現できます。Java Web アプリでは、コミュニティ主導型の ADAL4J の Microsoft 実装を使用することで、これを実現できます。

  ここでは、ADAL4J を使用して次のことを行います。
- ID プロバイダーとして Azure AD を使用して、ユーザーをアプリにサインインします。
- ユーザーについての情報を表示します。
- ユーザーをアプリからサインアウトします。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. ADAL4J ライブラリを使用するようにアプリを設定する
3. ADAL4J ライブラリを使用してサインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

最初に、[アプリのスケルトンをダウンロードするか](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip)。アプリケーションの登録先となる Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## 1\.アプリケーションを Azure AD に登録する
アプリケーションがユーザー認証を処理できるようにするには、まず、アプリケーションをテナントに登録する必要があります。

- Microsoft Azure 管理ポータルにサインインします。
- 左側のナビゲーションで **[Active Directory]** をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- **[アプリケーション]** タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    - アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    - **[サインオン URL]** は、アプリのベース URL です。スケルトンの既定値は、`http://localhost:8080/adal4jsample/` です。
    - **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`http://localhost:8080/adal4jsample/`) です。
- 登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、[構成] タブからコピーします。

アプリケーションのポータルで、アプリケーションの**アプリケーション シークレット**を作成し、メモしておきます。このプロジェクトはすぐに必要になります。


## 2\.Maven を使用して、ADAL4J ライブラリと前提条件を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように ADAL4J を構成します。ADAL4J は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-	プロジェクトのルート ディレクトリで、`pom.xml` を開くか作成し、`// TODO: provide dependencies for Maven` を探して次のコードに置き換えます。

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>adal4jsample</artifactId>
	<packaging>war</packaging>
	<version>0.0.1-SNAPSHOT</version>
	<name>adal4jsample</name>
	<url>http://maven.apache.org</url>
	<properties>
		<spring.version>3.0.5.RELEASE</spring.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>com.microsoft.azure</groupId>
			<artifactId>adal4j</artifactId>
			<version>1.1.1</version>
		</dependency>
		<dependency>
			<groupId>com.nimbusds</groupId>
			<artifactId>oauth2-oidc-sdk</artifactId>
			<version>4.5</version>
		</dependency>
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.0.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>1.7.5</version>
		</dependency>
		<!-- Spring 3 dependencies -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
	</dependencies>

	<build>
		<finalName>sample-for-adal4j</finalName>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<warName>${project.artifactId}</warName>
					<source>${project.basedir}\src</source>
					<target>${maven.compiler.target}</target>
					<encoding>utf-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>install</id>
						<phase>install</phase>
						<goals>
							<goal>sources</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.5</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>

```


## 手順 3.Java Web アプリケーション ファイル (WEB-INF) を作成する

ここでは、OpenID Connect 認証プロトコルを使用するように、Java Web アプリを構成します。ADAL4J ライブラリは、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-	まず、`\webapp\WEB-INF` にある `web.xml` ファイルを開き、アプリの構成値を xml に入力します。

ファイルは次のようになります。

```xml
<?xml version="1.0"?>
<web-app id="WebApp_ID" version="2.4"
	xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
	<display-name>Archetype Created Web Application</display-name>
	<context-param>
		<param-name>authority</param-name>
		<param-value>https://login.windows.net/</param-value>
	</context-param>
	<context-param>
		<param-name>tenant</param-name>
		<param-value>YOUR_TENANT_NAME</param-value>
	</context-param>

	<filter>
		<filter-name>BasicFilter</filter-name>
		<filter-class>com.microsoft.aad.adal4jsample.BasicFilter</filter-class>
		<init-param>
			<param-name>client_id</param-name>
			<param-value>YOUR_CLIENT_ID</param-value>
		</init-param>
		<init-param>
			<param-name>secret_key</param-name>
			<param-value>YOUR_CLIENT_SECRET</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>BasicFilter</filter-name>
		<url-pattern>/secure/*</url-pattern>
	</filter-mapping>

	<servlet>
		<servlet-name>mvc-dispatcher</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>mvc-dispatcher</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/mvc-dispatcher-servlet.xml</param-value>
	</context-param>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
</web-app>

```


    -	The `YOUR_CLIENT_ID` is the **Application Id** assigned to your app in the registration portal.
    -	The `YOUR_CLIENT_SECRET` is the **Application Secret**  you created in the portal.
    - The `YOUR_TENANT_NAME` is the **tenant name** of your app, e.g. contoso.onmicrosoft.com

構成パラメーターの残りの部分はそのままにしておきます。

> [AZURE.NOTE]
この XML ファイルからわかるように、ここでは、`mvc-dispatcher` という名前の JSP/サーブレット Web アプリを作成しています。この Web アプリでは、/secure URL にアクセスするたびに `BasicFilter` が使用されます。さらに、後の手順では、保護されたコンテンツの格納場所として /secure を使用し、Azure Active Directory への認証を強制するように構成します。

-	次に、`\webapp\WEB-INF` の下に `mvc-dispatcher-servlet.xml` ファイルを作成し、次の情報を入力します。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans     
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd">

	<context:component-scan base-package="com.microsoft.aad.adal4jsample" />

	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix">
			<value>/</value>
		</property>
		<property name="suffix">
			<value>.jsp</value>
		</property>
	</bean>

</beans>
```

このコードでは、Spring を使用することと、次の手順で記述する .jsp ファイルを検索する場所を Web アプリに指示します。

## 4\.Java JSP ビュー ファイルを作成する (BasicFilter MVC 用)

この段階で、WEB-INF での Web アプリの設定は半分しか終わっていません。次に、構成で示唆した、Web アプリによって実行される実際の Java Server Pages ファイルを作成する必要があります。

前述の xml 構成ファイルでは、.jsp ファイルを読み込む `/` リソースと、`BasicFilter` というフィルターを通過する `/secure` リソースがあることを Java で示しました。

ここでは、これらのリソースを作成します。

-	まず、`\webapp` の下に `index.jsp` ファイルを作成し、次のコードをコピーして貼り付けます。

```jsp
<html>
<body>
	<h2>Hello World!</h2>
	<ul>
	<li><a href="secure/aad">Secure Page</a></li>
	</ul>
</body>
</html>

```

これにより、単に、フィルターによって保護されているセキュリティで保護されたページにリダイレクトされます。

- 次に、同じディレクトリ内に、発生するすべてのエラーを受け取る `error.jsp` ファイルを作成します。

```jsp
<html>
<body>
	<h2>ERROR PAGE!</h2>
	<p>
		Exception -
		<%=request.getAttribute("error")%></p>
	<ul>
		<li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
	</ul>
</body>
</html>
```

- 最後に、`\webapp` の下に `\secure` というフォルダーを作成して、そのセキュリティで保護された Web ページを作成します。これにより、ディレクトリは `\webapp\secure` になります。

- このディレクトリ内に `aad.jsp` ファイルを作成し、次のコードをコピーして貼り付けます。

```jsp
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>AAD Secure Page</title>
</head>
<body>

	<h1>Directory - Users List</h1>
	<p>${users}</p>

	<ul>
		<li><a href="<%=request.getContextPath()%>/secure/aad?cc=1">Get
				new Access Token via Client Credentials</a></li>
	</ul>
	<ul>
		<li><a href="<%=request.getContextPath()%>/secure/aad?refresh=1">Get
				new Access Token via Refresh Token</a></li>
	</ul>
	<ul>
		<li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
	</ul>
</body>
</html>
```

このページは、特定の要求にリダイレクトされることがわかります。これらの要求は、BasicFilter サーブレットによって読み取られた後、`ADAJ4J` ライブラリを使用して実行されます。比較的単純だと思いませんか。

当然ながら、次は、サーブレットが動作するように Java ファイルを設定する必要があります。

## 5\.Java ヘルパー ファイルを作成する (BasicFilter MVC 用)

ここでは、次の操作を行ういくつかの Java ファイルを作成することを目的としています。

1. ユーザーのサインインとサインアウトを許可する。
2. ユーザーに関するデータを取得する。

> [AZURE.NOTE] 
ユーザーに関するデータを取得するには、Azure Active Directory から Graph API を使用する必要があります。Graph API とは、個々のユーザーを含む組織に関するデータを取得するために使用できる、セキュリティで保護された Web サービスです。これは、トークンに機密データを事前に入力するよりも優れています。なぜなら、データを要求するユーザーが承認されていて、(脱獄された電話、デスクトップの Web ブラウザーのキャッシュから) 偶然にトークンを取得した人にユーザーまたは組織に関する重要な詳細情報が漏えいすることがないためです。

それでは、この操作を行うための Java ファイルをいくつか作成しましょう。

1. ルート ディレクトリに adal4jsample フォルダーを作成し、すべての Java ファイルを保存します。

Java ファイルでは、`com.microsoft.aad.adal4jsample` 名前空間を使用します。ほとんどの IDE では、このために入れ子のフォルダー構造が作成されます (たとえば、`/com/microsoft/aad/adal4jsample`)。フォルダー構造の作成は自由ですが、必ずしも必要ではありません。

2. このフォルダー内に、`JSONHelper.java` という名前のファイルを作成します。このファイルは、トークンからの JSON データを解析するために使用します。次のコードをコピーして貼り付けることができます。

```Java

package com.microsoft.aad.adal4jsample;

import java.lang.reflect.Field;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang3.text.WordUtils;
import org.apache.log4j.Logger;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

/**
 * This class provides the methods to parse JSON Data from a JSON Formatted
 * String.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class JSONHelper {

    private static Logger logger = Logger.getLogger(JSONHelper.class);

    JSONHelper() {
        // PropertyConfigurator.configure("log4j.properties");
    }

    /**
     * This method parses an JSON Array out of a collection of JSON Objects
     * within a string.
     * 
     * @param jSonData
     *            The JSON String that holds the collection.
     * @return An JSON Array that would contains all the collection object.
     * @throws Exception
     */
    public static JSONArray fetchDirectoryObjectJSONArray(JSONObject jsonObject) throws Exception {
        JSONArray jsonArray = new JSONArray();
        jsonArray = jsonObject.optJSONObject("responseMsg").optJSONArray("value");
        return jsonArray;
    }

    /**
     * This method parses an JSON Object out of a collection of JSON Objects
     * within a string
     * 
     * @param jsonObject
     * @return An JSON Object that would contains the DirectoryObject.
     * @throws Exception
     */
    public static JSONObject fetchDirectoryObjectJSONObject(JSONObject jsonObject) throws Exception {
        JSONObject jObj = new JSONObject();
        jObj = jsonObject.optJSONObject("responseMsg");
        return jObj;
    }

    /**
     * This method parses the skip token from a json formatted string.
     * 
     * @param jsonData
     *            The JSON Formatted String.
     * @return The skipToken.
     * @throws Exception
     */
    public static String fetchNextSkiptoken(JSONObject jsonObject) throws Exception {
        String skipToken = "";
        // Parse the skip token out of the string.
        skipToken = jsonObject.optJSONObject("responseMsg").optString("odata.nextLink");

        if (!skipToken.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = skipToken.indexOf("$skiptoken=") + (new String("$skiptoken=")).length();
            skipToken = skipToken.substring(index);
        }
        return skipToken;
    }

    /**
     * @param jsonObject
     * @return
     * @throws Exception
     */
    public static String fetchDeltaLink(JSONObject jsonObject) throws Exception {
        String deltaLink = "";
        // Parse the skip token out of the string.
        deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.deltaLink");
        if (deltaLink == null || deltaLink.length() == 0) {
            deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.nextLink");
            logger.info("deltaLink empty, nextLink ->" + deltaLink);

        }
        if (!deltaLink.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = deltaLink.indexOf("deltaLink=") + (new String("deltaLink=")).length();
            deltaLink = deltaLink.substring(index);
        }
        return deltaLink;
    }

    /**
     * This method would create a string consisting of a JSON document with all
     * the necessary elements set from the HttpServletRequest request.
     * 
     * @param request
     *            The HttpServletRequest
     * @return the string containing the JSON document.
     * @throws Exception
     *             If there is any error processing the request.
     */
    public static String createJSONString(HttpServletRequest request, String controller) throws Exception {
        JSONObject obj = new JSONObject();
        try {
            Field[] allFields = Class.forName(
                    "com.microsoft.windowsazure.activedirectory.sdk.graph.models." + controller).getDeclaredFields();
            String[] allFieldStr = new String[allFields.length];
            for (int i = 0; i < allFields.length; i++) {
                allFieldStr[i] = allFields[i].getName();
            }
            List<String> allFieldStringList = Arrays.asList(allFieldStr);
            Enumeration<String> fields = request.getParameterNames();

            while (fields.hasMoreElements()) {

                String fieldName = fields.nextElement();
                String param = request.getParameter(fieldName);
                if (allFieldStringList.contains(fieldName)) {
                    if (param == null || param.length() == 0) {
                        if (!fieldName.equalsIgnoreCase("password")) {
                            obj.put(fieldName, JSONObject.NULL);
                        }
                    } else {
                        if (fieldName.equalsIgnoreCase("password")) {
                            obj.put("passwordProfile", new JSONObject("{"password": "" + param + ""}"));
                        } else {
                            obj.put(fieldName, param);

                        }
                    }
                }
            }
        } catch (JSONException e) {
            e.printStackTrace();
        } catch (SecurityException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return obj.toString();
    }

    /**
     * 
     * @param key
     * @param value
     * @return string format of this JSON obje
     * @throws Exception
     */
    public static String createJSONString(String key, String value) throws Exception {

        JSONObject obj = new JSONObject();
        try {
            obj.put(key, value);
        } catch (JSONException e) {
            e.printStackTrace();
        }

        return obj.toString();
    }

    /**
     * This is a generic method that copies the simple attribute values from an
     * argument jsonObject to an argument generic object.
     * 
     * @param jsonObject
     *            The jsonObject from where the attributes are to be copied.
     * @param destObject
     *            The object where the attributes should be copied into.
     * @throws Exception
     *             Throws a Exception when the operation are unsuccessful.
     */
    public static <T> void convertJSONObjectToDirectoryObject(JSONObject jsonObject, T destObject) throws Exception {

        // Get the list of all the field names.
        Field[] fieldList = destObject.getClass().getDeclaredFields();

        // For all the declared field.
        for (int i = 0; i < fieldList.length; i++) {
            // If the field is of type String, that is
            // if it is a simple attribute.
            if (fieldList[i].getType().equals(String.class)) {
                // Invoke the corresponding set method of the destObject using
                // the argument taken from the jsonObject.
                destObject
                        .getClass()
                        .getMethod(String.format("set%s", WordUtils.capitalize(fieldList[i].getName())),
                                new Class[] { String.class })
                        .invoke(destObject, new Object[] { jsonObject.optString(fieldList[i].getName()) });
            }
        }
    }

    public static JSONArray joinJSONArrays(JSONArray a, JSONArray b) {
        JSONArray comb = new JSONArray();
        for (int i = 0; i < a.length(); i++) {
            comb.put(a.optJSONObject(i));
        }
        for (int i = 0; i < b.length(); i++) {
            comb.put(b.optJSONObject(i));
        }
        return comb;
    }

}

```

3. 次に、`HttpClientHelper.java` という名前のファイルを作成します。このファイルは、AAD エンドポイントからの HTTP データを解析するために使用します。次のコードをコピーして貼り付けることができます。

```Java

package com.microsoft.aad.adal4jsample;

import java.io.BufferedReader;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;

import org.json.JSONException;
import org.json.JSONObject;

/**
 * This is Helper class for all RestClient class.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class HttpClientHelper {

    public HttpClientHelper() {
        super();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        BufferedReader reader = null;
        if (isSuccess) {
            reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        } else {
            reader = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
        }
        StringBuffer stringBuffer = new StringBuffer();
        String line = "";
        while ((line = reader.readLine()) != null) {
            stringBuffer.append(line);
        }

        return stringBuffer.toString();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, String payLoad) throws IOException {

        // Send the http message payload to the server.
        if (payLoad != null) {
            conn.setDoOutput(true);
            OutputStreamWriter osw = new OutputStreamWriter(conn.getOutputStream());
            osw.write(payLoad);
            osw.flush();
            osw.close();
        }

        // Get the message response from the server.
        BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        String line = "";
        StringBuffer stringBuffer = new StringBuffer();
        while ((line = br.readLine()) != null) {
            stringBuffer.append(line);
        }

        br.close();

        return stringBuffer.toString();
    }

    public static byte[] getByteaArrayFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        InputStream is = conn.getInputStream();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buff = new byte[1024];
        int bytesRead = 0;

        while ((bytesRead = is.read(buff, 0, buff.length)) != -1) {
            baos.write(buff, 0, bytesRead);
        }

        byte[] bytes = baos.toByteArray();
        baos.close();
        return bytes;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processResponse(int responseCode, String errorCode, String errorMsg) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        response.put("errorCode", errorCode);
        response.put("errorMsg", errorMsg);

        return response;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processGoodRespStr(int responseCode, String goodRespStr) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (goodRespStr.equalsIgnoreCase("")) {
            response.put("responseMsg", "");
        } else {
            response.put("responseMsg", new JSONObject(goodRespStr));
        }

        return response;
    }

    /**
     * for good response
     * 
     * @param responseCode
     * @param responseMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processBadRespStr(int responseCode, String responseMsg) throws JSONException {

        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (responseMsg.equalsIgnoreCase("")) { // good response is empty string
            response.put("responseMsg", "");
        } else { // bad response is json string
            JSONObject errorObject = new JSONObject(responseMsg).optJSONObject("odata.error");

            String errorCode = errorObject.optString("code");
            String errorMsg = errorObject.optJSONObject("message").optString("value");
            response.put("responseCode", responseCode);
            response.put("errorCode", errorCode);
            response.put("errorMsg", errorMsg);
        }

        return response;
    }

}

```

## 6\.Java Graph API モデル ファイルを作成する (BasicFilter MVC 用)

既に説明したとおり、ここでは Graph API を使用して、ログイン ユーザーに関するデータを取得します。これを簡単に実現するには、**ディレクトリ オブジェクト**を表すファイルと、**ユーザー**を表す個々のファイルの両方を作成し、Java のオブジェクト指向パターンを使用できるようにします。

1. `DirectoryObject.java` という名前のファイルを作成します。このファイルは、DirectoryObject に関する基本的なデータを格納するために使用します (後で他のグラフ クエリを実行する際にこのファイルを自由に使用してかまいません)。次のコードをコピーして貼り付けることができます。

```Java

package com.microsoft.aad.adal4jsample;

/**
 * @author Azure Active Directory Contributor
 *
 */
public abstract class DirectoryObject {
	
	public DirectoryObject() {
		super();
	}
	
	/**
	 * 
	 * @return
	 */
	public abstract String getObjectId();
	
	/**
	 * @param objectId
	 */
	public abstract void setObjectId(String objectId);

	/**
	 * 
	 * @return
	 */
	public abstract String getObjectType();

	/**
	 * 
	 * @param objectType
	 */
	public abstract void setObjectType(String objectType);
	
	/**
	 * 
	 * @return
	 */
	public abstract String getDisplayName();

	/**
	 * 
	 * @param displayName
	 */
	public abstract void setDisplayName(String displayName);

}

```

2. `User.java` という名前のファイルを作成します。このファイルは、ディレクトリの任意のユーザーに関する基本的なデータを格納するために使用します。また、これはディレクトリ データ用の非常に基本的な getter と setter であるため、次のコードをコピーして貼り付けることができます。

```Java

package com.microsoft.aad.adal4jsample;

import java.security.acl.Group;
import java.util.ArrayList;

import javax.xml.bind.annotation.XmlRootElement;

import org.json.JSONObject;

/**
 *  The User Class holds together all the members of a WAAD User entity and all the access methods and set methods
 *  @author Azure Active Directory Contributor
 */
@XmlRootElement
public class User extends DirectoryObject{
	
	// The following are the individual private members of a User object that holds
	// a particular simple attribute of an User object.
	protected String objectId;
	protected String objectType;
	protected String accountEnabled;
	protected String city;
	protected String country;
	protected String department;
	protected String dirSyncEnabled;
	protected String displayName;
	protected String facsimileTelephoneNumber;
	protected String givenName;
	protected String jobTitle;
	protected String lastDirSyncTime;
	protected String mail;
	protected String mailNickname;
	protected String mobile;
	protected String password;
	protected String passwordPolicies;
	protected String physicalDeliveryOfficeName;
	protected String postalCode;
	protected String preferredLanguage;
	protected String state;
	protected String streetAddress;
	protected String surname;
	protected String telephoneNumber;
	protected String usageLocation;
	protected String userPrincipalName;
	protected boolean isDeleted;  // this will move to dto

	/**
	 * below 4 properties are for future use
	 */
	// managerDisplayname of this user
	protected String managerDisplayname;
	
	// The directReports holds a list of directReports
	private ArrayList<User> directReports;
	
	// The groups holds a list of group entity this user belongs to. 
	private ArrayList<Group> groups;
	
	// The roles holds a list of role entity this user belongs to. 
	private ArrayList<Group> roles;
	
	
	/**
	 * The constructor for the User class. Initializes the dynamic lists and managerDisplayname variables.
	 */
	public User(){
		directReports = null;
		groups = new ArrayList<Group>();
		roles = new ArrayList<Group>();
		managerDisplayname = null;
	}
//	
//	public User(String displayName, String objectId){
//		setDisplayName(displayName);
//		setObjectId(objectId);
//	}
//	
//	public User(String displayName, String objectId, String userPrincipalName, String accountEnabled){
//		setDisplayName(displayName);
//		setObjectId(objectId);
//		setUserPrincipalName(userPrincipalName);
//		setAccountEnabled(accountEnabled);
//	}
//	

	/**
	 * @return The objectId of this user.
	 */
	public String getObjectId() {
		return objectId;
	}
	
	/**
	 * @param objectId The objectId to set to this User object.
	 */
	public void setObjectId(String objectId) {
		this.objectId = objectId;
	}


	/**
	 * @return The objectType of this User.
	 */
	public String getObjectType() {
		return objectType;
	}

	/**
	 * @param objectType The objectType to set to this User object.
	 */
	public void setObjectType(String objectType) {
		this.objectType = objectType;
	}

	/**
	 * @return The userPrincipalName of this User.
	 */
	public String getUserPrincipalName() {
		return userPrincipalName;
	}

	/**
	 * @param userPrincipalName The userPrincipalName to set to this User object.
	 */
	public void setUserPrincipalName(String userPrincipalName) {
		this.userPrincipalName = userPrincipalName;
	}

	
	/**
	 * @return The usageLocation of this User.
	 */
	public String getUsageLocation() {
		return usageLocation;
	}

	/**
	 * @param usageLocation The usageLocation to set to this User object.
	 */
	public void setUsageLocation(String usageLocation) {
		this.usageLocation = usageLocation;
	}

	/**
	 * @return The telephoneNumber of this User.
	 */
	public String getTelephoneNumber() {
		return telephoneNumber;
	}

	/**
	 * @param telephoneNumber The telephoneNumber to set to this User object.
	 */
	public void setTelephoneNumber(String telephoneNumber) {
		this.telephoneNumber = telephoneNumber;
	}

	/**
	 * @return The surname of this User.
	 */
	public String getSurname() {
		return surname;
	}

	/**
	 * @param surname The surname to set to this User Object.
	 */
	public void setSurname(String surname) {
		this.surname = surname;
	}

	/**
	 * @return The streetAddress of this User.
	 */
	public String getStreetAddress() {
		return streetAddress;
	}

	/**
	 * @param streetAddress The streetAddress to set to this User.
	 */
	public void setStreetAddress(String streetAddress) {
		this.streetAddress = streetAddress;
	}

	/**
	 * @return The state of this User.
	 */
	public String getState() {
		return state;
	}

	/**
	 * @param state The state to set to this User object.
	 */
	public void setState(String state) {
		this.state = state;
	}

	/**
	 * @return The preferredLanguage of this User.
	 */
	public String getPreferredLanguage() {
		return preferredLanguage;
	}

	/**
	 * @param preferredLanguage The preferredLanguage to set to this User.
	 */
	public void setPreferredLanguage(String preferredLanguage) {
		this.preferredLanguage = preferredLanguage;
	}

	/**
	 * @return The postalCode of this User.
	 */
	public String getPostalCode() {
		return postalCode;
	}

	/**
	 * @param postalCode The postalCode to set to this User.
	 */
	public void setPostalCode(String postalCode) {
		this.postalCode = postalCode;
	}

	/**
	 * @return The physicalDeliveryOfficeName of this User.
	 */
	public String getPhysicalDeliveryOfficeName() {
		return physicalDeliveryOfficeName;
	}

	/**
	 * @param physicalDeliveryOfficeName The physicalDeliveryOfficeName to set to this User Object.
	 */
	public void setPhysicalDeliveryOfficeName(String physicalDeliveryOfficeName) {
		this.physicalDeliveryOfficeName = physicalDeliveryOfficeName;
	}

	/**
	 * @return The passwordPolicies of this User.
	 */
	public String getPasswordPolicies() {
		return passwordPolicies;
	}

	/**
	 * @param passwordPolicies The passwordPolicies to set to this User object.
	 */
	public void setPasswordPolicies(String passwordPolicies) {
		this.passwordPolicies = passwordPolicies;
	}

	/**
	 * @return The mobile of this User.
	 */
	public String getMobile() {
		return mobile;
	}

	/**
	 * @param mobile The mobile to set to this User object.
	 */
	public void setMobile(String mobile) {
		this.mobile = mobile;
	}
	
	/**
	 * @return The Password of this User.
	 */
	public String getPassword() {
		return password;
	}

	/**
	 * @param password The mobile to set to this User object.
	 */
	public void setPassword(String password) {
		this.password = password;
	}

	/**
	 * @return The mail of this User.
	 */
	public String getMail() {
		return mail;
	}

	/**
	 * @param mail The mail to set to this User object.
	 */
	public void setMail(String mail) {
		this.mail = mail;
	}
	
	/**
	 * @return The MailNickname of this User.
	 */
	public String getMailNickname() {
		return mailNickname;
	}

	/**
	 * @param mail The MailNickname to set to this User object.
	 */
	public void setMailNickname(String mailNickname) {
		this.mailNickname = mailNickname;
	}


	/**
	 * @return The jobTitle of this User.
	 */
	public String getJobTitle() {
		return jobTitle;
	}

	/**
	 * @param jobTitle The jobTitle to set to this User Object.
	 */
	public void setJobTitle(String jobTitle) {
		this.jobTitle = jobTitle;
	}

	/**
	 * @return The givenName of this User.
	 */
	public String getGivenName() {
		return givenName;
	}

	/**
	 * @param givenName The givenName to set to this User.
	 */
	public void setGivenName(String givenName) {
		this.givenName = givenName;
	}

	/**
	 * @return The facsimileTelephoneNumber of this User.
	 */
	public String getFacsimileTelephoneNumber() {
		return facsimileTelephoneNumber;
	}

	/**
	 * @param facsimileTelephoneNumber The facsimileTelephoneNumber to set to this User Object.
	 */
	public void setFacsimileTelephoneNumber(String facsimileTelephoneNumber) {
		this.facsimileTelephoneNumber = facsimileTelephoneNumber;
	}

	/**
	 * @return The displayName of this User.
	 */
	public String getDisplayName() {
		return displayName;
	}

	/**
	 * @param displayName The displayName to set to this User Object.
	 */
	public void setDisplayName(String displayName) {
		this.displayName = displayName;
	}

	/**
	 * @return The dirSyncEnabled of this User.
	 */
	public String getDirSyncEnabled() {
		return dirSyncEnabled;
	}

	/**
	 * @param dirSyncEnabled The dirSyncEnabled to set to this User.
	 */
	public void setDirSyncEnabled(String dirSyncEnabled) {
		this.dirSyncEnabled = dirSyncEnabled;
	}

	/**
	 * @return The department of this User.
	 */
	public String getDepartment() {
		return department;
	}

	/**
	 * @param department The department to set to this User.
	 */
	public void setDepartment(String department) {
		this.department = department;
	}

	/**
	 * @return The lastDirSyncTime of this User.
	 */
	public String getLastDirSyncTime() {
		return lastDirSyncTime;
	}

	/**
	 * @param lastDirSyncTime The lastDirSyncTime to set to this User.
	 */
	public void setLastDirSyncTime(String lastDirSyncTime) {
		this.lastDirSyncTime = lastDirSyncTime;
	}

	/**
	 * @return The country of this User.
	 */
	public String getCountry() {
		return country;
	}

	/**
	 * @param country The country to set to this User.
	 */
	public void setCountry(String country) {
		this.country = country;
	}

	/**
	 * @return The city of this User.
	 */
	public String getCity() {
		return city;
	}

	/**
	 * @param city The city to set to this User.
	 */
	public void setCity(String city) {
		this.city = city;
	}

	/**
	 * @return The accountEnabled attribute of this User.
	 */
	public String getAccountEnabled() {
		return accountEnabled;
	}

	/**
	 * @param accountEnabled The accountEnabled to set to this User.
	 */
	public void setAccountEnabled(String accountEnabled) {
		this.accountEnabled = accountEnabled;
	}
	
	public boolean isIsDeleted() {
		return this.isDeleted;
	}

	public void setIsDeleted(boolean isDeleted) {
		this.isDeleted = isDeleted;
	}

	@Override
	public String toString() {
		return new JSONObject(this).toString();
	}
	
	public String getManagerDisplayname(){
		return managerDisplayname;
	}
	
	public void setManagerDisplayname(String managerDisplayname){
		this.managerDisplayname = managerDisplayname;
	}
}


/**
 * The Class DirectReports Holds the essential data for a single DirectReport entry. Namely,
 * it holds the displayName and the objectId of the direct entry. Furthermore, it provides the
 * access methods to set or get the displayName and the ObjectId of this entry.
 */
//class DirectReport extends User{
//
//	private String displayName;
//	private String objectId;
//	 
//	/**
//	 * Two arguments Constructor for the DirectReport Class.
//	 * @param displayName
//	 * @param objectId
//	 */
//	public DirectReport(String displayName, String objectId){
//		this.displayName = displayName;
//		this.objectId = objectId;
//	}
//
//	/**
//	 * @return The diaplayName of this direct report entry.
//	 */
//	public String getDisplayName() {
//		return displayName;
//	}
//
//	
//	/**
//	 *  @return The objectId of this direct report entry. 
//	 */
//	public String getObjectId() {
//		return objectId;
//	}
//
//}

```

## 7\.認証モデルとコントローラーのファイルを作成する (BasicFilter 用)

Java の記述は確かに冗長ですが、もう少しで完了です。最後から 2 番目の手順として、要求を処理する BasicFilter サーブレットを記述する前に、`ADAL4J` ライブラリで必要となるいくつかのヘルパー ファイルを追加で作成します。

1. `AuthHelper.java` という名前のファイルを作成します。このファイルでは、ログイン ユーザーの状態を確認するために使用するメソッドが含まれます。学習した内容は次のとおりです。

- `isAuthenticated()` メソッド: ユーザーがログインしているかどうかを返します。
- `containsAuthenticationData()`: トークンにデータが含まれているかどうかを返します。
- `isAuthenticationSuccessful()`: ユーザーの認証が成功したかどうかを返します。

次のコードをコピーして貼り付けます。

```Java
package com.microsoft.aad.adal4jsample;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;

import com.microsoft.aad.adal4j.AuthenticationResult;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public final class AuthHelper {

    public static final String PRINCIPAL_SESSION_NAME = "principal";

    private AuthHelper() {
    }

    public static boolean isAuthenticated(HttpServletRequest request) {
        return request.getSession().getAttribute(PRINCIPAL_SESSION_NAME) != null;
    }

    public static AuthenticationResult getAuthSessionObject(
            HttpServletRequest request) {
        return (AuthenticationResult) request.getSession().getAttribute(
                PRINCIPAL_SESSION_NAME);
    }

    public static boolean containsAuthenticationData(
            HttpServletRequest httpRequest) {
        Map<String, String[]> map = httpRequest.getParameterMap();
        return httpRequest.getMethod().equalsIgnoreCase("POST") && (httpRequest.getParameterMap().containsKey(
                        AuthParameterNames.ERROR)
                        || httpRequest.getParameterMap().containsKey(
                                AuthParameterNames.ID_TOKEN) || httpRequest
                        .getParameterMap().containsKey(AuthParameterNames.CODE));
    }

    public static boolean isAuthenticationSuccessful(
            AuthenticationResponse authResponse) {
        return authResponse instanceof AuthenticationSuccessResponse;
    }
}
```

2. `AuthParameterNames.java` という名前のファイルを作成します。このファイルでは、`ADAL4J` で必要となる変更不可の変数を定義するために使用します。次のコードをコピーして貼り付けます。

```Java
package com.microsoft.aad.adal4jsample;

public final class AuthParameterNames {

    private AuthParameterNames() {
    }

    public static String ERROR = "error";
    public static String ERROR_DESCRIPTION = "error_description";
    public static String ERROR_URI = "error_uri";
    public static String ID_TOKEN = "id_token";
    public static String CODE = "code";
}
```

3. 最後に、MVC パターンのコントローラーとなる `AadController.java` という名前のファイルを作成します。このファイルを使用して、JSP コントローラーを定義し、アプリの `secure/aad` URL エンドポイントを公開します。さらに、このファイルにはグラフ クエリも配置します。

次のコードをコピーして貼り付けます。

```Java
package com.microsoft.aad.adal4jsample;

import java.net.HttpURLConnection;
import java.net.URL;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.json.JSONArray;
import org.json.JSONObject;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.microsoft.aad.adal4j.AuthenticationResult;

@Controller
@RequestMapping("/secure/aad")
public class AadController {

    @RequestMapping(method = { RequestMethod.GET, RequestMethod.POST })
    public String getDirectoryObjects(ModelMap model, HttpServletRequest httpRequest) {
        HttpSession session = httpRequest.getSession();
        AuthenticationResult result = (AuthenticationResult) session.getAttribute(AuthHelper.PRINCIPAL_SESSION_NAME);
        if (result == null) {
            model.addAttribute("error", new Exception("AuthenticationResult not found in session."));
            return "/error";
        } else {
            String data;
            try {
                data = this.getUsernamesFromGraph(result.getAccessToken(), session.getServletContext()
                        .getInitParameter("tenant"));
                model.addAttribute("users", data);
            } catch (Exception e) {
                model.addAttribute("error", e);
                return "/error";
            }
        }
        return "/secure/aad";
    }

    private String getUsernamesFromGraph(String accessToken, String tenant) throws Exception {
        URL url = new URL(String.format("https://graph.windows.net/%s/users?api-version=2013-04-05", tenant,
                accessToken));

        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        // Set the appropriate header fields in the request header.
        conn.setRequestProperty("api-version", "2013-04-05");
        conn.setRequestProperty("Authorization", accessToken);
        conn.setRequestProperty("Accept", "application/json;odata=minimalmetadata");
        String goodRespStr = HttpClientHelper.getResponseStringFromConn(conn, true);
        // logger.info("goodRespStr ->" + goodRespStr);
        int responseCode = conn.getResponseCode();
        JSONObject response = HttpClientHelper.processGoodRespStr(responseCode, goodRespStr);
        JSONArray users = new JSONArray();

        users = JSONHelper.fetchDirectoryObjectJSONArray(response);

        StringBuilder builder = new StringBuilder();
        User user = null;
        for (int i = 0; i < users.length(); i++) {
            JSONObject thisUserJSONObject = users.optJSONObject(i);
            user = new User();
            JSONHelper.convertJSONObjectToDirectoryObject(thisUserJSONObject, user);
            builder.append(user.getUserPrincipalName() + "<br/>");
        }
        return builder.toString();
    }

}

```

## 8\.BasicFilter ファイルを作成する (BasicFilter MVC 用)

最終的に、ビュー (JSP ファイル) からの要求を処理する BasicFilter ファイルを作成する準備ができました。

次のコードを含む `BasicFilter.java` という名前のファイルを作成します。

```Java

package com.microsoft.aad.adal4jsample;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URI;
import java.net.URLEncoder;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;
import com.microsoft.aad.adal4j.ClientCredential;
import com.nimbusds.oauth2.sdk.AuthorizationCode;
import com.nimbusds.openid.connect.sdk.AuthenticationErrorResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public class BasicFilter implements Filter {

    private String clientId = "";
    private String clientSecret = "";
    private String tenant = "";
    private String authority;

    public void destroy() {

    }

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {

        if (request instanceof HttpServletRequest) {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            try {

                String currentUri = request.getScheme()
                        + "://"
                        + request.getServerName()
                        + ("http".equals(request.getScheme())
                                && request.getServerPort() == 80
                                || "https".equals(request.getScheme())
                                && request.getServerPort() == 443 ? "" : ":"
                                + request.getServerPort())
                        + httpRequest.getRequestURI();
                String fullUrl = currentUri
                        + (httpRequest.getQueryString() != null ? "?"
                                + httpRequest.getQueryString() : "");
                // check if user has a session
                if (!AuthHelper.isAuthenticated(httpRequest)) {
                    if (AuthHelper.containsAuthenticationData(httpRequest)) {
                        Map<String, String> params = new HashMap<String, String>();
                        for (String key : request.getParameterMap().keySet()) {
                            params.put(key,
                                    request.getParameterMap().get(key)[0]);
                        }
                        AuthenticationResponse authResponse = AuthenticationResponseParser
                                .parse(new URI(fullUrl), params);
                        if (AuthHelper.isAuthenticationSuccessful(authResponse)) {

                            AuthenticationSuccessResponse oidcResponse = (AuthenticationSuccessResponse) authResponse;
                            AuthenticationResult result = getAccessToken(
                                    oidcResponse.getAuthorizationCode(),
                                    currentUri);
                            createSessionPrincipal(httpRequest, result);
                        } else {
                            AuthenticationErrorResponse oidcResponse = (AuthenticationErrorResponse) authResponse;
                            throw new Exception(String.format(
                                    "Request for auth code failed: %s - %s",
                                    oidcResponse.getErrorObject().getCode(),
                                    oidcResponse.getErrorObject()
                                            .getDescription()));
                        }
                    } else {
                            // not authenticated
                            httpResponse.setStatus(302);
                            httpResponse
                                    .sendRedirect(getRedirectUrl(currentUri));
                            return;
                    }
                } else {
                    // if authenticated, how to check for valid session?
                    AuthenticationResult result = AuthHelper
                            .getAuthSessionObject(httpRequest);

                    if (httpRequest.getParameter("refresh") != null) {
                        result = getAccessTokenFromRefreshToken(
                                result.getRefreshToken(), currentUri);
                    } else {
                        if (httpRequest.getParameter("cc") != null) {
                            result = getAccessTokenFromClientCredentials();
                        } else {
                            if (result.getExpiresOnDate().before(new Date())) {
                                result = getAccessTokenFromRefreshToken(
                                        result.getRefreshToken(), currentUri);
                            }
                        }
                    }
                    createSessionPrincipal(httpRequest, result);
                }
            } catch (Throwable exc) {
                httpResponse.setStatus(500);
                request.setAttribute("error", exc.getMessage());
                httpResponse.sendRedirect(((HttpServletRequest) request)
                        .getContextPath() + "/error.jsp");
            }
        }
        chain.doFilter(request, response);
    }

    private AuthenticationResult getAccessTokenFromClientCredentials()
            throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", new ClientCredential(clientId,
                            clientSecret), null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private AuthenticationResult getAccessTokenFromRefreshToken(
            String refreshToken, String currentUri) throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByRefreshToken(refreshToken,
                            new ClientCredential(clientId, clientSecret), null,
                            null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;

    }

    private AuthenticationResult getAccessToken(
            AuthorizationCode authorizationCode, String currentUri)
            throws Throwable {
        String authCode = authorizationCode.getValue();
        ClientCredential credential = new ClientCredential(clientId,
                clientSecret);
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByAuthorizationCode(authCode, new URI(
                            currentUri), credential, null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private void createSessionPrincipal(HttpServletRequest httpRequest,
            AuthenticationResult result) throws Exception {
        httpRequest.getSession().setAttribute(
                AuthHelper.PRINCIPAL_SESSION_NAME, result);
    }

    private String getRedirectUrl(String currentUri)
            throws UnsupportedEncodingException {
        String redirectUrl = authority
                + this.tenant
                + "/oauth2/authorize?response_type=code%20id_token&scope=openid&response_mode=form_post&redirect_uri="
                + URLEncoder.encode(currentUri, "UTF-8") + "&client_id="
                + clientId + "&resource=https%3a%2f%2fgraph.windows.net"
                + "&nonce=" + UUID.randomUUID() + "&site_id=500879";
        return redirectUrl;
    }

    public void init(FilterConfig config) throws ServletException {
        clientId = config.getInitParameter("client_id");
        authority = config.getServletContext().getInitParameter("authority");
        tenant = config.getServletContext().getInitParameter("tenant");
        clientSecret = config.getInitParameter("secret_key");
    }

}
```

このサーブレットは、`ADAL4J` を実行するためにアプリケーションに求められるすべてのメソッドを公開します。次のトピックがあります。

- `getAccessTokenFromClientCredentials()` - シークレットからアクセス トークンを取得します。
- `getAccessTokenFromRefreshToken()` - 更新トークンからアクセス トークンを取得します。
- `getAccessToken()` - OpenID Connect フローからアクセス トークンを取得します (ここでは、これを使用します)。
- `createSessionPrincipal()` - Graph API へのアクセスに使用するプリンシパルを作成します。
- `getRedirectUrl()` - ポータルに入力した値と比較する redirectURL を取得します。

##サンプルをコンパイルして Tomcat で実行する

ルート ディレクトリに戻り、次のコマンドを実行して、`maven` を使用して作成したサンプルをビルドします。この場合、依存関係用に作成した `pom.xml` ファイルが使用されます。

`$ mvn package`

これで、`/targets` ディレクトリに `adal4jsample.war` ファイルが作成されます。そのファイルを Tomcat コンテナーにデプロイし、次の URL にアクセスします。

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
最新の Tomcat サーバーで WAR をデプロイするのは非常に簡単です。`http://localhost:8080/manager/` に移動し、``adal4jsample.war` ファイルのアップロードに関する指示に従うだけです。ファイルは適切なエンドポイントに自動的にデプロイされます。

##次のステップ

お疲れさまでした。 これで、作業中の Java アプリケーションで、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。テナントに一連のユーザーを設定します (設定していない場合)。

参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

<!---HONumber=AcomDC_0921_2016-->