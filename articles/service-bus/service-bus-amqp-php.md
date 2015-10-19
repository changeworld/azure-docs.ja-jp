<properties 
   pageTitle="AMQP 1.0 での Service Bus と PHP | Microsoft Azure"
   description="AMQP で PHP から Service Bus を使用します。"
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
   ms.date="10/07/2015"
   ms.author="sethm" />

# AMQP 1.0 で PHP から Service Bus を使用する

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP は Proton-C にバインドされている PHP 言語です。つまり、Proton-PHP は、C で実装されているエンジンのラッパーとして実装されています。

## Proton クライアント ライブラリをダウンロードする

Proton-C および関連するバインド (PHP を含む) は、[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) からダウンロードできます。ソース コード形式のファイルがダウンロードされます。コードをビルドするには、ダウンロードしたパッケージに含まれる指示に従って操作してください。

> [AZURE.IMPORTANT]このドキュメントの執筆時点で、Proton-C での SSL サポートは Linux オペレーティング システムでのみ提供されています。Azure Service Bus では SSL を使用する必要があるため、Proton-C (および言語バインド) は、現時点では Linux から Service Bus へのアクセスにのみ使用できます。Windows で Proton-C と SSL を使用できるようにするための作業が進行中です。更新情報を頻繁に確認してください。

## PHP から Service Bus のキュー、トピック、サブスクリプションを使用する

次のコードは、Service Bus メッセージング エンティティからメッセージを送受信する方法を示しています。

### Proton-PHP を使用してメッセージを送信する

次のコードは、Service Bus メッセージング エンティティにメッセージを送信する方法を示しています。

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### Proton-PHP を使用してメッセージを受信する

次のコードは、Service Bus メッセージング エンティティからメッセージを受信する方法を示しています。

```
$messenger = new Messenger();
$address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";
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

## .NET と Proton-PHP 間のメッセージング

### アプリケーションのプロパティ

#### Proton-PHP から Service Bus .NET API へ

Proton-PHP メッセージでサポートされるアプリケーションのプロパティの型は、**integer**、**double**、**Boolean**、**string**、**object** です。次の PHP コードは、これらのプロパティの型を使用してメッセージにプロパティを設定する方法を示しています。

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Service Bus .NET API では、メッセージ アプリケーションのプロパティは [BrokeredMessage][] の **Properties** コレクションで伝達されます。次のコードは、PHP クライアントから受信したメッセージのアプリケーションのプロパティを読み取る方法を示しています。

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
|-------------------|--------------------|
| integer | int |
| double | double |
| boolean | bool |
| string | string |
| オブジェクト | オブジェクト |

#### Service Bus .NET API から PHP へ

[BrokeredMessage][] 型でサポートされるアプリケーションのプロパティの型は、**byte**、**sbyte**、**char**、**short**、**ushort**、**int**、**uint**、**long**、**ulong**、**float**、**double**、**decimal**、**bool**、**Guid**、**string**、**Uri**、**DateTime**、**DateTimeOffset**、**TimeSpan** です。次の .NET コードは、これらのプロパティの各型を使用して [BrokeredMessage][] オブジェクトにプロパティを設定する方法を示しています。

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
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | integer | - | | sbyte | integer | - | | char | Char | Proton-PHP class | | short | integer | - | | ushort | integer | - | | int | integer | - | | uint | Integer | - | | long | integer | - | | ulong | integer | - | | float | double | - | | double | double | - | | decimal | string | 現在、decimal は Proton でサポートされていません。 | | bool | boolean | - | | Guid | UUID | Proton-PHP クラス | | string | string | - | | DateTime | integer | - | | DateTimeOffset | DescribedType | AMQP 型にマップされる DateTimeOffset.UtcTicks:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | AMQP 型にマップされる Timespan.Ticks:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | AMQP 型にマップされる Uri.AbsoluteUri:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### 標準のプロパティ

次の表は、Proton-PHP の標準のメッセージ プロパティと [BrokeredMessage][] の標準のメッセージ プロパティの対応を示します。

| Proton-PHP | Service Bus .NET | メモ |
|----------------------|--------------------------|----------------------------------------------------------|
| Durable | 該当なし | Service Bus は、持続的なメッセージのみをサポートします。 |
| 優先順位 | 該当なし | Service Bus は、単一のメッセージの優先度のみをサポートします。 |
| Ttl | Message.TimeToLive | 変換、Proton-PHP TTL はミリ秒単位で定義されます。 |
| first\_acquirer | - | - | | delivery\_count | - | - | | Id | Message.Id | - | | user\_id | - | - | | Address | Message.To | - | | Subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationId | - | | content\_type | Message.ContentType | - | | content\_encoding | 該当なし | - | | expiry\_time | Message.ExpiresAtUTC | - | | creation\_time | 該当なし | - | | group\_id | Message.SessionId | - | | group\_sequence | - | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | Format | 該当なし | -

#### Service Bus .NET API から Proton-PHP へ

| Service Bus .NET | Proton-PHP | メモ |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType | Message->content\_type | - | | CorrelationId | Message->correlation\_id | - | | EnqueuedTimeUtc | Message->annotations[x-opt-enqueued-time] | - | | Label | Message->subject | - | | MessageId | Message->id | - | | ReplyTo | Message->reply\_to | - | | ReplyToSessionId | Message->reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Message->annotations ["x-opt-scheduled-enqueue-time"] | - | | SessionId | Message->group\_id | - | | TimeToLive | Message->ttl | 変換、Proton-PHP TTL はミリ秒単位で定義されます。| | To | Message->address | - |

## 次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

- [Service Bus AMQP の概要]
- [AMQP で .NET から Service Bus を使用する]
- [AMQP で Java から Service Bus を使用する]
- [AMQP で Python から Service Bus を使用する]
- [Azure Linux VM に Apache Qpid Proton-C をインストールする]
- [Windows Server 用 Service Bus の AMQP]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Windows Server 用 Service Bus の AMQP]: https://msdn.microsoft.com/library/dn574799.aspx

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[AMQP で .NET から Service Bus を使用する]: service-bus-amqp-dotnet.md
[AMQP で Java から Service Bus を使用する]: service-bus-amqp-java.md
[AMQP で Python から Service Bus を使用する]: service-bus-amqp-python.md
[Azure Linux VM に Apache Qpid Proton-C をインストールする]: service-bus-amqp-apache.md

<!---HONumber=Oct15_HO2-->