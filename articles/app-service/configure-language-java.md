---
title: Windows Java アプリを構成する
description: Azure App Service の Windows VM インスタンスで Java アプリが実行されるように構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
keywords: azure app service, web アプリ, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b09a7765cff20fb49ce6ab3d1e7bce2e15f0e9e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475219"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Azure App Service 向けの Windows Java アプリを構成する

Azure App Service を使用すると、Java 開発者は、完全に管理された Windows ベースのサービス上で Tomcat Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Java クイック スタート](app-service-web-get-started-java.md)をお読みください。 Java 開発に限られない、App Service の使用に関する一般的な質問については、[App Service Windows の FAQ](faq-configuration-and-management.md) で回答されています。

## <a name="deploying-your-app"></a>アプリのデプロイ

[Maven 用 Azure Web アプリ プラグイン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)を使用して、.war ファイルをデプロイできます。 一般的な IDE を使用したデプロイは、[Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) または [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse) でもサポートされています。

それ以外の場合、デプロイの方法はアーカイブの種類によって異なります。

- .war ファイルを Tomcat にデプロイするには、`/api/wardeploy/` エンドポイントを使用してアーカイブ ファイルを POST します。 この API の詳細については、[このドキュメント](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)を参照してください。
- Java SE に .jar ファイルをデプロイするには、Kudu サイトの `/api/zipdeploy/` エンドポイントを使用します。 この API の詳細については、[このドキュメント](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)を参照してください。

FTP を使用して .war をデプロイしないでください。 FTP ツールは、スタートアップ スクリプト、依存関係、またはその他のランタイム ファイルをアップロードするために設計されています。 これは、Web アプリをデプロイするための最適な選択肢ではありません。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 詳細については、「[Azure App Service 診断の概要](overview-diagnostics.md)」を参照してください。

### <a name="use-flight-recorder"></a>Flight Recorder を使用する

Azul JVM を使用する App Service のすべての Java ランタイムには、Zulu Flight Recorder が付属しています。 これを使用すると、JVM、システム、および Java レベルのイベントを記録して、Java アプリケーションの動作を監視し、問題のトラブルシューティングを行うことができます。

時間を指定して記録を行うには、Java アプリケーションの PID (プロセス ID) が必要です。 PID を検索するには、ブラウザーで Web アプリの SCM サイト (https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/) を開きます。 このページには、Web アプリで実行中のプロセスが表示されます。 テーブル内で "java" という名前のプロセスを探し、対応する PID (プロセス ID) をコピーします。

次に、SCM サイト上部のツール バーにある **デバッグ コンソール** を開き、次のコマンドを実行します。 `<pid>` を、先ほどコピーしたプロセス ID に置き換えます。 このコマンドにより、Java アプリケーションのプロファイラーによる 30 秒の記録が開始され、`D:\home` ディレクトリに `timed_recording_example.jfr` という名前のファイルが生成されます。

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

詳しくは、[Jcmd コマンド リファレンス](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)をご覧ください。

#### <a name="analyze-jfr-files"></a>`.jfr` ファイルの分析

