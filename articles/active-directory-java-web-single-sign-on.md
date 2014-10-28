<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin"></tags>

# Java と Azure Active Directory による Web シングル サインオン

## <a name="introduction"></a> はじめに

このチュートリアルでは Java 開発者向けに、Azure Active Directory を活用して、Office 365 顧客のユーザーに対してシングル サインオンを有効にする方法について説明します。学習内容:

-   顧客のテナントでの Web アプリケーションのプロビジョニング
-   WS-Federation を使用したアプリケーションの保護

### 前提条件

このチュートリアルでは特定のアプリケーション サーバーを使用しますが、経験豊富な Java 開発者であれば、この後に説明するプロセスは他の環境でも同様に適用できます。次の開発環境の前提条件はこのチュートリアルで必要になります。

-   [Azure Active Directory 用の Java サンプル コード][Azure Active Directory 用の Java サンプル コード]
-   [Java Runtime Environment 1.6][Java Runtime Environment 1.6]
-   [JBoss Application Server version 7.1.1.Final][JBoss Application Server version 7.1.1.Final]
-   [JBoss Developer Studio IDE][JBoss Developer Studio IDE]
-   インターネット インフォメーション サービス (IIS) 7.5 (SSL が有効)
-   Windows PowerShell
-   [Office 365 PowerShell コマンドレット][Office 365 PowerShell コマンドレット]

### 目次

-   [はじめに][はじめに]
-   [手順 1.Java アプリケーションを作成する][手順 1.Java アプリケーションを作成する]
-   [手順 2.会社のディレクトリ テナントでアプリケーションをプロビジョニングする][手順 2.会社のディレクトリ テナントでアプリケーションをプロビジョニングする]
-   [手順 3.WS-Federation を従業員のサインインに使用してアプリケーションを保護する][手順 3.WS-Federation を従業員のサインインに使用してアプリケーションを保護する]
-   [まとめ][まとめ]

## <a name="createapp"></a>手順 1.Java アプリケーションの作成

この手順では、リソースが保護されたシンプルな Java アプリケーションを作成する方法について説明します。このリソースへのアクセスは、会社の STS によって管理されるフェデレーション認証を使用して許可されます (後で説明)。

1.  JBoss Developer Studio の新しいインスタンスを開きます。
2.  **[File]** メニューの **[New]** をクリックし、**[Project]** をクリックします。
3.  **[New Project]** ダイアログ ボックスで、**Maven** フォルダーを展開し、**[Maven Project]**、**[Next]** の順にクリックします。
4.  **[New Maven Project]** ダイアログ ボックスで、**[Create a simple project (skip archetype selection)]** チェック ボックスをオンにして、**[Next]** をクリックします。
5.  **[New Maven Project]** ダイアログ ボックスの次のページで、**[Group Id]** と **[Artifact Id]** テキスト ボックスに「*sample*」と入力します。次に、**[Packaging]** ドロップダウン メニューの **[war]** を選択し、**[Finish]** をクリックします。
6.  新しい Maven プロジェクトが作成されます。左側の **[Project Explorer]** メニューで、**sample** プロジェクトを展開し、**pom.xml** ファイルを右クリックして、**[Open With]**、**[Text Editor]** の順にクリックします。
7.  **pom.xml** ファイルで、*\<project\>* セクション内に次の XML を追加します。

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    この XML の入力後、**pom.xml** ファイルを保存します。

8.  **sample** プロジェクトを右クリックし、**[Maven]**、**[Update Maven Project]** の順にクリックします。**[Update Maven Project]** ダイアログ ボックスで **[OK]** をクリックします。この手順では **pom.xml** の変更でプロジェクトが更新されます。

9.  **sample** プロジェクトを右クリックし、**[New]**、**[JSP File]** の順にクリックします。

10. **[New JSP File]** ダイアログで、新しいファイルのパスを *sample/src/main/webapp* に変更します。次に、ファイルに **index.jsp** という名前を付け、**[Finish]** をクリックします。

11. 新しい **index.jsp** ファイルが自動的に開きます。自動的に生成されたコードを次のコードに置き換え、ファイルを保存します。

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. **sample** プロジェクトを右クリックして、**[Run As]**、**[Run on Server]** の順にクリックします。

13. **[Run On Server]** ダイアログ ボックスで、**[JBoss Enterprise Application Platform 6.x]** が選択されていることを確認し、**[Finished]** をクリックします。

## <a name="provisionapp"></a>手順 2.会社のディレクトリ テナントでアプリケーションをプロビジョニングする

