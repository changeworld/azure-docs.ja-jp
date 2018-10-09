---
title: Azure App Service on Linux での Java 言語のサポート | Microsoft Docs
description: Azure App Service on Linux で Java アプリをデプロイするための開発者ガイド。
keywords: Azure App Service, Web アプリ, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 2b2256ef5802160dbaa66e2a098a798fcdc653d2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064504"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>App Service on Linux の Java 開発者ガイド

Azure App Service on Linux を使用すると、Java 開発者は、完全に管理された Linux ベースのサービス上で Tomcat または Java Standard Edition (SE) パッケージ化 Web アプリケーションを迅速にビルド、デプロイ、およびスケーリングすることができます。 アプリケーションは、コマンドラインから Maven プラグインを使用して、または IntelliJ、Eclipse、Visual Studio Code などのエディターでデプロイします。

このガイドでは、App Service for Linux を使用する Java 開発者向けに主要な概念と手順を示します。 Azure App Service for Linux を使用したことがない場合は、まず [Java クイック スタート](quickstart-java.md)をお読みください。 Java 開発に限られない、App Service for Linux の使用に関する一般的な質問については、[App Service Linux の FAQ](app-service-linux-faq.md) で回答されています。

## <a name="logging-and-debugging-apps"></a>アプリのログ記録とデバッグ

パフォーマンス レポート、トラフィックの視覚エフェクト、および正常性検査は、Azure portal を介して各アプリに対して使用できます。 これらの診断ツールにアクセスして使用する方法の詳細については、「[Azure App Service 診断の概要](/azure/app-service/app-service-diagnostics)」を参照してください。

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

詳細については、[Azure CLI を使用したログのストリーミング](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface)に関する記事を参照してください。

### <a name="app-logging"></a>アプリのログ記録

