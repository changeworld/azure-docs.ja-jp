---
title: App Service on Linux 用の Java 開発者ガイド - Azure | Microsoft Docs
description: Azure App Service on Linux で実行される Java アプリを構成するについて説明します。
keywords: Azure App Service, Web アプリ, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 6f6dac37d1114e8a9faa16c07fd5c14a90a5b0fb
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976734"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>App Service on Linux の Java 開発者ガイド

Azure App Service on Linux を使用すると、Java 開発者は、完全に管理された Linux ベースのサービス上で Tomcat または Java Standard Edition (SE) パッケージ化 Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service for Linux を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service for Linux を使用したことがない場合は、まず [Java クイック スタート](quickstart-java.md)をお読みください。 Java 開発に限られない、App Service for Linux の使用に関する一般的な質問については、[App Service Linux の FAQ](app-service-linux-faq.md) で回答されています。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 これらの診断ツールにアクセスして使用する方法の詳細については、「[Azure App Service 診断の概要](/azure/app-service/overview-diagnostics)」を参照してください。

## <a name="application-performance-monitoring"></a>アプリケーション パフォーマンス監視

Linux 上の App Service で実行されている Java アプリで New Relic および AppDynamics を構成する手順については、[Linux 上での Azure App Service の Java アプリを使用したアプリケーション パフォーマンス監視ツール](how-to-java-apm-monitoring.md)に関する記事を参照してください。

### <a name="ssh-console-access"></a>SSH コンソール アクセス 

アプリを実行している Linux 環境への SSH 接続を使用できます。 Web ブラウザーまたはローカル ターミナルを介して Linux システムに接続する完全な手順については、「[Azure App Service on Linux での SSH のサポート](/azure/app-service/containers/app-service-linux-ssh-support)」を参照してください。

### <a name="streaming-logs"></a>ストリーミング ログ

簡単なデバッグとトラブルシューティングを行うために、Azure CLI を使用してコンソールにログをストリーミングできます。 ログ記録を有効にするには、`az webapp log config` を使用して CLI を構成します。

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

その後、`az webapp log tail` を使用してコンソールにログをストリーミングします。

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

