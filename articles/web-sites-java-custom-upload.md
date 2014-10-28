<properties linkid="develop-java-tutorials-web-site-custom_upload" urlDisplayName="Upload custom Java website" pageTitle="Upload a custom Java website to Azure" metaKeywords="" description="This tutorial shows you how to upload a custom Java website to Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Upload a custom Java website to Azure" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Azure へのカスタム Java Web サイトのアップロード

このトピックでは、カスタム Java Web サイトを Azure にアップロードする方法について説明します。Java Web サイトに適用される情報や、特定のアプリケーションを有効にする例も取り上げています。

Azure には、Azure ポータルの構成 UI や Azure のアプリケーション ギャラリーを使用して Java Web サイトを作成するための手段が用意されています。詳細については、「[Azure Websites と Java の概要][Azure Websites と Java の概要]」を参照してください。このチュートリアルは、Azure の構成 UI やアプリケーション ギャラリーを使用しないシナリオを対象にしています。

# 構成のガイドライン

ここでは、Azure のカスタム Java Web サイトに想定される設定について説明します。

-   Java プロセスで使用される HTTP ポートは動的に割り当てられます。このプロセスでは、環境変数 `HTTP_PLATFORM_PORT` のポートを使用する必要があります。
-   1 つの HTTP リスナー以外のすべてのリッスン ポートを無効にする必要があります。Tomcat では、それはシャットダウン、HTTPS、AJP などのポートです。
-   コンテナーは IPv4 トラフィック専用に構成する必要があります。
-   アプリケーションの**スタートアップ** コマンドはこの構成で設定する必要があります。
-   書き込みアクセス許可のあるディレクトリを必要とするアプリケーションは、Azure Websites のコンテンツ ディレクトリ (**D:\\home**) に配置する必要があります。

web.config ファイルで必要に応じて環境変数を設定できます。

# web.config の httpPlatform の構成

ここでは、web.config での **httpPlatform** 形式について説明します。

**arguments** (Default="")。**processPath** 設定で指定した実行可能ファイルまたはスクリプトの引数。

次に例を示します (**processPath** も使用)。

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"

    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"

**processPath** - HTTP 要求のリッスン プロセスを起動する実行可能ファイルまたはスクリプトへのパス。

次に例を示します。

    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="d:\home\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       

**rapidFailsPerMinute** (Default=10)。**processPath** で指定したプロセスが 1 分あたりにクラッシュできる回数。この制限を超えた場合、**HttpPlatformHandler** は残りの分数、プロセスを起動しなくなります。

**requestTimeout** (Default="00:02:00")。**HttpPlatformHandler** が `%HTTP_PLATFORM_PORT%` でリッスン プロセスからの応答を待つ期間。

**startupRetryCount** (Default=10)。**HttpPlatformHandler** が **processPath** で指定されたプロセスを起動しようとする回数。詳細については「**startupTimeLimit**」を参照してください。

**startupTimeLimit** (Default=10 seconds)。**HttpPlatformHandler** が実行可能ファイル/スクリプトによるポートのリッスン プロセスの開始を待つ期間。この制限を超えた場合、**HttpPlatformHandler** はリッスン プロセスを強制終了し、**startupRetryCount** の回数、再起動しようとします。

**stdoutLogEnabled** (Default="true")。true の場合、**processPath** 設定で指定されたプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されたファイルにリダイレクトされます (「**stdoutLogFile**」を参照)。

**stdoutLogFile** (Default="d:\\home\\LogFiles\\httpPlatformStdout.log")。**processPath** で指定されたプロセスの **stdout** と **stderr** を記録するログの絶対ファイル パス。

> [WACOM.NOTE] `%HTTP_PLATFORM_PORT%` は特殊なプレースホルダーであり、**arguments** の一部として、または **httpPlatform** **environmentVariables** リストの一部として指定する必要があります。このプレース ホルダーは、**HttpPlatformHandler** によって内部で生成されたポートに置き換えられるため、**processPath** で指定されたプロセスがこのポートをリッスンできるようになります。

# デプロイ

Java ベースの Web サイトは、インターネット インフォメーション サービス (IIS) ベースの Web アプリケーションの場合とほぼ同じ手段を使用して、簡単にデプロイできます。FTP、Git、Kudu はすべて、Web サイト用の統合 SCM 機能と同様に、デプロイ メカニズムとしてサポートされています。WebDeploy はプロトコルとして機能しますが、Java は Visual Studio で開発されていないため、WebDeploy は Java Web サイトのデプロイには向いていません。

# アプリケーションの構成例

次のアプリケーションの web.config ファイルとアプリケーション構成は、Azure Websites で Java アプリケーションを有効にする方法を示す例として挙げています。

## Tomcat

Azure Websites には 2 種類の Tomcat インスタンスが用意されていますが、独自のインスタンスをアップロードすることもできます。次に示しているのは、個別の JVM に Tomcat をインストールする例です。

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="d:\home\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Tomcat 側で、行う必要のあるいくつかの構成変更があります。server.xml では次のように設定する必要があります。

