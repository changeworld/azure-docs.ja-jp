---
title: Shared Access Signature の概要 | Microsoft Docs
description: Shared Access Signature とは何か、その動作方法、およびノード、PHP、および C# での使用方法。
services: service-bus,event-hubs
documentationcenter: na
author: djrosanova
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2016
ms.author: darosa;sethm

---
# Shared Access Signature
*Shared Access Signature* (SAS) は、Event Hubs、ブローカー メッセージング (キューとトピック) およびリレー型メッセージングを含む、Service Bus の主要なセキュリティ メカニズムです。この記事では、Shared Access Signature、そのしくみ、およびプラットフォームに依存しない方法で共有アクセス署名を使用する方法について説明します。

## SAS の概要
Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。SAS は、すべての Service Bus サービスによって使用される非常に強力なメカニズムです。実際の使用では、SAS には*共有アクセス ポリシー*と*Shared Access Signature* (多くの場合*トークン*と呼ばれます) という 2 つのコンポーネントがあります。

Service Bus での共有アクセス署名に関する詳細については、「[Service Bus での共有アクセス署名認証](service-bus-shared-access-signature-authentication.md)」を参照してください。

## 共有アクセス ポリシー
SAS を理解するうえで重要な点の 1 つは、SAS の基盤はポリシーであるということです。すべてのポリシーについて、**名前**、**スコープ**、および**権限**という 3 種類の情報を決定します。**名前**とは、文字どおり名前を表します。そのスコープ内で一意の名前です。スコープは簡単です。対象となるリソースの URI です。Service Bus 名前空間の場合、スコープは、**`https://<yournamespace>.servicebus.windows.net/`** のような完全修飾ドメイン名 (FQDN) です。

ポリシーに使用可能な権限は、いずれも名前から意味がわかりやすくなっています。

* 送信
* リッスン
* 管理

ポリシーを作成した後、そのポリシーには*プライマリ キー*と*セカンダリ キー*が割り当てられます。これらは、暗号化された強力なキーです。これらをなくしたり、外部に漏らしたりしないでください。これらは、常に [Azure クラシック ポータル][Azure クラシック ポータル]から入手可能です。生成されたキーのいずれかを使用できます。また、いつでも再生成できます。ただし、再生成するか、またはポリシーのプライマリ キーを変更する場合は、ポリシーから作成された Shared Access Signature がすべて無効になります。

Service Bus の名前空間を作成するときに、**RootManageSharedAccessKey** という名前空間全体に対してポリシーが自動的に作成され、そのポリシーにはすべての権限が与えられます。**root** としてログオンしないため、本当に正当な理由がない限り、このポリシーを使用しないでください。ポータルの名前空間の **[構成]** タブで、追加のポリシーを作成できます。重要なのは、Service Bus の 1 つのツリーのレベル (名前空間、キュー、イベント ハブなど) には、最大 12 個のポリシーだけを保持できることです。

## Shared Access Signature (トークン)
ポリシー自体は、Service Bus のアクセス トークンではありません。これは、アクセス トークンの生成元のオブジェクトで、プライマリ キーまたはセカンダリ キーを使用します。次の形式の文字列を注意深く作成することで、トークンが生成されます。

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

ここで `signature-string` は、CRLF を追加し、有効期限 (エポックとなる 1970 年 1 月 1 日の `00:00:00 UTC` からのミリ秒) を設定したトークンのスコープ (前のセクションで説明した**スコープ**) の SHA-256 ハッシュです。

ハッシュは、次の擬似コードに似ていて、32 バイトを返します。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非ハッシュ値は、**SharedAccessSignature** 文字列にあり、受信者は同じ結果が返されることを確認するために、同じパラメーターを使用してハッシュを計算できます。URI はスコープを指定し、キー名はハッシュを計算するために使用するポリシーを識別します。これは、セキュリティの観点からは重要です。署名が、受信者 (Service Bus) が計算した署名と一致しない場合は、アクセスは拒否されます。この時点で、送信者にはキーへのアクセス権があり、この送信者にポリシーで指定した権限を付与する必要があることが確認できます。

## ポリシーからの署名の生成
実際に、どのようにコードでこの操作を実現するのでしょうか。 いくつかの方法を説明します。

### NodeJS
```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java
```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP
```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();     
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .         "&skn=" . $sasKeyName; 
return $token; 
}
```