JFR ファイルをご自分のローカル コンピューターにダウンロードするには、[FTPS](deploy-ftp.md) を使います。 JFR ファイルを分析するには、[Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/) をダウンロードしてインストールします。 Zulu Mission Control については、[Azul のドキュメント](https://docs.azul.com/zmc/)と[インストール手順](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)をご覧ください。

### <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

詳細については、[Cloud Shell でのログのストリーミング](troubleshoot-diagnostic-logs.md#in-cloud-shell)に関する記事をご覧ください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。 Java と Tomcat のアプリ ログは */LogFiles/Application/* ディレクトリにあります。

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](/azure/application-insights/app-insights-java-trace-logs)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。


## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [アプリケーションの設定の構成](configure-common.md#configure-app-settings)
- [カスタム ドメインの設定](app-service-web-tutorial-custom-domain.md)
- [TLS バインドの構成](configure-ssl-bindings.md)
- [CDN の追加](../cdn/cdn-add-to-web-app.md)
- [Kudu サイトを構成する](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java ランタイム オプションを設定する

割り当てられたメモリまたはその他の JVM ランタイムのオプションを設定するには、[アプリ設定](configure-common.md#configure-app-settings)を作成して `JAVA_OPTS` と名付け、オプションを指定します。 App Service では、開始時にこの設定が環境変数として Java ランタイムに渡されます。

Azure portal の Web アプリ用の **[アプリケーション設定]** で、`-Xms512m -Xmx1204m` などの追加の設定を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

Maven プラグインからアプリ設定を構成するには、Azure プラグイン セクションで設定/値のタグを追加します。 次の例では、特定の最小および最大の Java ヒープ サイズを設定します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service プランで 1 つのデプロイ スロットを使用して 1 つのアプリケーションを実行している開発者は、次のオプションを使用できます。

- B1 および S1 インスタンス: `-Xms1024m -Xmx1024m`
- B2 および S2 インスタンス: `-Xms3072m -Xmx3072m`
- B3 および S3 インスタンス: `-Xms6144m -Xmx6144m`

アプリケーション ヒープ設定をチューニングする際には、App Service プランの詳細を確認し、複数のアプリケーションおよびデプロイ スロットのニーズを考慮して、メモリの最適な割り当てを特定する必要があります。

### <a name="turn-on-web-sockets"></a>Web ソケットを有効にする

Azure portal のアプリケーション用の **[アプリケーション設定]** で、Web ソケットのサポートを有効にします。 設定を有効にするには、アプリケーションを再起動する必要があります。

Azure CLI を使用して、次のコマンドで Web ソケットのサポートを有効にします。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

その後、アプリケーションを再起動します。

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>既定の文字エンコーディングを設定する

Azure portal の Web アプリ用の **[アプリケーション設定]** で、値 `-Dfile.encoding=UTF-8` を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

または、App Service Maven プラグインを使用してアプリ設定を構成できます。 プラグイン構成で、設定名および値のタグを追加します。

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP ファイルをプリコンパイルする

Tomcat アプリケーションのパフォーマンスを向上させるには、App Service にデプロイする前に、JSP ファイルをコンパイルします。 Apache Sling によって提供されている [Maven プラグイン](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html)を使用するか、この [Ant ビルド ファイル](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)を使用できます。

## <a name="secure-applications"></a>セキュリティで保護されたアプリケーション

App Service で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](/azure/security/security-paas-applications-using-app-services)を備えています。

### <a name="authenticate-users-easy-auth"></a>ユーザーを認証する (Easy Auth)

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。 詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](configure-authentication-provider-aad.md)」と、他の ID プロバイダーの関連記事を参照してください。 複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](app-service-authentication-how-to.md)に関する記事の手順に従います。

#### <a name="tomcat"></a>Tomcat

Tomcat アプリケーションでは、Principal オブジェクトを Map オブジェクトにキャストすることで、サーブレットから直接ユーザーの要求にアクセスできます。 Map オブジェクトによって、各要求の種類がその種類の要求のコレクションにマップされます。 以下のコードで、`request` は `HttpServletRequest` のインスタンスです。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

これで `Map` オブジェクトの特定の要求を検査できます。 たとえば、次のコード スニペットでは、すべての要求の種類を反復処理し、各コレクションのコンテンツを出力します。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

ユーザーをサインアウトさせるには、`/.auth/ext/logout` パスを使用します。 他のアクションを実行する場合は、「[Azure App Service 上での認証と承認の高度な使用方法](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)」を参照してください。 Tomcat の [HttpServletRequest インターフェイス](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)とそのメソッドに関する公式ドキュメントもあります。 次のサーブレット メソッドも、ご利用の App Service 構成に基づいてハイドレートされます。

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

この機能を無効にするには、`1` の値で `WEBSITE_AUTH_SKIP_PRINCIPAL` という名前のアプリケーション設定を作成します。 App Service によって追加されるすべてのサーブレット フィルターを無効にするには、`1` の値を含む `WEBSITE_SKIP_FILTERS` という名前の設定を作成します。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

