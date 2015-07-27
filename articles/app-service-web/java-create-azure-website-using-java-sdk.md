<properties 
	pageTitle="Azure SDK for Java を使用した Azure App Service での Web アプリの作成" 
	description="Azure SDK for Java を使用してプログラムで Azure App Service の Web アプリを作成する方法について説明します。" 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="v-donntr"/>


# Azure SDK for Java を使用した Azure App Service での Web アプリの作成

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## 概要

このチュートリアルでは、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) で Web アプリを作成する Azure SDK for Java アプリケーションを作成し、Web サイトにアプリケーションをデプロイする方法について説明します。次の 2 つの部分から構成されます。

- パート 1 では、Web アプリを作成する Java アプリケーションをビルドする方法を説明します。
- パート 2 では、簡単な JSP の "Hello World" アプリケーションを作成する方法を説明し、FTP クライアントを使用して App Service にコードをデプロイします。


## 前提条件

### ソフトウェアのインストール

この記事の AzureWebDemo アプリケーション コードは、Azure Java SDK 0.7.0 を使用して記述されました。これは、[Web Platform Installer (WebPI)](http://go.microsoft.com/fwlink/?LinkID=252838) を使用してインストールできます。また、必ず最新バージョンの [Azure Toolkit for Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx) を使用してください。SDK をインストールしたら、**Maven リポジトリ**で**インデックスの更新**を実行して、Eclipse プロジェクトの依存関係を更新します。次に、**[依存関係]** ウィンドウで、各パッケージの最新バージョンを再度追加します。**[ヘルプ] > [インストールの詳細]** の順にクリックして、Eclipse にインストールされているソフトウェアのバージョンを確認できます。少なくとも次のバージョンがインストールされている必要があります。

- Microsoft Azure Libraries for Java 0.7.0.20150309 のパッケージ
- Eclipse IDE for Java EE Developers 4.4.2.20150219


### Azure でクラウド リソースを作成して構成する

この手順を開始する前に、アクティブな Azure サブスクリプションを持ち、Azure で既定の Active Directory (AD) を設定する必要があります。


### Azure で Active Directory (AD) を作成する

Azure サブスクリプションでまだ Active Directory (AD) を持っていない場合は、Microsoft アカウントを使用して [Azure の旧ポータル](https://manage.windowsazure.com)にログインします。複数のサブスクリプションを持っている場合は、**[サブスクリプション]** をクリックし、このプロジェクトで使用するサブスクリプションの既定のディレクトリを選択します。**[適用]** をクリックしてそのサブスクリプション ビューに切り替えます。

1. 左側のメニューから **[Active Directory]** を選択します。**[新規] > [ディレクトリ] > [カスタム作成]** の順にクリックします。

2. **[ディレクトリの追加]** で、**[新しいディレクトリの作成]** を選択します。

3. **[名前]** にディレクトリ名を入力します。

4. **[ドメイン]** にドメイン名を入力します。これは、既定でディレクトリに含まれている基本的なドメイン名で、`<domain_name>.onmicrosoft.com` という形式になっていますです。ディレクトリ名や自分が所有する別のドメイン名に基づいて名前を付けることができます。組織が既に使用している別のドメイン名を後から追加することもできます。

5. **[国またはリージョン]** でロケールを選択します。

AD の詳細については、「[Azure AD ディレクトリとは](http://technet.microsoft.com/library/jj573650.aspx)」をご覧ください。


### Azure の管理証明書を作成する

Azure SDK for Java は、管理証明書を使用して Azure サブスクリプションを認証します。これらは、クライアント アプリケーションの認証に使用する X.509 v3 証明書で、サービス管理 API を使用してサブスクリプション所有者に代わって動作し、サブスクリプションのリソースを管理します。

この手順のコードは、自己署名証明書を使用して Azure を認証します。この手順では、証明書を作成し、事前に [Azure の旧ポータル](https://manage.windowsazure.com)にアップロードする必要があります。これには、次の手順が含まれます。

- クライアント証明書を表す PFX ファイルを生成し、ローカルに保存します。
- PFX ファイルから管理証明書 (CER ファイル) を生成します。
- CER ファイルを Azure サブスクリプションにアップロードします。
- PFX ファイルを JKS に変換します。Java はこの形式を使用して、証明書を使用して認証するためです。
- アプリケーションの認証コードを記述します。これはローカルの JKS ファイルを参照します。

この手順が完了すると、CER 証明書は Azure サブスクリプションに格納され、JKS 証明書はローカル ドライブに格納されます。管理証明書の詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/library/azure/gg551722.aspx)」をご覧ください。


#### 証明書を作成する

自身の自己署名証明書を作成するには、オペレーティング システムでコマンド コンソールを開き、次のコマンドを実行します。

> **注:** コマンドを実行するコンピューターに JDK がインストールされている必要があります。また、keytool へのパスは、JDK をインストールする場所によって異なります。詳細については、Java オンライン ドキュメントの「[Key and Certificate Management Tool (keytool) (キーと証明書管理ツール (keytool))](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html)」をご覧ください。

.pfx ファイルを作成するには:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

.cer ファイルを作成するには:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

各値の説明:

- `<java-install-dir>` は、Java がインストールされているディレクトリへのパスです。
- `<keystore-id>` は、キーストア エントリの識別子です (`AzureRemoteAccess` など)。
- `<cert-store-dir>` は、証明書を格納するディレクトリへのパスです (`C:/Certificates` など)。
- `<cert-file-name>` は、証明書ファイルの名前です (`AzureWebDemoCert` など)。
- `<password>` は、証明書を保護する場合のパスワードです。6 文字以上の長さにする必要があります。パスワードを入力しなくても続行できますが、推奨しません。
- `<dname>` は、エイリアスに関連付けられる X.500 識別名であり、自己署名証明書の発行者フィールドとサブジェクト フィールドとして使用されます。

詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/library/azure/gg551722.aspx)」をご覧ください。


#### 証明書をアップロードする

自己署名証明書を Azure にアップロードするには、旧ポータルの **[設定]** ページに進み、**[管理証明書]** タブをクリックします。ページの下部にある **[アップロード]** をクリックし、作成した CER ファイルの場所に移動します。


#### PFX ファイルを JKS に変換する

Windows コマンド プロンプト (管理者として実行) で、証明書を含むディレクトリに移動し、次のコマンドを実行します。`<java-install-dir>` は、Java がインストールされているコンピューターのディレクトリです。

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. メッセージが表示されたら、変換先のキーストア パスワードを入力します。これは、JKS ファイルのパスワードになります。

2. メッセージが表示されたら、ソースのキーストア パスワードを入力します。これは、PFX ファイルに指定したパスワードです。

2 つのパスワードを同じにする必要はありません。パスワードを入力しなくても続行できますが、推奨しません。


## Web アプリ作成アプリケーションの構築

### Eclipse ワークスペースと Maven プロジェクトを作成する

このセクションでは、AzureWebDemo という名前の Web アプリ作成アプリケーションのワークスペースと Maven プロジェクトを作成します。

1. 新しい Maven プロジェクトを作成します。**[ファイル] > [新規作成] > [Maven プロジェクト]** の順にクリックします。**[New Maven Project]** で、**[Create a simple project]** と **[Use default workspace location]** を選択します。

2. **[New Maven Project]** の 2 ページ目で、次にように指定します。

    - Group ID: `com.<username>.azure.webdemo`
    - Artifact ID: AzureWebDemo
    - Version: 0.0.1-SNAPSHOT
    - Packaging: jar
    - Name: AzureWebDemo

    **[完了]** をクリックします。

3. プロジェクト エクスプローラーで、新しいプロジェクトの pom.xml ファイルを開きます。**[依存関係]** タブを選択します。これは新しいプロジェクトなので、まだパッケージが表示されていません。

4. Maven リポジトリ ビューを開きます。**[Window] > [ビューを表示] > [その他] > [Maven] > [Maven リポジトリ]** の順にクリックし、**[OK]** をクリックします。**[Maven リポジトリ]** ビューが IDE の下部に表示されます。

5. **[グローバル リポジトリ]** を開き、**[中央]** リポジトリを右クリックして **[インデックスの再構築]** を選択します。

    ![][1]
    
    この手順は、接続の速度に応じて数分かかる場合があります。インデックスを再構築するときに、**[中央]** の Maven リポジトリに Microsoft Azure パッケージが表示されます。

6. **[依存関係]** で、**[追加]** をクリックします。**[Enter Group ID...]** に `azure-management` と入力します。基本管理と App Service Web アプリ管理用のパッケージを選択します。

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **注:** 新しいバージョンのリリース後に依存関係を更新した場合は、この一覧の各依存関係を再度追加する必要があります。**[追加]** をクリックして各依存関係を選択すると、**[依存関係]** の一覧に新しいバージョン番号が表示されます。

**[OK]** をクリックします。次に、Azure パッケージが **[依存関係]** の一覧に表示されます。


### Azure SDK を呼び出して Web アプリを作成する Java コードを記述する

次に、Azure SDK for Java で API を呼び出して App Service Web アプリを作成するコードを記述します。

1. Java クラスを作成してメイン エントリ ポイントのコードを格納します。プロジェクト エクスプローラーで、プロジェクト ノードを右クリックして **[新規] > [クラス]** の順に選択します。

2. **[New Java Class]** で、クラスに `WebCreator` という名前を付け、**[public static void main]** チェック ボックスをオンします。選択内容が次のように表示されます。

    ![][2]

3. **[完了]** をクリックします。プロジェクト エクスプローラーに WebCreator.java ファイルが表示されます。


### App Service Web アプリを作成する Azure API を呼び出す


#### 必要なインポートを追加する

WebCreator.java で、次のインポートを追加します。これらのインポートは、Azure API を使用する場合に管理ライブラリ内のクラスへのアクセスを提供します。

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### メイン エントリ ポイントのクラスを定義する

AzureWebDemo アプリケーションの目的は App Service Web アプリを作成することなので、このアプリケーションのメイン クラスに `WebAppCreator` という名前を付けます。このクラスは、Azure サービス管理 API を呼び出して Web アプリを作成するメイン エントリ ポイントのコードを提供します。

Web アプリと Web スペースに次のパラメーター定義を追加します。自身の Azure サブスクリプション ID と証明書情報を提供する必要があります。

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

各値の説明:

- `<subscription-id>` は、リソースを作成する Azure サブスクリプション ID です。
- `<certificate-store-path>` は、ローカルの証明書ストア ディレクトリ内の JKS ファイルへのパスとファイル名です。たとえば、Linux の場合は `C:/Certificates/CertificateName.jks`、Windows の場合は `C:\Certificates\CertificateName.jks` です。
- `<certificate-password>` は、JKS 証明書を作成したときに指定したパスワードです。
- `webAppName` には任意の名前を選択できます。この手順では、`WebDemoWebApp` という名前を使用します。完全なドメイン名は、`webAppName` に `domainName` が追加されるため、この場合の完全なドメインは `webdemowebapp.azurewebsites.net` となります。
- `domainName` は、上記のように指定する必要があります。
- `webSpaceName` には、[WebSpaceNames](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html) クラスで定義されたいずれかの値を使用する必要があります。
- `appServicePlanName` は、上記のように指定する必要があります。

> **注:** このアプリケーションを実行するたびに、`webAppName` と `appServicePlanName` の値を変更 (または Azure ポータルで Web アプリを削除) してから、アプリケーションを再度実行する必要があります。そうしないと、Azure に同じリソースが既に存在しているため実行に失敗します。


#### Web 作成メソッドを定義する

次に、Web アプリを作成するメソッドを定義します。このメソッド `createWebApp` は、Web アプリと Web スペースのパラメーターを指定します。これは、App Service Web アプリ管理クライアントの作成と構成も行い、[WebSiteManagementClient](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html) オブジェクトで定義されます。この管理クライアントは、Web アプリ作成の鍵となります。アプリケーションがサービス管理 API を呼び出すことで Web アプリ (作成、更新、削除などの操作を実行する) を管理できる、RESTful Web サービスを提供します。

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

このコードは、成功または失敗を示す HTTP 状態の応答を出力します。成功した場合は、作成された Web アプリの名前を出力します。


#### main() メソッドを定義する

createWebApp() を呼び出して Web アプリを作成する、main() メソッド コードを提供します。

最後に、`createWebApp` を `main` から呼び出します。

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### アプリケーションを実行して Web アプリの作成を確認する

アプリケーションの実行を確認するには、**[実行] > [実行]** の順にクリックします。アプリケーションが実行を完了すると、Eclipse コンソールに次の出力が表示されます。

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Azure 旧ポータルにログインし、**[Web アプリ]** をクリックします。数分以内に、新しい Web アプリが Web アプリの一覧に表示されます。


## Web アプリへのアプリケーションのデプロイ

AzureWebDemo を実行して新しい Web アプリを作成したら、旧ポータルにログインし、**[Web アプリ]** をクリックして、**[Web アプリ]** の一覧から **[WebDemoWebApp]** を選択します。Web アプリのダッシュ ボード ページで、**[参照]** をクリックして移動します (または、URL `webdemowebapp.azurewebsites.net` をクリックします)。コンテンツがまだ Web アプリに発行されていないため、空のプレースホルダー ページが表示されます。

次に、"Hello World" アプリケーションを作成し、これを Web アプリにデプロイします。


### JSP Hello World アプリケーションを作成する

#### アプリケーションを作成する

アプリケーションを Web にデプロイする方法を説明するために、以下の手順では、簡単な "Hello World" Java アプリケーションを作成し、これをアプリケーションを作成した App Service Web アプリにアップロードする方法を示しています。

1. **[File] > [New] > [Dynamic Web Project]** の順にクリックします。これに `JSPHello` という名前を付けます。このダイアログ ボックスの他の設定を変更する必要はありません。**[完了]** をクリックします。

    ![][3]

2. Project Explorer で、**[JSPHello]** プロジェクトを展開し、**[WebContent]** を右クリックして **[New] > [JSP File]** の順にクリックします。[New JSP File] ダイアログ ボックスで、新しいファイルに `index.jsp` という名前を付けます。**[次へ]** をクリックします。

3. **[Select JSP Template]** ダイアログで、**[New JSP File (html)]** を選択し、**[Finish]** をクリックします。

4. Index.jsp で、`<head>` タグと `<body>` タグのセクションに次のコードを追加します。

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### Hello World アプリケーションを localhost で実行する

このアプリケーションを実行する前に、いくつかのプロパティを構成する必要があります。

1. **[JSPHello]** プロジェクトを右クリックし、**[Properties]** を選択します。

2. **[Properties]** ダイアログ ボックスで、**[Java Build Path]** を選択し、**[Order and Export]** タブを選択します。次に、**[JRE System Library]** チェック ボックスをオンにし、**[Up]** をクリックして一覧の先頭に移動します。

    ![][4]

3. さらに、**[Properties]** ダイアログ ボックスで、**[Targeted Runtimes]** を選択し、**[New]** をクリックします。

4. **[New Server Runtime Environment]** ダイアログ ボックスで、**Apache Tomcat v7.0** などのサーバーを選択し、**[Next]** をクリックします。**[Tomcat Server]** ダイアログ ボックスで、**[Name]** には `Apache Tomcat v7.0`、**[Tomcat Installation Directory]** には使用する Tomcat サーバーのバージョンがインストールされているディレクトリを設定します。

    ![][5]

    **[完了]** をクリックします。

5. 次に、**[Properties]** ダイアログ ボックスの **[Targeted Runtimes]** ページに戻ります。**[Apache Tomcat v7.0]** を選択し、**[OK]** をクリックします。

    ![][6]

6. Eclipse の **[Run]** メニューで、**[Run]** をクリックします。**[Run]** ダイアログ ボックスで、**[Run on Server]** を選択します。**[Run on Server]** ダイアログ ボックスで、**[Tomcat v7.0 Server]** を選択します。

    ![][7]

    **[完了]** をクリックします。

7. アプリケーションが実行されると、Eclipse の localhost ウィンドウに **JSPHello** ページが表示され (`http://localhost:8080/JSPHello/`)、次のメッセージが表示されます。

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### アプリケーションを WAR としてエクスポートする

Web プロジェクト ファイルを Web アーカイブ (WAR) ファイルとしてエクスポートすると、これを Web アプリにデプロイできます。次の Web プロジェクト ファイルは、WebContent フォルダーに存在します。

    META-INF
    WEB-INF
    index.jsp

1. WebContent フォルダーを右クリックし、**[Export]** を選択します。

2. **[Export Select]** ダイアログ ボックスで、**[Web] > [WAR]** ファイルの順にクリックし、**[次へ]** をクリックします。

3. **[WAR Export]** ダイアログ ボックスで、現在のプロジェクトの src ディレクトリを選択し、末尾に WAR ファイルの名前を含めます。次に例を示します。

    `<project-path>/JSPHello/src/JSPHello.war`

WAR ファイルのデプロイ方法の詳細については、「[Azure の Java Web サイトへのアプリケーションの追加](web-sites-java-add-app.md)」をご覧ください。


### FTP を使用して Hello World アプリケーションをデプロイする

サードパーティの FTP クライアントを選択してアプリケーションを発行します。この手順では、2 つのオプション (Azure に組み込まれている Kudu コンソールと、グラフィカル UI を備えた使いやすい一般的なツール FileZilla) について説明します。

> **注:** Azure Plugin for Eclipse with Java 2.4 は、ストレージ アカウントとクラウド サービスへのデプロイをサポートしていますが、Web アプリへのデプロイは現在サポートしていません。ストレージ アカウントとクラウド サービスへのデプロイは、「[Azure の Hello World アプリケーションを Eclipse で作成する](http://msdn.microsoft.com/library/azure/hh690944.aspx)」の説明に従って、Azure デプロイ プロジェクトを使用して行うことができますが、Web アプリへはデプロイできません。FTP や GitHub などの他の方法を使用して、Web アプリにファイルを転送します。

> **注:** Windows コマンド プロンプトから FTP (Windows に付属するコマンド ラインの FTP.EXE ユーティリティ) を使用することはお勧めしません。FTP.EXE などのアクティブ FTP を使用する FTP クライアントは、多くの場合、ファイアウォールでの動作に失敗します。アクティブ FTP は LAN ベースの内部アドレスを指定するため、FTP サーバーは接続に失敗する可能性があります。

FTP を使用した App Service Web アプリへのデプロイの詳細については、次のトピックをご覧ください。

- [Manage web apps using the Azure Portal: Deployment (Azure ポータルを使用した Web アプリの管理: デプロイ)](web-sites-manage.md/#deployment)
- [FTP ユーティリティを使用したデプロイ](web-sites-deploy.md)


#### [デプロイ資格情報を設定する] を選択して構成できます

**AzureWebDemo** アプリケーションを実行して Web アプリを作成したことを確認します。ファイルはこの場所に転送されます。

1. 旧ポータルにログインし、**[Web アプリ]** をクリックします。**WebDemoWebApp** が Web アプリの一覧に表示され、実行していることを確認します。**[WebDemoWebApp]** をクリックし、**[ダッシュボード]** ページを開きます。

2. **[ダッシュボード]** ページの **[概要]** で、**[Set up your deployment credentials]** をクリックします (既にデプロイ資格情報を設定している場合は、**[Reset your deployment credentials]** が読み込まれます)。

    デプロイ資格情報は、Microsoft アカウントと関連付けられています。Git と FTP を使用してデプロイする際のユーザー名パスワードを指定する必要があります。これらの資格情報を使用すると、Microsoft アカウントに関連付けられているすべての Azure サブスクリプション内の任意の Web アプリにデプロイできます。Git と FTP のデプロイ資格情報をダイアログ ボックスに入力し、後で使用するためにパスワードとユーザー名を記録します。


#### FTP の接続情報を取得する

FTP を使用して、新しく作成された Web アプリにアプリケーション ファイルをデプロイするには、接続情報を取得する必要があります。接続情報を取得するには、2 つの方法があります。1 つは、Web アプリの**ダッシュボード** ページにアクセスする方法、もう 1 つは、Web アプリの発行プロファイルをダウンロードする方法です。発行プロファイルは、FTP ホスト名やログオン資格情報などの情報を Azure App Service の Web アプリに提供する XML ファイルです。このユーザー名とパスワードを使用すると、この Web アプリだけでなく、Azure アカウントに関連付けられているすべてのサブスクリプション内の任意の Web アプリにデプロイできます。

[Azure ポータル](https://portal.azure.com)で、Web アプリのブレードから FTP 接続情報を取得するには:

1. **Essentials** で、**FTP ホスト名**を検索してコピーします。これは、`ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net` のような URI です。

2. **Essentials** で、**FTP/デプロイ ユーザー名**を検索してコピーします。これは、*Web アプリ名\\デプロイ ユーザー名*という形式です (例: `WebDemoWebApp\deployer77`)。

発行プロファイルから FTP 接続情報を取得するには

1. Web アプリのブレードで、**[発行プロファイルを取得]** をクリックします。.publishsettings ファイルがローカル ドライブにダウンロードされます。

2. XML エディターまたはテキスト エディターで .publishsettings ファイルを開き、`publishMethod="FTP"` を含む `<publishProfile>` 要素を検索します。この要素は次のようになります。

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Web アプリの `publishProfile` 設定は、次のように FileZilla Site Manager 設定にマップされることに注意してください。

- `publishUrl` は、**FTP ホスト名**と同じで、**ホスト**で設定された値です。
- `publishMethod="FTP"` は、**プロトコル**が **[FTP - File Transfer Protocol]**、**暗号化**が **[Use plain FTP]** に設定されていることを意味します。
- `userName` と `userPWD` は、デプロイ資格情報をリセットするときに指定された、実際のユーザー名とパスワードの値のキーです。`userName` は**デプロイ / FTP ユーザー**と同じです。これらは、FileZilla の**ユーザー**と**パスワード**にマップされます。
- `ftpPassiveMode="True"` は、FTP サイトがパッシブ FTP 転送を使用することを意味します (**[転送設定]** タブで **[パッシブ]** を選択)。


#### Java アプリケーションをホストする Web アプリを構成する

アプリケーションを発行する前に、Web アプリが Java アプリケーションをホストできるように、いくつかの構成設定を変更する必要があります。

1. 旧ポータルで、Web アプリの **[ダッシュ ボード]** ページに移動し、**[構成]** をクリックします。**[構成]** ページで、次の設定を指定します。

2. **[Java バージョン]** の既定値を **[オフ]** にし、アプリケーションに対応する Java バージョンを選択します (例: 1.7.0_51)。次に、**[Web コンテナー]** が Tomcat サーバーのバージョンに設定されていることを確認します。

3. **[既定のドキュメント]** で、index.jsp を追加し、これを一覧の先頭に移動します(Web アプリの既定のファイルは hostingstart.html です)。

4. **[保存]** をクリックします。


#### Kudu を使用してアプリケーションを発行する

アプリケーションを発行する 1 つの方法は、Azure に組み込まれている Kudu デバッグ コンソールを使用することです。Kudu は安定性があり、App Service Web アプリや Tomcat サーバーと一貫性があることが知られています。ブラウザーから次の形式の URL を参照して、Web アプリのコンソールにアクセスします。

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. この手順では、Kudu コンソールは次の URL にあります。この場所にアクセスしてください。

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. 上部のメニューから、**[デバッグ コンソール] > [CMD]** の順に選択します。

3. コンソールのコマンド ラインで、`/site/wwwroot` に移動します (または、ページの上部にあるディレクトリ ビューで、`site`、`wwwroot` の順にクリックします)。

    `cd /site/wwwroot`

4. **Java バージョン**を指定したら、Tomcat サーバーで webapps ディレクトリを作成する必要があります。コンソールのコマンド ラインで、webapps ディレクトリに移動します。

    `mkdir webapps`

    `cd webapps`

5. JSPHello.war を `<project-path>/JSPHello/src/` からドラッグし、`/site/wwwroot/webapps` の下の Kudu ディレクトリ ビューにドロップします。[Drag here to upload and zip] 領域にはドラッグしないでください。Tomcat がファイルを解凍します。

  ![][8]

最初に、JSPHello.war が単独でディレクトリ領域に表示されます。

  ![][9]

短時間 (おそらく 5 分未満) で、Tomcat サーバーはアンパックされた JSPHello ディレクトリに WAR ファイルを解凍します。ルート ディレクトリをクリックして、Index.jsp が解凍され、コピーされていることを確認します。確認したら、webapps ディレクトリに戻り、アンパックされた JSPHello ディレクトリが作成されていることを確認します。これらの項目が表示されない場合は、少し待ってから繰り返します。

  ![][10]


#### FileZilla を使用してアプリケーションを発行する (省略可能)

アプリケーションの発行に使用できるもう 1 つのツールは、FileZilla です。これは、サード パーティの一般的な FTP クライアントで、使いやすいグラフィカル UI を備えています。まだ入手していない場合は、[http://filezilla-project.org/](http://filezilla-project.org/) から FileZilla をダウンロードしてインストールできます。クライアントの使用方法については、「[FileZilla に関するドキュメント](https://wiki.filezilla-project.org/Documentation)」と「[FTP クライアント - パート 4: FileZilla (ブログの投稿)](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx)」をご覧ください。

1. FileZilla で、**[ファイル] > [サイト マネージャー]** の順にクリックします。
2. **[サイト マネージャー]** ダイアログ ボックスで、**[新しいサイト]** をクリックします。新しい空の FTP サイトが表示され、**[Select Entry]** に名前を指定するように求められます。この手順では、`AzureWebDemo-FTP` という名前を付けます。

    **[全般]** タブで次の設定を指定します。- **[ホスト]:** ダッシュボードからコピーした **FTP ホスト名**を入力します。- **[ポート]:** (これはパッシブ転送であり、使用するポートをサーバーが決定するため、空白のままにします。) - **[プロトコル]:** FTP - File Transfer Protocol - **[暗号化]:** Use plain FTP - **[ログオン タイプ]:** 通常 - **[ユーザー]:** ダッシュボードからコピーしたデプロイ / FTP ユーザーを入力します。これは完全な FTP ユーザー名であり、その形式は *Web アプリ名\\ユーザー名*になります。 - **[パスワード]:** デプロイ資格情報を設定したときに指定したパスワードを入力します。

    **[転送設定]** タブで **[パッシブ]** を選択します。

3. **[接続]** をクリックします。成功すると、FileZilla のコンソールに `Status: Connected` というメッセージが表示され、ディレクトリの内容を一覧表示する `LIST` コマンドが実行されます。

4. **ローカル** サイト パネルで、JSPHello.war ファイルが格納されているソース ディレクトリを選択します。パスは次のようになります。

    `<project-path>/JSPHello/src/`

5. **リモート** サイト パネルで、転送先フォルダーを選択します。WAR ファイルを Web アプリのルートの下の `webapps` ディレクトリにデプロイします。`/site/wwwroot` に移動し、`wwwroot` を右クリックして **[ディレクトリの作成]** を選択します。ディレクトリに `webapps` という名前を付け、そのディレクトリに入力します。

6. JSPHello.war を `/site/wwwroot/webapps` に転送します。**ローカル** ファイル リストで JSPHello.war を選択し、これを右クリックして **[アップロード]** を選択します。ファイルが `/site/wwwroot/webapps` に表示されるはずです。

7. JSPHello.war を webapps ディレクトリにコピーすると、Tomcat サーバーは WAR ファイル内のファイルを自動的にアンパック (解凍) します。Tomcat サーバーはほぼ瞬時にアンパックを開始しますが、ファイルが FTP クライアントに表示されるまで長い時間 (場合によっては数時間) がかかる可能性があります。


#### Hello World アプリケーションを Web アプリで実行する

1. WAR ファイルがアップロードされ、Tomcat サーバーがアンパックされた `JSPHello` ディレクトリを作成したことを確認したら、ブラウザーで `http://webdemowebapp.azurewebsites.net/JSPHello` にアクセスしてアプリケーションを実行します。

    > **注:** 旧ポータルから **[参照]** をクリックすると、既定の Web ページに "この Java ベースの Web アプリケーションは正常に作成されました。" と表示される場合があります。 既定の Web ページの代わりにアプリケーションの出力を表示するには、Web ページを更新する必要があります。

2. アプリケーションを実行すると、Web ページに次の出力が表示されます。

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Azure リソースをクリーンアップする

この手順では、App Service Web アプリを作成します。Web アプリが存在する限りリソースに課金されます。テストや開発のために Web アプリを引き続き使用する予定がない場合は、停止や削除を検討する必要があります。Web アプリを停止する場合は、少額の料金が発生しますが、いつでも再開できます。Web アプリを削除する場合は、アップロードされているすべてのデータが消去されます。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

<!---HONumber=July15_HO3-->