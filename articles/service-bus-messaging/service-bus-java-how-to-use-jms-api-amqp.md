---
title: Java Message Service API と Azure Service Bus で AMQP を使用する
description: Java Message Service (JMS) API を Azure Service Bus と Advanced Message Queuing Protodol (AMQP) 1.0 と共に使用する方法。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119159"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Azure Service Bus と AMQP 1.0 で Java Message Service (JMS) を使用する

Azure Service Bus での **Advanced Message Queuing Protocol (AMQP) 1.0** のサポートにより、ブローカー メッセージング機能 (キューおよびトピック発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。 さらに、さまざまな言語、フレームワーク、およびオペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

この記事では、一般的な **Java Message Service (JMS)** API を AMQP プロトコルを介して使用して Java アプリケーションから Azure Service Bus のメッセージング機能 (キューおよびトピック発行/サブスクライブ) を使用する方法について説明します。

## <a name="get-started-with-service-bus"></a>Service Bus の概要
このガイドでは、`basicqueue` という名前のキューが含まれる Service Bus 名前空間が既にあることを前提とします。 まだない場合は、[Azure portal](https://portal.azure.com) を使用して[名前空間とキューを作成する](service-bus-create-namespace-portal.md)ことができます。 Service Bus 名前空間とキューの作成方法の詳細については、「[Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)」を参照してください。

> [!NOTE]
> パーティション分割されたキューおよびトピックも AMQP をサポートします。 詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」と「[パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート](service-bus-partitioned-queues-and-topics-amqp-overview.md)」を参照してください。
> 
>

## <a name="what-jms-features-are-supported"></a>どのような JMS 機能がサポートされていますか?

Azure Service Bus でサポートされている JMS 機能を次に示します。

| 特徴 | Azure Service Bus Standard レベル - JMS 1.1 | Azure Service Bus Premium レベル - JMS 2.0 (プレビュー) |
|---|---|---|
| AMQP 経由でのエンティティの自動作成 | サポートされていません | **サポートされています** |
| キュー | **サポートされています** | **サポートされています** |
| トピック | **サポートされています** | **サポートされています** |
| 一時キュー | サポートされていません <br/> (代わりに *AutoDeleteOnIdle* セットで通常のキューを作成します) | **サポートされています** |
| 一時トピック | サポートされていません | **サポートされています** |
| メッセージ セレクター | サポートされていません | **サポートされています** |
| キュー ブラウザー | サポートされていません <br/> (Service Bus API の *Peek* 機能を使用してください) | **サポートされています** |
| 共有の永続的サブスクリプション | **サポートされています** | **サポートされています**|
| 非共有の永続的サブスクリプション | サポートされていません | **サポートされています** |
| 共有の非永続的サブスクリプション | サポートされていません | **サポートされています** |
| 非共有の非永続的サブスクリプション | サポートされていません | **サポートされています** |
| 永続的サブスクリプションの登録を解除する | サポートされていません | **サポートされています** |
| ReceiveNoWait | サポートされていません | **サポートされています** |
| 分散トランザクション | サポートされていません | サポートされていません |
| 永続的な終端 | サポートされていません | サポートされていません |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Service Bus Standard レベルの追加の注意事項
**Session** ごとに作成できる **MessageProducer** または **MessageConsumer** は 1 つのみです。 アプリケーションで複数の **MessageProducers** または **MessageConsumers** を作成する必要がある場合は、それぞれに専用の**セッション**を作成してください。

## <a name="downloading-the-java-message-service-jms-client-library"></a>Java Message Service (JMS) クライアント ライブラリのダウンロード

Azure Service Bus に接続し、AMQP 経由で Java Message Service (JMS) API を活用するには、以下のライブラリを活用する必要があります。 これらは、プロジェクトに適した依存関係管理ツールを使用してビルド パスに追加する必要があります。

必要なクライアント ライブラリは、使用している価格レベルによって異なります。

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Premium レベル - JMS 2.0 over AMQP (プレビュー)

Azure Service Bus Premium レベルで利用できるすべてのプレビュー機能を利用するには、[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) ライブラリを利用します。

### <a name="standard-tier---jms-11-over-amqp"></a>Standard レベル - JMS 1.1 over AMQP

Service Bus Standard レベルでサポートされている JMS 機能を利用するには (「[どのような JMS 機能がサポートされていますか?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)」を参照) 以下のライブラリを利用してください。

* [Geronimo JMS 1.1 spec](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Qpid JMS クライアント](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS JAR 名およびバージョンが変更されている可能性があります。 詳細については、[Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10) を参照してください。
>

## <a name="coding-java-applications"></a>Java アプリケーションのコーディング

依存関係がインポートされると、Java アプリケーションを JMS プロバイダーに依存しない方法で記述できます。

Azure Service Bus Standard と Premium は、依存関係とサポートされる JMS 機能の数が異なるため、これら 2 つのプログラミング モデルは少し異なります。

> [!IMPORTANT]
> 次のガイドでは、単純なアプリケーションを通じて、Azure Service Bus に接続する方法を紹介します。
>
> ほとんどのエンタープライズ アプリケーション アーキテクチャには、依存関係と構成を管理するための独自の方法がある場合があるため、以下は必要なものを理解し、独自のアプリケーションに適切に適応するためのガイドとして使用してください。
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS を使用した Azure Service Bus への接続

JMS クライアントを使用して Azure Service Bus に接続するには、**ConnectionString** が必要です。これは、[[Azure portal]](https://portal.azure.com) の **[プライマリ接続文字列]** にある '共有アクセス ポリシー' で使用できます。


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>JMS 2.0 経由で Azure Service Bus Premium に接続する (プレビュー)

1. `ServiceBusJmsConnectionFactorySettings` をインスタンス化します
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. 適切な `ServiceBusConnectionString` を使用して `ServiceBusJmsConnectionFactory` をインスタンス化します。
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. `ConnectionFactory` を使用して `Connection` を作成した後に `Session` を作成するか 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    または `JMSContext` を作成します (JMS 2.0 クライアントの場合)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>JMS 1.1 経由で Azure Service Bus Standard に接続する

1. Azure Service Bus 構成を **servicebus.properties** という名前の JNDI プロパティ ファイルに挿入します。
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. JNDI コンテキストのセットアップと ConnectionFactory の構成
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. `ConnectionFactory` を使用して `Connection`、および `Session` の順番に作成します。
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>JMS アプリケーションの記述

`Session` または `JMSContext` がインスタンス化されると、アプリケーションで使い慣れた JMS API を利用して管理操作とデータ操作の両方を実行できます。

各レベルでサポートされる API を確認するには、Standard レベルと Premium レベルの両方で[サポートされている MS 機能](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)の一覧を参照してください。

## <a name="summary"></a>まとめ
このガイドでは、AMQP 1.0 経由で Java Message Service (JMS) を使用する Java クライアント アプリケーションと Azure Service Bus がどのように対話するかについて説明しました。

Service Bus AMQP 1.0 のサポートは、.NET、C、Python、PHP など、その他の言語からも使用できます。 Service Bus で AMQP 1.0 のサポートを使用すると、これらのさまざまな言語を使って作成されたコンポーネントで高い信頼性と十分な忠実度のメッセージ交換が実現されます。

## <a name="next-steps"></a>次のステップ

Azure Service Bus と Java Message Service (JMS) エンティティの詳細については、以下のリンクを参照してください。 
* [Service Bus - キュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java メッセージ サービス エンティティ](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0: 開発者ガイド](service-bus-amqp-dotnet.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)