「[Azure App Service で TLS バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」の手順に従って、既存の TLS/SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

### <a name="use-keyvault-references"></a>KeyVault 参照を使用する

[Azure Key Vault](../key-vault/key-vault-overview.md) では、アクセス ポリシーと監査履歴を使用した一元的なシークレット管理を提供しています。 シークレット (パスワードや接続文字列など) を KeyVault に格納し、環境変数を使用してアプリケーション内でこれらのシークレットにアクセスすることができます。

最初に、[Key Vault へのアクセス権をアプリに付与](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)したり、[アプリケーション設定で自分のシークレットに対する KeyVault 参照を設定](app-service-key-vault-references.md#reference-syntax)したりするための手順に従います。 App Service のターミナルにリモートでアクセスしている間に環境変数を出力することで、シークレットへの参照が解決されることを確認できます。

Spring または Tomcat 構成ファイルにこれらのシークレットを挿入するには、環境変数の挿入構文 (`${MY_ENV_VAR}`) を使用します。 Spring の構成ファイルについては、[外部化された構成](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)に関するこちらのドキュメントを参照してください。


## <a name="configure-apm-platforms"></a>APM プラットフォームを構成する

このセクションでは、NewRelic と AppDynamics アプリケーション パフォーマンスの監視 (APM) プラットフォームで、Linux 上の Azure App Service にデプロイされた Java アプリケーションを接続する方法を示します。

### <a name="configure-new-relic"></a>New Relic の構成

1. [NewRelic.com](https://newrelic.com/signup) で New Relic アカウントを作成します
2. NewRelic から Java エージェントをダウンロードします。ファイル名は *newrelic-java-x.x.x.zip* のようになります。
3. ライセンス キーをコピーします。これは後ほどエージェントの構成を行う際に必要です。
4. [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用して、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
5. */home/site/wwwroot/apm* の下にあるディレクトリにアンパックされた New Relic Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/newrelic* 内にある必要があります。
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* で YAML ファイルを変更し、プレース ホルダー ライセンスの値を独自のライセンス キーに置き換えます。
7. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - もしアプリが **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS`の名前および `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` の値を持つ環境変数を作成します。

### <a name="configure-appdynamics"></a>AppDynamics の構成

1. [AppDynamics.com](https://www.appdynamics.com/community/register/) で AppDynamics アカウントを作成します
2. AppDynamics の Web サイトから Java エージェントをダウンロードします。ファイル名は *AppServerAgent-x.x.x.xxxxx.zip* のようになります
3. [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用して、新しいディレクトリ */home/site/wwwroot/apm* を作成します。
4. */home/site/wwwroot/apm* の下にあるディレクトリに Java エージェント ファイルをアップロードします。 エージェント用のファイルは */home/site/wwwroot/apm/appdynamics* 内にある必要があります。
5. Azure portal で App Service でアプリケーションを参照し、新しいアプリケーション設定を作成します。
    - **Java SE** を使用している場合、`JAVA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。
    - **Tomcat** を使用している場合、`CATALINA_OPTS` の名前および `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` の値を持ち、`<app-name>` の App Service 名を持つ環境変数を作成します。

>  `JAVA_OPTS` または `CATALINA_OPTS` 用の環境変数がすでにある場合、`-javaagent:/...` のオプションを現在値の最期に追加します。

## <a name="data-sources"></a>データ ソース

### <a name="tomcat"></a>Tomcat

これらの説明は、すべてのデータベース接続に適用されます。 プレースホルダーを、選択したデータベースのドライバーのクラス名と JAR ファイルに置き換える必要があります。 一般的なデータベースのクラス名とドライバーのダウンロードを含む表を次に示します。

| データベース   | ドライバーのクラス名                             | JDBC ドライバー                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [ダウンロード](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) ("プラットフォームに依存しない" を選択) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [ダウンロード](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用するように Tomcat を構成するには、まず、起動時に Tomcat によって読み込まれる `CATALINA_OPTS` 環境変数をカスタマイズします。 [App Service Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)のアプリ設定を使用して、次の値を設定します。

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

または、Azure portal の **[構成]**  >  **[アプリケーション設定]** ページで環境変数を設定します。

次に、データ ソースを Tomcat サーブレットで実行されている 1 つのアプリケーション、またはすべてのアプリケーションのどちらに対して使用可能にする必要があるかを判定します。

#### <a name="application-level-data-sources"></a>アプリケーション レベル データ ソース

1. プロジェクトの *META-INF/* ディレクトリ内に *context.xml* ファイルを作成します。 *META-INF/* ディレクトリが存在しない場合は作成します。

2. *context.xml* で、`Context` 要素を追加してデータ ソースを JNDI アドレスにリンクします。 `driverClassName` プレースホルダーを、上の表にあるドライバーのクラス名に置き換えます。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの *web.xml* を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>構成を完了する

最後に、ドライバーの JAR を Tomcat クラスパス内に配置し、App Service を再起動します。 JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを */home/tomcat/lib* ディレクトリに配置します。 (このディレクトリがまだ存在しない場合は作成します。)これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。

1. [Cloud Shell](https://shell.azure.com) で、Web アプリ拡張機能をインストールします。

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを */home/tomcat/lib* フォルダーにアップロードします。

あるいは、FTP クライアントを使用して JDBC ドライバーをアップロードできます。 [FTP 資格情報を取得するための手順](deploy-configure-credentials.md)に従ってください。

## <a name="configuring-tomcat"></a>Tomcat の構成

Tomcat の `server.xml` または他の構成ファイルを編集するには、最初にポータルで Tomcat のメジャー バージョンを確認してメモします。

1. `env` コマンドを実行して、ご使用のバージョンの Tomcat ホーム ディレクトリを見つけます。 `AZURE_TOMCAT`で始まり、ご使用のメジャー バージョンに一致する環境変数を検索します。 たとえば、`AZURE_TOMCAT85_HOME` は Tomcat 8.5 を指しています。
1. ご使用のバージョンの Tomcat ホーム ディレクトリを特定したら、構成ディレクトリを `D:\home` にコピーします。 たとえば、`AZURE_TOMCAT85_HOME` の値が `D:\Program Files (x86)\apache-tomcat-8.5.37` の場合、コピーされたディレクトリの新しいパスは `D:\home\apache-tomcat-8.5.37` になります。

最後に、お客様のアプリ サービスを再起動します。 デプロイメントの場所は、以前と同様に `D:\home\site\wwwroot\webapps` になります。

## <a name="configure-java-se"></a>Java SE を構成する

Windows 上の Java SE で JAR アプリケーションを実行している場合、`server.port` は、アプリケーションの起動時にコマンドライン オプションとして渡されます。 HTTP ポートは、環境変数 `HTTP_PLATFORM_PORT` から手動で解決できます。 この環境変数の値は、アプリケーションがリッスンする HTTP ポートになります。 

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Windows の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。 Azure 上の Java の詳細については、[こちらのサポート ドキュメント](https://docs.microsoft.com/azure/java/jdk/)を参照してください。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

Tomcat 8.0 は、[2018 年 9 月 30 日にサポートが終了 (EOL)](https://tomcat.apache.org/tomcat-80-eol.html) しました。 ランタイムは Azure App Service でまだ機能しますが、Azure では Tomcat 8.0 へのセキュリティ更新プログラムは適用されません。 可能であれば、Tomcat 8.5 または 9.0 にアプリケーションを移行してください。 Azure App Service では、Tomcat 8.5 と 9.0 の両方を使用できます。 詳細については、[Tomcat の公式 Web サイト](https://tomcat.apache.org/whichversion.html)を参照してください。 

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/azure-only/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行うとき、Microsoft では [Azure がサポートする Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して Azul Zulu JDK に関する[問題を投稿](/azure/azure-portal/supportability/how-to-create-azure-support-request)できます。

## <a name="next-steps"></a>次のステップ

このトピックでは、Azure App Service for Windows の Java ランタイムのサポート ステートメントを提供します。

- Azure App Service での Web アプリケーションのホストの詳細については、「[App Service の概要](overview.md)」を参照してください。
- Azure 開発に関する Java については、「[Java 開発者向けの Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable)」を参照してください。