### C (&) #35；
```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Shared Access Signature の使用 (HTTP レベル)
Service Bus ですべてのエンティティの Shared Access Signature を作成する方法を理解したので、HTTP POST を実行する準備ができました。

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

この方法は、すべての機能に対して利用できます。キュー、トピック、サブスクリプション、イベント ハブまたはリレーに対して SAS を作成できます。Event Hubs の発行元ごとの ID を使用する場合は、`/publishers/< publisherid>` を追加するだけです。

送信者またはクライアントに SAS トークンを付与する場合は、送信者またはクライアントはキーを直接保持しないため、キーを取得するハッシュを反転できません。そのため、管理者は、送信者またはクライアントがアクセスできる対象とアクセスする期間を制御できます。重要なのは、ポリシーのプライマリ キーを変更する場合は、ポリシーから作成された Shared Access Signature がすべて無効になるということです。

## Shared Access Signature の使用 (AMQP レベル)
前のセクションでは、データを Service Bus に送信するために、HTTP POST 要求を使用して SAS トークンを使用する方法について説明しました。ご存じのように、Service Bus へのアクセスには、AMQP (Advanced Message Queuing Protocol) を使用できます。AMQP は、多くのシナリオでパフォーマンス上の理由から好まれているプロトコルです。SAS トークンと AMQP の併用については、[AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) のドキュメントを参照してください。このドキュメントは、2013 年から執筆されているドラフトですが、現在 Azure でもサポートされています。

Service Bus へのデータ送信を開始する前に、発行元から適切に定義された **"$cbs"** という AMQP ノードに対して、AMQP メッセージ内で SAS トークンを送信する必要があります (すべての SAS トークンを取得して検証するためにサービスで使用される "特別な" キューと考えることができます)。発行元は、AMQP メッセージ内で **"ReplyTo"** フィールドを指定する必要があります。これは、サービスから発行元に対してトークンの検証結果を返信するノードです (発行元とサービス間の簡単な要求/応答のパターン)。この応答ノードは、AMQP 1.0 仕様に記載されているように、"リモート ノードの動的作成" について話すことで "その場で" 作成されます。発行元は SAS トークンが有効であることを確認した後に、次の処理に進み、サービスに対してデータを送信できるようになります。

次の手順は、[AMQP.Net Lite](https://github.com/Azure/amqpnetlite) ライブラリを使用して、AMQP プロトコルで SAS トークンを送信する方法を示しています。この方法は、C&#35; で開発する公式の Service Bus SDK (WinRT、.Net Compact Framework、.Net Micro Framework、Mono など) を使用できない場合に有効です。当然ながら、HTTP レベル ("Authorization" ヘッダー内で送信される HTTP POST 要求と SAS トークン) の場合と同様に、要求ベースのセキュリティが AMQP レベルでどのように機能するかを理解するためにもこのライブラリは役立ちます。AMQP についてこのような詳しい知識が不要な場合は、公式の Service Bus SDK と .NET Framework アプリケーションを利用できます。これがその役割を果たします。

### C (&) #35；
```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()` メソッドは、サービスに対する TCP 接続を表す *connection* ([AMQP .NET Lite ライブラリ](https://github.com/Azure/amqpnetlite)に用意されている AMQP Connection クラス インスタンス) と、送信する SAS トークンである *sasToken* パラメーターを受け取ります。

> [!NOTE]
> \(SAS トークンを送信する必要がないときに使用されるユーザー名とパスワードを含む既定の PLAIN ではなく) **EXTERNAL に設定された SASL 認証メカニズム**を使用して、接続が作成されている点に注意してください。
> 
> 

次に、発行元は、SAS トークンの送信とサービスからの応答 (トークンの検証結果) の受信に使用される 2 つの AMQP リンクを作成します。

AMQP メッセージには一連のプロパティと、簡単なメッセージより多くの情報が含まれています。SAS トークンはメッセージの本文です (コンストラクターを使用)。**"ReplyTo"** プロパティは、受信側リンクで検証結果を受信するノード名に設定されます (必要に応じて名前を変更できます。名前はサービスで自動的に作成されます)。最後の 3 つの application/custom プロパティは、実行する必要がある操作の種類を示すためにサービスで使用されます。CBS ドラフト仕様に記載されているように、**操作名** ("put-token")、**トークンの種類** (この例では、"servicebus.windows.net:sastoken")、およびトークンを適用する**オーディエンスの "名前"** (エンティティ全体) を設定する必要があります。

発行元は、送信側リンクで SAS トークンを送信した後に、受信側リンクの応答を読み取る必要があります。応答は、**"status-code"** というアプリケーション プロパティを含む簡単な AMQP メッセージです。このプロパティには、HTTP 状態コードと同じ値を含めることができます。

## 次のステップ
これらの SAS トークンで実行できる処理の詳細については、[Service Bus REST API リファレンス](https://msdn.microsoft.com/library/azure/hh780717.aspx)を参照してください。

Service Bus 認証の詳細については、「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照してください。

[こちらのブログ記事](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)では、C# および JavaScript での SAS に関するさまざまな例を紹介しています。

[Azure クラシック ポータル]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0622_2016-->