この手順では、Azure Active Directory 顧客の管理者がテナントで Java アプリケーションをプロビジョニングし、シングル サインオンを構成する方法について説明します。この手順の完了後、会社の従業員は Office 365 アカウントを使用して Web アプリケーションに対して認証できるようになります。

プロビジョニング プロセスは、アプリケーション用に新しいサービス プリンシパルを作成することから始まります。サービス プリンシパルは Azure Active Directory によって使用されて、アプリケーションがディレクトリに対して登録および認証されます。

1.  Office 365 PowerShell コマンドレットをダウンロードしてインストールします (まだインストールしていない場合)。
2.  **[スタート]** メニューから **Microsoft Online Services Module for Windows PowerShell** コンソールを実行します。このコンソールには、Office 365 テナントの属性を設定するためのコマンド ライン環境が用意されています。たとえば、サービス プリンシパルの作成や変更などが可能です。
3.  必要な **MSOnlineExtended** モジュールをインポートするには、次のコマンドを入力し、Enter キーを押します。

        Import-Module MSOnlineExtended -Force

4.  Office 365 ディレクトリに接続するには、会社の管理者の資格情報を指定する必要があります。次のコマンドを入力して Enter キーを押したら、プロンプトで資格情報を入力します。

        Connect-MsolService

5.  この時点でアプリケーション用に新しいサービス プリンシパルを作成します。次のコマンドを入力し、Enter キーを押します。

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    この手順では、次のような出力が表示されます。

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > この出力のうち特に生成された対称キーを保存しておく必要があります。このキーはサービス プリンシパルの作成時にしか表示されず、以降に取得することはできません。その他の値は、グラフ API を使用してディレクトリ内の情報を読み書きするために必要です。

6.  最後の手順では、アプリケーションの応答 URL を設定します。応答 URL は、認証の試行後に応答が送信される場所です。次のコマンドを入力し、Enter キーを押します。

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

これで、Web アプリケーションはディレクトリでプロビジョニングされ、会社の従業員が Web シングル サインオンに使用できるようになりました。

## <a name="protectapp"></a>手順 3.WS-Federation を従業員のサインインに使用してアプリケーションを保護する

この手順では、前提条件にあるサンプル コード パッケージの一部としてダウンロードした Windows Identity Foundation (WIF) と **waad-federation** ライブラリを使用して、フェデレーテッド ログインのサポートを追加する方法について説明します。また、ログイン ページを追加し、アプリケーションとディレクトリ テナントとの信頼を構成します。

1.  JBoss Developer Studio で、**[File]**、**[Import]** の順にクリックします。

2.  **[Import]** ダイアログ ボックスで、**Maven** フォルダーを展開し、**[Existing Maven Projects]**、**[Next]** の順にクリックします。

3.  **[Import Maven Projects]** ダイアログ ボックスで、**[Root Directory]** パスを、**waad-federation** ライブラリ (サンプル コード パッケージ内) をダウンロードした場所に設定します。次に、**waad-federation** プロジェクトの **pom.xml** ファイルの横にあるチェック ボックスをオンにし、**[Finish]** をクリックします。

4.  **sample** プロジェクトを展開し、**pom.xml** ファイルを右クリックして、**[Open With]**、**[Text Editor]** の順にクリックします。

5.  **pom.xml** ファイルで、*\<project\>* セクション内に次の XML を追加し、ファイルを保存します。

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  **sample** プロジェクトを右クリックし、**[Maven]**、**[Update Project]** の順にクリックします。**[Update Maven Project]** ダイアログ ボックスで **[OK]** をクリックします。この手順では **pom.xml** の変更でプロジェクトが更新されます。

7.  **sample** プロジェクトを右クリックし、**[New]**、**[Filter]** の順にクリックします。

8.  **[Create Filter]** ダイアログ ボックスで、**[Class name]** エントリに「*FederationFilter*」と入力し、**[Finish]** をクリックします。

9.  自動的に生成された **FederationFilter.java** ファイルが開きます。そのファイルのコードを次のコードに置き換え、ファイルを保存します。

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. **Project Explorer** で、**src**、**main**、**webapp** フォルダーの順に展開します。**web.xml** ファイルを右クリックし、**[Open With]**、**[Text Editor]** の順にクリックします。

11. **web.xml** ファイルで、セキュリティで保護されているページと保護されていないページを処理するためのフィルターを追加します。また、そのフィルターでは、認証されていないユーザーがログイン ページ (**login-page-url** フィルター パラメーターで指定) にリダイレクトされます。ただし、URL が **allowed-regex** フィルター パラメーターで指定した正規表現に一致した場合、その URL はフィルター処理されます。*\<web-app\>* セクション内に次の XML を追加し、**web.xml** ファイルを保存します。

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. ログイン ページを作成するために、**sample** プロジェクトを右クリックし、**[New]**、**[JSP File]** の順にクリックします。

