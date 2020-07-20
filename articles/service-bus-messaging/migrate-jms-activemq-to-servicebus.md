---
title: Java Message Service (JMS) アプリケーションを ActiveMQ から Azure Service Bus に移行する |Microsoft Docs
description: この記事では、ActiveMQ を操作する既存の JMS アプリケーションを移行して、Azure Service Bus を操作する方法について説明します。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122225"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>既存の Java Message Service (JMS) 2.0 アプリケーションを ActiveMQ から Azure Service Bus に移行する

Azure Service Bus では、Advanced Message Queuing Protocol (AMQP) プロトコルを介して Java Message Service (JMS) 2.0 API を利用する Java/J2EE および Spring のワークロードがサポートされています。

このガイドでは、JMS ブローカー (具体的には Apache ActiveMQ または Amazon MQ) とやり取りして Azure Service Bus を操作する既存の Java Message Service (JMS) 2.0 アプリケーションを変更する場合に注意すべき事項について説明します。

## <a name="before-you-start"></a>開始する前に

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus と Apache ActiveMQ との相違点

Azure Service Bus と Apache ActiveMQ は両方とも、クライアント アプリケーションがメッセージを送受信するための JMS プロバイダーとして機能するメッセージ ブローカーです。 どちらの場合も、**キュー**でのポイントツーポイント セマンティクスと、**トピック**および**サブスクリプション**での発行 - サブスクライブ セマンティクスが有効になります。 

ただし、両者にはいくつかの違いがあります。

| カテゴリ | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| アプリケーションの階層化 | クラスター化モノリス | 2 層 <br> (ゲートウェイ + バックエンド) |
| プロトコルのサポート | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| プロビジョニング モード | <ul> <li> IaaS (オンプレミス) </li> <li> AmazonMQ (マネージド PaaS) </li> | マネージド PaaS |
| メッセージ サイズ | ユーザーが構成可能 | 1 MB (Premium レベル) |
| 高可用性 | お客様による管理 | プラットフォーム管理 |
| ディザスター リカバリー | お客様による管理 | プラットフォーム管理 | 

### <a name="current-supported-and-unsupported-features"></a>現在サポートされている機能とサポートされていない機能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>注意事項

2 層を持つという Azure Service Bus の性質によって、さまざまな事業継続機能 (高可用性とディザスター リカバリー) が提供されます。 ただし、JMS 機能を利用する際には、いくつかの点を考慮する必要があります。

#### <a name="service-upgrades"></a>サービスのアップグレード

Service Bus をアップグレードまたは再起動する場合、一時キューまたはトピックが削除されます。

アプリケーションが、一時キューまたはトピックにおけるデータ損失の影響を受ける可能性がある場合は、一時キューまたはトピックを使用**しないで**、代わりに永続的なキュー、トピック、およびサブスクリプションを使用することをお勧めします。

#### <a name="data-migration"></a>データ移行

Azure Service Bus を操作するためのクライアント アプリケーションを移行/変更した場合でも、ActiveMQ に保持されているデータは Service Bus には移行されません。

ActiveMQ のキュー、トピック、およびサブスクリプションをドレインし、メッセージを Service Bus のキュー、トピック、およびサブスクリプションに再生する際に、カスタム アプリケーションが必要になる場合があります。

#### <a name="authentication-and-authorization"></a>認証と権限承認

Azure Active Directory によってサポートされるロール ベースの Access Control (RBAC) は、Azure Service Bus に適した認証メカニズムです。

ただし、Apache QPID JMS では要求ベースの認証がサポートされていないため、RBAC は現在サポートされていません。

現時点では、SAS キーを使用した認証のみがサポートされています。

## <a name="pre-migration"></a>移行前

### <a name="version-check"></a>バージョン チェック

以下に、JMS アプリケーションの作成中に使用されるコンポーネントと、サポートされている特定のバージョンを示します。 

| コンポーネント | Version |
|---|---|
| Java Message Service (JMS) API | 1.1 以上 |
| AMQP プロトコル | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>AMQP ポートが開いていることを確認する

Azure Service Bus は、AMQP プロトコルを介した通信をサポートしています。 このため、ポート 5671 (AMQP) と 443 (TCP) を介した通信を有効にする必要があります。 クライアント アプリケーションがホストされている場所によっては、これらのポートを介した通信を許可するためのサポート チケットが必要になる場合があります。

> [!IMPORTANT]
> Azure Service Bus は、AMQP 1.0 プロトコル**のみ**をサポートしています。

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>エンタープライズ構成 (VNET、ファイアウォール、プライベート エンドポイントなど) の設定