-   シャットダウン ポート = -1
-   HTTP コネクタ ポート = {port.http}
-   HTTP コネクタ アドレス = "127.0.0.1"
-   HTTPS と AJP のコネクタをコメントアウト
-   IPv4 の設定は catalina.properties ファイルでも可能で、`java.net.preferIPv4Stack=true` を追加できます。

Direct3d の呼び出しは Azure Websites ではサポートされていません。それらの呼び出しを無効にするには、アプリケーションによるこのような呼び出しに備えて、次の Java オプションを追加します。 `-Dsun.java2d.d3d=false`

## Jetty

Tomcat の場合と同様、Jetty の独自のインスタンスをアップロードできます。Jetty のフル インストールを実行する場合、構成は次のようになります。

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;d:\home\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

Jetty の構成を start.ini で変更して、`java.net.preferIPv4Stack=true` を設定する必要があります。

## Hudson

Microsoft のテストでは、Hudson 3.1.2 war と既定の Tomcat 7.0.50 インスタンスを使用しましたが、セットアップに UI を使用しませんでした。Hudson はソフトウェア ビルド ツールであるため、サイトで **AlwaysOn** フラグを設定できる専用のインスタンスにインストールすることをお勧めします。

1.  Azure Websites のサイト ルート (**d:\\home\\site\\wwwroot**) に **webapps** ディレクトリを作成し (まだない場合)、**d:\\home\\site\\wwwroot\\webapps** に Hudson.war を配置します。
2.  apache maven 3.0.5 (Hudson と互換性あり) をダウンロードし、**d:\\home\\site\\wwwroot** に配置します。
3.  **d:\\home\\site\\wwwroot** に web.config を作成し、そのファイルに次の内容を貼り付けます。

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <handlers>
              <add name="httppPlatformHandler" path="*" verb="*" 
        modules="httpPlatformHandler" resourceType="Unspecified" />
            </handlers>
            <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
        startupTimeLimit="20"
        startupRetryCount="10">
        <environmentVariables>
          <environmentVariable name="HUDSON_HOME" 
        value="d:\home\site\wwwroot\hudson_home" />
          <environmentVariable name="JAVA_OPTS" 
        value="-Djava.net.preferIPv4Stack=true -Duser.home=d:/home/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
        </environmentVariables>            
            </httpPlatform>
          </system.webServer>
        </configuration>

    この時点で、Web サイトを再び開始して、変更を行うことができます。<http://yoursite/hudson> に接続して Hudson を開始します。

4.  Hudson の自動構成の後に、次の画面が表示されます。

    ![Hudson][Hudson]

5.  Hudson の構成ページにアクセスします。**[Manage Hudson]** をクリックし、**[Configure System]** をクリックします。
6.  次に示しているように JDK を構成します。

    ![Hudson の構成][Hudson の構成]

7.  次に示すように Maven を構成します。

    ![Maven の構成][Maven の構成]

8.  設定を保存します。これで、Hudson は構成されて使用できる状態になりました。

Hudson の詳細については、[][]<http://hudson-ci.org></a> を参照してください。

## Liferay

Liferay は Azure Websites でサポートされています。Liferay には大量のメモリが必要になることがあるため、十分なメモリを用意できる中規模または大規模な専用ワーカーで、サイトを実行する必要があります。Liferay も起動するまで数分かかります。そのため、サイトで **AlwaysOn** を設定することをお勧めします。

Tomcat にバンドルされている Liferay 6.1.2 Community Edition GA3 を使用して、次のファイルを Liferay のダウンロード後に編集します。

**Server.xml**

-   シャットダウン ポートを -1 に変更。
-   HTTP コネクタを次のように変更。
    `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
-   AJP コネクタをコメントアウト。

**liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** フォルダーで、**portal-ext.properties** という名前のファイルを作成します。このファイルには、次に示している 1 行を追加する必要があります。

    liferay.home=d:/home/site/wwwroot/liferay

tomcat-7.0.40 フォルダーと同じディレクトリ レベルで、**web.config** という名前のファイルを次の内容で作成します。

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="d:\home\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="d:\home\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

**httpPlatform** ブロックで、**requestTimeout** は "00:10:00" に設定されています。この時間を短くすることはできますが、Liferay のブートストラップ中に何らかのタイムアウト エラーが表示される可能性があります。この値を変更した場合は、Tomcat の server.xml の **connectionTimeout** も変更する必要があります。

注意点としては、前に示している web.config で JRE\_HOME 環境変数を、64-bit JDK を参照するように指定しています。既定では 32-bit ですが、Liferay では高位のメモリが必要になる場合があるため、64-bit JDK を使用することをお勧めします。

これらの変更を行ったら、Liferay を実行する Web サイトを再び開始し、<http://yoursite> を開きます。Liferay ポータルは Web サイトのルートから利用できます。

Liferay の詳細については、[][1]<http://www.liferay.com></a> を参照してください。

  [Azure Websites と Java の概要]: ../web-sites-java-get-started
  [Hudson]: ./media/web-sites-java-custom-upload/hudson1.png
  [Hudson の構成]: ./media/web-sites-java-custom-upload/hudson2.png
  [Maven の構成]: ./media/web-sites-java-custom-upload/maven.png
  []: http://hudson-ci.org
  [1]: http://www.liferay.com