詳細については、[Azure CLI を使用したログのストリーミング](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)に関する記事を参照してください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](/azure/app-service/troubleshoot-diagnostic-logs#enablediag)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。

アプリケーションで [Logback](https://logback.qos.ch/) または [Log4j](https://logging.apache.org/log4j) をトレースに使用している場合は、「[Application Insights を使用した Java トレース ログの探索](/azure/application-insights/app-insights-java-trace-logs)」にあるログ記録フレームワークの構成手順に従って、これらのトレースを確認のために Azure Application Insights に転送することができます。 

## <a name="customization-and-tuning"></a>カスタマイズとチューニング

Azure App Service for Linux では、Azure portal および CLI を使用したチューニングとカスタマイズが追加設定なしでサポートされています。 Java 以外の特定の Web アプリの構成については、次の記事を確認してください。

- [App Service の設定の構成](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [カスタム ドメインの設定](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL の有効化](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN の追加](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java ランタイム オプションを設定する

Tomcat と Java SE の両方の環境で、割り当てられたメモリまたはその他の JVM ランタイムのオプションを設定するには、次に示すように[アプリケーション設定](/azure/app-service/web-sites-configure#app-settings)で JAVA_OPTS を設定します。 App Service Linux では、開始時にこの設定が環境変数として Java ランタイムに渡されます。

Azure portal の Web アプリ用の **[アプリケーション設定]** で、`$JAVA_OPTS -Xms512m -Xmx1204m` などの追加の設定を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

Azure App Service Linux Maven プラグインからアプリ設定を構成するには、Azure プラグイン セクションで設定/値のタグを追加します。 次の例では、特定の最小および最大の Java ヒープ サイズを設定します。

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

App Service プランで 1 つのデプロイ スロットを使用して 1 つのアプリケーションを実行している開発者は、次のオプションを使用できます。

- B1 と S1 のインスタンス: -Xms1024m -Xmx1024m
- B2 と S2 のインスタンス: -Xms3072m -Xmx3072m
- B3 と S3 のインスタンス: -Xms6144m -Xmx6144m


アプリケーション ヒープ設定をチューニングする際には、App Service プランの詳細を確認し、複数のアプリケーションおよびデプロイ スロットのニーズを考慮して、メモリの最適な割り当てを特定する必要があります。

### <a name="turn-on-web-sockets"></a>Web ソケットを有効にする

Azure portal のアプリケーション用の **[アプリケーション設定]** で、Web ソケットのサポートを有効にします。 設定を有効にするには、アプリケーションを再起動する必要があります。

Azure CLI を使用して、次のコマンドで Web ソケットのサポートを有効にします。

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

その後、アプリケーションを再起動します。

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>既定の文字エンコーディングを設定する 

Azure portal の Web アプリ用の **[アプリケーション設定]** で、値 `$JAVA_OPTS -Dfile.encoding=UTF-8` を含む、`JAVA_OPTS` という名前の新しいアプリ設定を作成します。

または、App Service Maven プラグインを使用してアプリ設定を構成できます。 プラグイン構成で、設定名および値のタグを追加します。 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-applications"></a>セキュリティで保護されたアプリケーション

App Service for Linux で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](/azure/security/security-paas-applications-using-app-services)を備えています。 

### <a name="authenticate-users"></a>ユーザーを認証する

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。  詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](/azure/app-service/configure-authentication-provider-aad)」と、他の ID プロバイダーの関連記事を参照してください。

複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)に関する記事の手順に従います。

 Spring Boot 開発者は、[Azure Active Directory Spring Boot スターター](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)を使用して、使い慣れた Spring Security の注釈と API を使用してアプリケーションをセキュリティで保護することができます。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

[既存のカスタム SSL 証明書のバインド](/azure/app-service/app-service-web-tutorial-custom-ssl)に関する記事の手順に従って、既存の SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>データ ソースへの接続

>[!NOTE]
> アプリケーションで Spring Boot または Spring Framework を使用している場合は、Spring データ JPA のデータベース接続情報を環境変数として設定できます [アプリケーションのプロパティ ファイル内]。 その後、Azure portal または CLI で、[アプリ設定](/azure/app-service/web-sites-configure#app-settings)を使用してアプリケーションに対してこれらの値を定義することができます。

これらの説明は、すべてのデータベース接続に適用されます。 プレースホルダーを、選択したデータベースのドライバーのクラス名と JAR ファイルに置き換える必要があります。 一般的なデータベースのクラス名とドライバーのダウンロードを含む表を次に示します。

| Database   | ドライバーのクラス名                             | JDBC ドライバー                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [ダウンロード](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [ダウンロード](https://dev.mysql.com/downloads/connector/j/) ("プラットフォームに依存しない" を選択) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [ダウンロード](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用するように Tomcat を構成するには、最初に、起動時に Tomcat によって読み込まれる `CATALINA_OPTS` 環境変数をカスタマイズします。 [App Service Maven プラグイン](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)のアプリ設定を使用して、次の値を設定します。

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value> 
    </property> 
</appSettings> 
```

または、Azure Portal の [アプリケーションの設定] ブレードで環境変数を設定します。

次に、データ ソースを Tomcat サーブレットで実行されている 1 つのアプリケーション、またはすべてのアプリケーションのどちらに対して使用可能にする必要があるかを判定します。

#### <a name="for-application-level-data-sources"></a>アプリケーション レベル データ ソースの場合は、次の手順に従います。 

1. プロジェクトの `META-INF/` ディレクトリ内に `context.xml` ファイルを作成します。 `META-INF/` ディレクトリが存在しない場合は作成します。

2. `context.xml` で、`Context` 要素を追加してデータ ソースを JNDI アドレスにリンクします。 `driverClassName` プレースホルダーを、上の表にあるドライバーのクラス名に置き換えます。

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

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="for-shared-server-level-resources"></a>共有サーバー レベル リソースの場合は、次の手順に従います。

1. 構成がまだ存在しない場合は、SSH を使用して、App Service Linux インスタンス上で `/usr/local/tomcat/conf` の内容を `/home/tomcat/conf` にコピーします。
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. `server.xml` の `<Server>` 要素内に Context 要素を追加します。

    ```xml
    <Server>
    ...
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
    ...
    </Server>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>最後に、ドライバーの JAR を Tomcat クラスパス内に配置し、App Service を再起動します。 

1. JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを `/home/tomcat/lib` ディレクトリに配置します。 (このディレクトリがまだ存在しない場合は作成します。)これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。  
    1. Azure App Service webpp 拡張機能をインストールします。

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを `/home/tomcat/lib` フォルダーにアップロードします。

    あるいは、FTP クライアントを使用して JDBC ドライバーをアップロードできます。 [FTP 資格情報を取得するための手順](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)に従ってください。

2. サーバー レベルのデータ ソースを作成した場合は、App Service Linux アプリケーションを再起動します。 Tomcat が `CATALINA_HOME` を `/home/tomcat/conf` にリセットし、更新された構成を使用します。

## <a name="docker-containers"></a>Docker コンテナー

Azure がサポートしている Zulu JDK をコンテナーで使用するには、[サポートされている Azul Zulu Enterprise for Azure のダウンロード ページ](https://www.azul.com/downloads/azure-only/zulu/)に記載されている事前構築済みのイメージをプルして使用するか、[Microsoft Java GitHub リポジトリ](https://github.com/Microsoft/java/tree/master/docker)にある `Dockerfile` の例を使用するようにします。

## <a name="runtime-availability-and-statement-of-support"></a>ランタイムの可用性とサポート ステートメント

App Service for Linux では、Java Web アプリケーションのマネージド ホスティング用に次の 2 つのランタイムがサポートされています。

- Web アーカイブ (WAR) ファイルとしてパッケージ化されたアプリケーションを実行するための [Tomcat サーブレット コンテナー](https://tomcat.apache.org/)。 サポートされているバージョンは 8.5 と 9.0 です。
- Java アーカイブ (JAR) ファイルとしてパッケージ化されたアプリケーションを実行するための Java SE ランタイム環境。 サポートされているメジャー バージョンは Java 8 だけです。

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント 

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/downloads/azure-only/zulu/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Linux の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/azure-only/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行う際には、[Azure がサポートする Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して Azul Zulu JDK に関する[問題を投稿](/azure/azure-supportability/how-to-create-azure-support-request)できます。

## <a name="next-steps"></a>次の手順

[Java 開発者向けの Azure](/java/azure/) センターにアクセスして、Azure クイック スタート、チュートリアル、および Java リファレンス ドキュメントを入手してください。
