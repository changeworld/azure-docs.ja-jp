<properties 
   pageTitle="AMQP 1.0 での Service Bus と .NET | Microsoft Azure"
   description="AMQP で .NET から Service Bus を使用します。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/15/2015"
   ms.author="sethm" />

# AMQP 1.0 で .NET から Service Bus を使用する

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Service Bus SDK のダウンロード

AMQP 1.0 は、Service Bus SDK Version 2.1 以降でサポートされています。最新の SDK は、[NuGet][] からダウンロードできます。

## AMQP 1.0 を使用するように .NET アプリケーションを構成する

既定では、Service Bus .NET クライアント ライブラリは SOAP ベースの専用プロトコルを使ってサービス バス サービスと通信します。既定のプロトコルの代わりに AMQP 1.0 を使用するには、次のセクションの説明に従って、Service Bus 接続文字列を明示的に構成する必要があります。AMQP 1.0 を使用する際、アプリケーション コードはこの変更以外には基本的に変更されません。

現在のリリースでは、AMQP を使用する際にサポートされていない API 機能がいくつかあります。サポートされていない機能については、後ほど「サポートされていない機能、制限、および動作の違い」セクションで示します。 そのほか、AMQP を使用すると意味が変わる詳細な構成設定もいくつかあります。

### App.config を使用した構成

アプリケーションの設定は、App.config 構成ファイルを使って保存することをお勧めします。Service Bus アプリケーションの場合、App.config を使って Service Bus の **ConnectionString** 値の設定を保存できます。App.config ファイルの例は次のとおりです。

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

`Microsoft.ServiceBus.ConnectionString` 設定の値は、Service Bus への接続を構成するために使用される Service Bus 接続文字列です。その形式は次のとおりです。

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

この場合の `[namespace]` と `SharedAccessKey` は Azure ポータルから取得されます。詳細については、「[Service Bus キューの使用方法][]」を参照してください。

AMQP を使用する場合は、接続文字列に `;TransportType=Amqp` を付加します。この表記により、クライアント ライブラリに対して、AMQP 1.0 を使用して Service Bus に接続するように通知します。

## メッセージのシリアル化

既定のプロトコルを使用する場合、.NET クライアント ライブラリの既定のシリアル化の動作として、クライアント ライブラリと Service Bus サービスの間のトランスポートのために [DataContractSerializer][] 型を使用して [BrokeredMessage][] インスタンスをシリアル化します。AMQP トランスポート モードの使用時に、クライアント ライブラリは、[ブローカー メッセージ][BrokeredMessage]を AMQP メッセージにシリアル化するために AMQP 型システムを使用します。このシリアル化によって、別のプラットフォームで実行されている可能性のある受信側アプリケーション (JMS API を使用して Service Bus にアクセスする Java アプリケーションなど) で、メッセージを受信して解釈できるようになります。

[BrokeredMessage][] インスタンスを構築すると、メッセージの本文として機能する .NET オブジェクトをパラメーターとしてコンストラクターに提供できます。AMQP プリミティブ型にマップできるオブジェクトの場合、本文は AMQP データ型にシリアル化されます。オブジェクトを AMQP プリミティブ型 (つまり、アプリケーションによって定義されているカスタム型) に直接マップできない場合、オブジェクトは [DataContractSerializer][] を使用してシリアル化され、シリアル化されたバイトが AMQP データ メッセージで送信されます。

.NET 以外のクライアントとの相互運用性を高めるには、メッセージ本文の AMQP 型に直接シリアル化できる .NET 型のみを使用してください。次の表は、それらの型と、対応する AMQP 型システムへのマッピングを示します。

| .NET 本文のオブジェクト型 | 対応する AMQP の型 | AMQP 本文セクションの型 |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool | boolean | AMQP 値 |
| byte | ubyte | AMQP 値 |
| ushort | ushort | AMQP 値 |
| uint | uint | AMQP 値 |
| ulong | ulong | AMQP 値 |
| sbyte | byte | AMQP 値 |
| short | short | AMQP 値 |
| int | int | AMQP 値 |
| long | long | AMQP 値 |
| float | float | AMQP 値 |
| double | double | AMQP 値 |
| 小数点 | decimal128 | AMQP 値 |
| char | char | AMQP 値 |
| DateTime | timestamp | AMQP 値 |
| Guid | uuid | AMQP 値 |
| byte | binary | AMQP 値 |
| string | string | AMQP 値 |
| System.Collections.IList | list | AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Array | array | AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Collections.IDictionary | map | AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されている項目のみです。注: String キーのみがサポートされます。 |
| Uri | 記述子付き string (次の表を参照) | AMQP 値 |
| DateTimeOffset | 記述子付き long (次の表を参照) | AMQP 値 |
| TimeSpan | 記述子付き long (次の表を参照) | AMQP 値 |
| Stream | binary | AMQP データ (複数の場合あり)。データ セクションには、Stream オブジェクトから読み取られた未加工のバイトが格納されます。 |
| その他のオブジェクト | binary | AMQP データ (複数の場合あり)。DataContractSerializer またはアプリケーションから提供されるシリアライザーを使用するオブジェクトのシリアル化されたバイナリが格納されます。 |

| .NET 型 | 対応する AMQP の記述子付き型 | メモ |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## サポートされていない機能、制限、および動作の違い

AMQP を使用する場合、Service Bus .NET API の次の機能は、現在サポートされていません。

-   トランザクション。

-   転送先を経由した送信。

-   メッセージ シーケンス番号による受信。

-   メッセージとセッションの参照。

-   セッションの状態。

-   バッチベースの API。

-   受信のスケールアウト。

-   サブスクリプション ルールの実行時の操作。

-   セッション ロックの更新。

現在、AMQP を使用する際にサポートされていない API を具体的に以下に示します。

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession][]
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek][]
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState][]
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)][]
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock][]

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock][]

また、Service Bus .NET API の動作は、AMQP を使用する場合と既定のプロトコルを使用する場合では以下のようないくつかの細かい違いがあります。

-   [OperationTimeout][] プロパティは無視されます。

-   `MessageReceiver.Receive(TimeSpan.Zero)` は `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` として実装されます。

## AMQP プロトコル設定を制御する

.NET API では、AMQP プロトコルの動作を制御するいくつかの設定が公開されています。

-   **MessageReceiver.PrefetchCount**: リンクに適用する初期のクレジットを制御します。既定値は 0 です。

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: 接続オープン時のネゴシエーションで提供される最大 AMQP フレーム サイズを制御します。既定値は 65,536 バイトです。

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: 転送がバッチ可能である場合、この値でディスポジションを送信する場合の最大遅延が決まります。既定では、送信側/受信側によって継承されます。個々の送信側/受信側は、既定値 (20 ミリ秒) を上書きできます。

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: AMQP 接続を SSL 接続で確立するかどうかを制御します。既定値は **true** です。

## 次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

- [Service Bus AMQP の概要]
- [Windows Server 用 Service Bus の AMQP]

  [Service Bus キューの使用方法]: service-bus-dotnet-how-to-use-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.GetState]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx
  [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[Windows Server 用 Service Bus の AMQP]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=Oct15_HO4-->