13. **[New JSP File]** ダイアログで、新しいファイルのパスを *sample/src/main/webapp* に変更します。次に、ファイルに **login.jsp** という名前を付け、**[Finish]** をクリックします。

14. 新しい **login.jsp** ファイルが自動的に開きます。自動的に生成されたコードを次のコードに置き換え、ファイルを保存します。

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. **Project Explorer** で、**sample** プロジェクトの **/src/main** フォルダーを展開します。**resources** フォルダーを右クリックし、**[New]**、**[Other]** の順にクリックします。

16. **[New]** ダイアログ ボックスで、**JBoss Tools Web** フォルダーを展開し、**[Properties File]**、**[Next]** の順にクリックします。

17. **[New File Properties]** ダイアログ ボックスで、ファイルに **federation** という名前を付け、**[Finish]** をクリックします。

18. **Project Explorer** で、**sample** プロジェクトの **src/main/resources** フォルダーを展開します。**federation.properties** ファイルを右クリックし、**[Open With]**、**[Text Editor]** の順にクリックします。

19. **federation.properties** ファイルで、次の構成エントリを追加し、ファイルを保存します。

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    > **audienceuris** と **realm** の値の先頭は "spn:" にする必要があります。

20. この時点で、新しいサーブレットを作成する必要があります。**sample** プロジェクトを右クリックし、**[New]**、**[Other]**、**[Servlet]** の順にクリックします。

21. **[Create Servlet]** ダイアログ ボックスで、*FederationServlet* の**クラス名**を指定し、**[Finish]** をクリックします。

22. **FederationServlet.java** ファイルが自動的に開かれます。そのファイルの内容を次のコードに置き換え、ファイルを保存します。

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. **Project Explorer** で、**src/main/webapp/WEB-INF** フォルダーを展開します。**web.xml** ファイルを右クリックし、**[Open With]**、**[Text Editor]** の順にクリックします。

24. **web.xml** ファイルで、*\<url-pattern\>* セクション内の **/FederationServlet** 設定を **/ws-saml** に置き換えます。次に例を示します。

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. **Project Explorer** で、**src/main/webapp** フォルダーを展開します。**index.jsp** ファイルを右クリックし、**[Open With]**、**[Text Editor]** の順にクリックします。

26. **index.jsp** ファイルで、既存のコードを次のコードに置き換え、ファイルを保存します。

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. **Project Explorer** で、**src/main/webapp/WEB-INF** フォルダーを展開します。**web.xml** ファイルを右クリックし、**[Open With]**、**[Text Editor]** の順にクリックします。

28. この時点で、アプリケーションの SSL を有効にします。**web.xml** ファイルで、*\<web-app\>* セクション内に次の *\<security-constraint\>* セクションを挿入し、ファイルを保存します。

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > 次の手順に進む前に、SSL をサポートするように JBoss Server を既に構成していることを確認してください。

29. これで、アプリケーションをエンド ツー エンドで実行する準備ができました。**sample** プロジェクトを右クリックし、**[Run As]**、**[Run On Server]** の順にクリックします。前の手順で指定した値をそのまま使用し、**[Finish]** をクリックします。

30. JBoss ブラウザーで、ログイン ページが開きます。**[Awesome Computers]** リンクをクリックすると、Office 365 の ID プロバイダー ページに自動的にリダイレクトされます。このページでディレクトリ テナントの資格情報を使用してログインできます。たとえば、*john.doe@fabrikam.onmicrosoft.com* を使用します。

31. ログイン後、認証されたユーザーとしてセキュリティで保護されたページ (**sample/index.jsp**) に再度リダイレクトされます。

## <a name="summary"></a>まとめ

このチュートリアルでは、シングル サインオン Azure Active Directory 機能を使用する 1 つのテナント Java アプリケーションを作成および構成する方法について説明しました。

  [Azure Active Directory 用の Java サンプル コード]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server version 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [JBoss Developer Studio IDE]: https://devstudio.jboss.com/earlyaccess/
  [Office 365 PowerShell コマンドレット]: http://onlinehelp.microsoft.com/ja-jp/office365-enterprises/ff652560.aspx
  [はじめに]: #introduction
  [手順 1.Java アプリケーションを作成する]: #createapp
  [手順 2.会社のディレクトリ テナントでアプリケーションをプロビジョニングする]: #provisionapp
  [手順 3.WS-Federation を従業員のサインインに使用してアプリケーションを保護する]: #protectapp
  [まとめ]: #summary
