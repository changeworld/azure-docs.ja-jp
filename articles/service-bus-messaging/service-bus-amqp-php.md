---
title: "AMQP 1.0 での Service Bus と PHP | Microsoft Docs"
description: "AMQP で PHP から Service Bus を使用します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dfb26c2b-41d3-4ed6-936b-b8d2f1dbd470
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 122865f056d6bd7fc8c75665d29753542a83405c


---
# <a name="using-service-bus-from-php-with-amqp-10"></a>AMQP 1.0 で PHP から Service Bus を使用する
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP は Proton-C にバインドされている PHP 言語です。つまり、Proton-PHP は、C で実装されているエンジンのラッパーとして実装されています。

## <a name="downloading-the-proton-client-library"></a>Proton クライアント ライブラリをダウンロードする
Proton-C および関連するバインド (PHP を含む) は、[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) からダウンロードできます。 ソース コード形式のファイルがダウンロードされます。 コードをビルドするには、ダウンロードしたパッケージに含まれる指示に従って操作してください。

> [!IMPORTANT]
> このドキュメントの執筆時点で、Proton-C での SSL サポートは Linux オペレーティング システムでのみ提供されています。 Azure Service Bus では SSL を使用する必要があるため、Proton-C (および言語バインド) は、現時点では Linux から Service Bus へのアクセスにのみ使用できます。 Windows で Proton-C と SSL を使用できるようにするための作業が進行中です。更新情報を頻繁に確認してください。
> 
> 

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>PHP から Service Bus のキュー、トピック、サブスクリプションを使用する
次のコードは、Service Bus メッセージング エンティティからメッセージを送受信する方法を示しています。

### <a name="sending-messages-using-proton-php"></a>Proton-PHP を使用してメッセージを送信する
次のコードは、Service Bus メッセージング エンティティにメッセージを送信する方法を示しています。

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Proton-PHP を使用してメッセージを受信する
次のコードは、Service Bus メッセージング エンティティからメッセージを受信する方法を示しています。

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>.NET と Proton-PHP 間のメッセージング
### <a name="application-properties"></a>Application properties
#### <a name="protonphp-to-service-bus-net-apis"></a>Proton-PHP から Service Bus .NET API へ
Proton-PHP メッセージでサポートされるアプリケーションのプロパティの型は、**integer**、**double**、**Boolean**、**string**、**object** です。 次の PHP コードは、これらのプロパティの型を使用してメッセージにプロパティを設定する方法を示しています。

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Service Bus .NET API では、メッセージ アプリケーションのプロパティは [BrokeredMessage][BrokeredMessage] の **Properties** コレクションで伝達されます。 次のコードは、PHP クライアントから受信したメッセージのアプリケーションのプロパティを読み取る方法を示しています。

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

次の表は、PHP プロパティの型と .NET プロパティの型の対応を示しています。

| PHP プロパティの型 | .NET プロパティの型 |
| --- | --- |
| integer |int |
| double |double |
| boolean |bool |
| string |string |
| オブジェクト |オブジェクト |

#### <a name="service-bus-net-apis-to-php"></a>Service Bus .NET API から PHP へ
[BrokeredMessage][BrokeredMessage] 型でサポートされるアプリケーションのプロパティの型は、**byte**、**sbyte**、**char**、**short**、**ushort**、**int**、**uint**、**long**、**ulong**、**float**、**double**、**decimal**、**bool**、**Guid**、**string**、**Uri**、**DateTime**、**DateTimeOffset**、**TimeSpan** です。 次の .NET コードは、これらのプロパティの各型を使用して [BrokeredMessage][BrokeredMessage] オブジェクトにプロパティを設定する方法を示しています。

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

次の PHP コードは、Service Bus .NET クライアントから受信したメッセージのアプリケーションのプロパティを読み取る方法を示しています。

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

次の表は、.NET プロパティの型と PHP プロパティの型の対応を示しています。

| .NET プロパティの型 | PHP プロパティの型 | メモ |
| --- | --- | --- |
| byte |integer |- |
| sbyte |integer |- |
| char |Char |Proton-PHP クラス |
| short |integer |- |
| ushort |integer |- |
| int |integer |- |
| uint |整数 |- |
| long |integer |- |
| ulong |integer |- |
| float |double |- |
| double |double |- |
| Decimal |string |Proton では現在のところ、10 進法はサポートされていません。 |
| bool |boolean |- |
| Guid |UUID |Proton-PHP クラス |
| string |string |- |
| DateTime |integer |- |
| Datetimeoffset |DescribedType |AMQP タイプにマッピングされている DateTimeOffset.UtcTicks:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan |DescribedType |AMQP タイプにマッピングされている Timespan.Ticks:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> |
| Uri |DescribedType |AMQP 型にマッピングされている Uri.AbsoluteUri:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### <a name="standard-properties"></a>標準のプロパティ
次の表は、Proton-PHP の標準のメッセージ プロパティと [BrokeredMessage][BrokeredMessage] の標準のメッセージ プロパティの対応を示します。

| Proton-PHP | Service Bus .NET | メモ |
| --- | --- | --- |
| Durable |該当なし |Service Bus は、持続的なメッセージのみをサポートします。 |
| 優先順位 |該当なし |Service Bus は、単一のメッセージの優先度のみをサポートします。 |
| Ttl |Message.TimeToLive |変換、Proton-PHP TTL はミリ秒単位で定義されます。 |
| first\_acquirer |- |- |
| delivery\_count |- |- |
| ID |Message.Id |- |
| user\_id |- |- |
| Address |Message.To |- |
| [件名] |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationId |- |
| content\_type |Message.ContentType |- |
| content\_encoding |該当なし |- |
| expiry\_time |Message.ExpiresAtUTC |- |
| creation\_time |該当なし |- |
| group\_id |Message.SessionId |- |
| group\_sequence |- |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| 形式 |該当なし |- |

#### <a name="service-bus-net-apis-to-proton-php"></a>Service Bus .NET API から Proton-PHP へ
| Service Bus .NET | Proton-PHP | メモ |
| --- | --- | --- |
| ContentType |Message-\>content\_type |- |
| CorrelationId |Message-\>correlation\_id |- |
| EnqueuedTimeUtc |Message-\>annotations[x-opt-enqueued-time] |- |
| ラベル |Message-\>subject |- |
| MessageId |Message-\>id |- |
| ReplyTo |Message-\>reply\_to |- |
| ReplyToSessionId |Message-\>reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |Message-\>annotations ["x-opt-scheduled-enqueue-time"] |- |
| SessionId |Message-\>group\_id |- |
| TimeToLive |Message-\>ttl |変換、Proton-PHP TTL はミリ秒単位で定義されます。 |
| To |Message-\>address |- |

## <a name="next-steps"></a>次のステップ
さらに詳しい情報については、 次のリンク先を参照してください。

* [Service Bus AMQP の概要]
* [Windows Server 用 Service Bus の AMQP]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Windows Server 用 Service Bus の AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
[Service Bus AMQP の概要]: service-bus-amqp-overview.md



<!--HONumber=Nov16_HO3-->