Azure Service Bus によって、さまざまなエンタープライズ セキュリティおよび高可用性機能が有効になります。 詳細については、以下のドキュメント リンクを参照してください。

  * [仮想ネットワーク サービス エンドポイント](service-bus-service-endpoints.md)
  * [ファイアウォール](service-bus-ip-filtering.md)
  * [カスタマー マネージド キー (BYOK) を使用したサービス側の暗号化](configure-customer-managed-key.md)
  * [プライベート エンドポイント](private-link-service.md)
  * [認証と承認](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>監視、アラート、トレース

メトリックは、Service Bus 名前空間ごとに Azure Monitor に発行されます。これは、名前空間に割り当てられたリソースのアラートと動的スケーリングに利用できます。

さまざまなメトリックの詳細と、これらのメトリックに対するアラートの設定方法については、[Azure Monitor での Service Bus メトリックに関する説明](service-bus-metrics-azure-monitor.md)を参照してください。

また、データ操作のクライアント側のトレースについては[こちら](service-bus-end-to-end-tracing.md)を、また、管理操作の操作ログ/診断ログについては[こちら](service-bus-diagnostic-logs.md)を参照してください。

### <a name="metrics---newrelic"></a>メトリック - NewRelic

以下では、ActiveMQ のメトリックが Azure Service Bus のどのメトリックにマップされるかについて簡単に説明しています。 以下は、NewRelic から参照されます。

  * [ActiveMQ/Amazon MQ NewRelic メトリック](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus NewRelic メトリック](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 現在、NewRelic は ActiveMQ に直接シームレスに統合されていませんが、NewRelic には Amazon MQ に使用できるメトリックがあります。
> Amazon MQ は ActiveMQ から派生しているため、次のガイドでは、AmazonMQ から Azure Service Bus に NewRelic メトリックがマップされています。
>

|メトリックのグループ化| AmazonMQ/ActiveMQ のメトリック | Azure Service Bus のメトリック |
|------------|---------------------------|--------------------------|
|ブローカー|`CpuUtilization`|`CPUXNS`|
|ブローカー|`MemoryUsage`|`WSXNS`|
|ブローカー|`CurrentConnectionsCount`|`activeConnections`|
|ブローカー|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|ブローカー|`InactiveDurableTopicSubscribersCount`|サブスクリプションのメトリックを活用|
|ブローカー|`TotalMessageCount`|キュー/トピック/サブスクリプション レベル `activeMessages` を活用|
|キュー/トピック|`EnqueueCount`|`incomingMessages`|
|キュー/トピック|`DequeueCount`|`outgoingMessages`|
|キュー|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>移行

既存の JMS 2.0 アプリケーションを移行して Azure Service Bus を操作するには、以下の手順を実行する必要があります。

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>ActiveMQ からトポロジをエクスポートして、Azure Service Bus でエンティティを作成する (省略可能)

クライアント アプリケーションが Azure Service Bus にシームレスに接続できるようにするには、キュー、トピック、サブスクリプションを含むトポロジを **Apache ActiveMQ** から **Azure Service Bus** に移行する必要があります。

> [!NOTE]
> Java Message Service (JMS) アプリケーションでは、キュー、トピック、およびサブスクリプションはランタイム操作で作成します。 ほとんどの Java Message Service (JMS) プロバイダー (メッセージ ブローカー) には、実行に*キュー*、*トピック*、および*サブスクリプション*を作成するための機能があります。
>
> したがって、上記の手順は省略可能です。
>
> アプリケーションが実行時にトポロジを作成するための権限を持っていることを確認するには、***SAS ' 管理 '*** 権限を含む接続文字列が使用されていることを確認してください。

目的 
  * [ActiveMQ コマンドライン ツール](https://activemq.apache.org/activemq-command-line-tools-reference) を利用してトポロジをエクスポートします。
  * [Azure Resource Manager テンプレート](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を使用して同じトポロジを再作成します。
  * Azure Resource Manager テンプレートを実行します。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Service Bus JMS 実装の Maven の依存関係をインポートする

Azure Service Bus とシームレスに接続できるようにするには、次のように、***azure-servicebus-jms*** パッケージを Maven `pom.xml` ファイルに依存関係として追加する必要があります。

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>アプリケーション サーバー構成の変更

この部分は、ActiveMQ に接続するクライアント アプリケーションをホストしているアプリケーション サーバーによって異なります。

#### <a name="tomcat"></a>Tomcat

ここでは、`/META-INF/context.xml` ファイルに示されている ActiveMQ に固有の構成を使用します。

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

次のように調整して、Azure Service Bus を指定することができます。

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Spring アプリケーション

##### <a name="update-applicationproperties-file"></a>`application.properties` ファイルを更新する

Spring Boot アプリケーションを使用して ActiveMQ に接続する場合です。

ここでの目的は、`application.properties` ファイルから ActiveMQ 固有のプロパティを***削除***することです。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

次に、Service Bus 固有のプロパティを `application.properties` ファイルに***追加***します。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>ActiveMQConnectionFactory を ServiceBusJmsConnectionFactory に置き換える

次の手順では、ActiveMQConnectionFactory のインスタンスを ServiceBusJmsConnectionFactory に置き換えます。

> [!NOTE] 
> 実際にコードをどのように変更するかは、アプリケーションおよび依存関係の管理方法によって異なります。以下のサンプルでは、***変更する必要がある***コードを示しています。
>

これまでに、ActiveMQConnectionFactory のオブジェクトを次のようにインスタンス化している可能性があります。

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

このコードを ServiceBusJmsConnectionFactory のオブジェクトをインスタンス化するように変更します。

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>移行後

Azure Service Bus でメッセージの送受信を開始するようにアプリケーションを変更したので、その機能が想定どおりに動作することを確認する必要があります。 その後、アプリケーション スタックをさらに調整し、最新化することができます。

## <a name="next-steps"></a>次のステップ

[Azure Service Bus JMS 用の Spring Boot スターター](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus)を使用して、Azure Service Bus にシームレスに統合します。

Service Bus メッセージングと Java Message Service (JMS) の詳細については、次のトピックをご覧ください。

* [Service Bus - JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