Azure portal または [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) を使用して[アプリケーションのログ記録](/azure/app-service/web-sites-enable-diagnostic-log#enablediag)を有効にし、アプリケーションの標準コンソール出力および標準コンソール エラー ストリームをローカル ファイル システムまたは Azure BLOB ストレージに書き込むよう App Service を構成します。 App Service のローカル ファイル システム インスタンスへのログ記録は、構成されてから 12 時間後に無効になります。 リテンション期間を長くする必要がある場合は、BLOB ストレージ コンテナーに出力を書き込むようアプリケーションを構成します。

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

## <a name="secure-application"></a>セキュリティで保護されたアプリケーション

App Service for Linux で実行される Java アプリケーションは、他のアプリケーションと同じ一連の[セキュリティのベスト プラクティス](/azure/security/security-paas-applications-using-app-services)を備えています。 

### <a name="authenticate-users"></a>ユーザーを認証する

**[認証/承認]** オプションを使用して、Azure portal でアプリ認証を設定します。 そこから、Azure Active Directory、または Facebook、Google、GitHub などのソーシャル ログインを使用して、認証を有効にすることができます。 Azure portal の構成は、1 つの認証プロバイダーを構成するときにのみ機能します。  詳細については、「[Azure Active Directory ログインを使用するよう App Service アプリを構成する](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)」と、他の ID プロバイダーの関連記事を参照してください。

複数のサインイン プロバイダーを有効にする必要がある場合は、[App Service 認証のカスタマイズ](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)に関する記事の手順に従います。

 Spring Boot 開発者は、[Azure Active Directory Spring Boot スターター](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)を使用して、使い慣れた Spring Security の注釈と API を使用してアプリケーションをセキュリティで保護することができます。

### <a name="configure-tlsssl"></a>TLS/SSL を構成する

[既存のカスタム SSL 証明書のバインド](/azure/app-service/app-service-web-tutorial-custom-ssl)に関する記事の手順に従って、既存の SSL 証明書をアップロードし、アプリケーションのドメイン名にバインドします。 既定では、アプリケーションで引き続き HTTP 接続が許可されます。チュートリアルの特定の手順に従って、SSL と TLS を適用します。

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>データ ソースへの接続

>[!NOTE]
> アプリケーションで Spring Boot または Spring Framework を使用している場合は、Spring データ JPA のデータベース接続情報を環境変数として設定できます [アプリケーションのプロパティ ファイル内]。 その後、Azure portal または CLI で、[アプリ設定](/azure/app-service/web-sites-configure#app-settings)を使用してアプリケーションに対してこれらの値を定義することができます。

Java Database Connectivity (JDBC) または Java Persistence API (JPA) を使用して、データベースへのマネージド接続を使用するよう Tomcat を構成するには、まず起動時に Tomcat によって読み取られる CATALINA_OPTS 環境変数をカスタマイズします。 App Service Maven プラグインで、アプリ設定を使用してこれらの値を設定します。

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

または、Azure portal の同等の App Service 設定を使用します。

次に、1 つのアプリケーションのみに対して、または App Service プランで実行されているすべてのアプリケーションに対して、データ ソースを使用可能にする必要があるかを判断します。

アプリケーション レベル データ ソースの場合は、次の手順に従います。 

1. Web アプリケーションに `context.xml` ファイルが存在しない場合は追加し、プロジェクトがビルドされたら WAR ファイルの `META-INF` ディレクトリにこのファイルを追加します。

2. このファイルで、`Context` パス エントリを追加して、データ ソースを JNDI アドレスにリンクします。 その

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

共有サーバー レベル リソースの場合は、次の手順に従います。

1. 構成がまだ存在しない場合は、SSH を使用して、App Service Linux インスタンス上で `/usr/local/tomcat/conf` の内容を `/home/tomcat` にコピーします。

2. `server.xml` にコンテキストを追加する

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. アプリケーションでこのデータ ソースを使用するよう、アプリケーションの `web.xml` を更新します。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. JDBC ドライバー ファイルが Tomcat クラスローダーで確実に使用できるように、これらのファイルを `/home/tomcat/lib` ディレクトリに配置します。 これらのファイルを App Service インスタンスにアップロードするには、次の手順を行います。  
    1. Azure App Service webpp 拡張機能をインストールします。
      ```azurecli-interactive
      az extension add –name webapp
      ```
    2. 次の CLI コマンドを実行して、ローカル システムから App Service への SSH トンネルを作成します。
      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```
    3. SFTP クライアントを使用してローカルのトンネル ポートに接続し、ファイルを `/home/tomcat/lib` にアップロードします。

5. App Service Linux アプリケーションを再起動します。 Tomcat によって `CATALINA_HOME` が `/home/tomcat` にリセットされ、更新された構成およびクラスが使用されます。

## <a name="docker-containers"></a>Docker コンテナー

コンテナー内の App Service で実行されている Azure 対応 Zulu JDK を使用するには、アプリケーションの `Dockerfile` で [Java App Service Docker イメージ リポジトリ](https://github.com/Azure-App-Service/java)のイメージが確実に使用されるようにします。

## <a name="runtime-availability-and-statement-of-support"></a>ランタイムの可用性とサポート ステートメント

App Service for Linux では、Java Web アプリケーションのマネージド ホスティング用に次の 2 つのランタイムがサポートされています。

- Web アーカイブ (WAR) ファイルとしてパッケージ化されたアプリケーションを実行するための [Tomcat サーブレット コンテナー](http://tomcat.apache.org/)。 サポートされているバージョンは 8.5 と 9.0 です。
- Java アーカイブ (JAR) ファイルとしてパッケージ化されたアプリケーションを実行するための Java SE ランタイム環境。 サポートされているメジャー バージョンは Java 8 だけです。

## <a name="java-runtime-statement-of-support"></a>Java ランタイムのサポート ステートメント 

### <a name="jdk-versions-and-maintenance"></a>JDK のバージョンとメンテナンス

Azure でサポートされている Java Development Kit (JDK) は、[Azul Systems](https://www.azul.com/) で提供されている [Zulu](https://www.azul.com/products/zulu-and-zulu-enterprise/) です。

メジャー バージョンの更新プログラムは、Azure App Service for Linux の新しいランタイム オプションによって提供されます。 お客様は、App Service デプロイを構成することで Java のこれらの新しいバージョンに更新します。また、主要な更新プログラムをテストし、ニーズを満たしていることを確認する必要があります。

サポートされている JDK は、毎年 1 月、4 月、7 月、および 10 月の四半期ごとに自動的に適用されます。

### <a name="security-updates"></a>セキュリティ更新プログラム

重大なセキュリティの脆弱性のパッチおよび修正プログラムは、Azul Systems から利用可能になり次第リリースされます。 "重大な" 脆弱性は、[NIST Common Vulnerability Scoring System バージョン 2](https://nvd.nist.gov/cvss.cfm) で 9.0 以上の基本スコアにより定義されます。

### <a name="deprecation-and-retirement"></a>廃止と提供終了

サポートされている Java ランタイムが廃止予定の場合、影響を受けるランタイムを使用している Azure の開発者には、ランタイム終了の少なくとも 6 か月前までに廃止の通知が送信されます。

### <a name="local-development"></a>ローカル開発

開発者は、[Azul のダウンロード サイト](https://www.azul.com/downloads/zulu/)から運用エディションの Azul Zulu Enterprise JDK をローカルでのデプロイ用にダウンロードできます。

### <a name="development-support"></a>開発サポート

[正規の Azure サポート プラン](https://azure.microsoft.com/support/plans/)を利用して Azure または [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 用の開発を行う際には、Azul Zulu Enterprise JDK の製品サポートを利用できます。

### <a name="runtime-support"></a>ランタイム サポート

[正規のサポート プラン](https://azure.microsoft.com/support/plans/)の対象である開発者は、Azure サポートを利用して App Service Linux の Java ランタイムに関する[問題を投稿](/azure/azure-supportability/how-to-create-azure-support-request)できます。

## <a name="next-steps"></a>次の手順

[Java 開発者向けの Azure](/java/azure/) センターにアクセスして、Azure クイック スタート、チュートリアル、および Java リファレンス ドキュメントを入手してください。
