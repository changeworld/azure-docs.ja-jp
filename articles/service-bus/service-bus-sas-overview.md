<properties
   pageTitle="共有アクセス署名の概要"
   description="共有アクセス署名とは何か、その動作方法、およびノード、PHP、および C# での使用方法。"
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# Shared Access Signature

*共有アクセス署名* (SAS) は、イベント ハブ、仲介型メッセージング (キューとトピック) およびリレー型メッセージングを含む、Service Bus の主要なセキュリティ メカニズムです。この記事では、共有アクセス署名、そのしくみ、およびプラットフォームに依存しない方法で共有アクセス署名を使用する方法について説明します。

## SAS の概要

共有アクセス署名は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。SAS は、すべての Service Bus サービスによって使用される非常に強力なメカニズムです。実際の使用では、SAS には*共有アクセス ポリシー*と*共有アクセス署名* (多くの場合*トークン*と呼ばれます) という 2 つのコンポーネントがあります。

Service Bus での共有アクセス署名に関する詳細については、「[Service Bus での共有アクセス署名認証](https://msdn.microsoft.com/library/azure/dn170477.aspx)」を参照してください。

## 共有アクセス ポリシー

SAS を理解するうえで重要な点の 1 つは、SAS の基盤はポリシーであるということです。すべてのポリシーについて、**名前**、**スコープ**、および**権限**という 3 種類の情報を決定します。**名前**とは、文字どおり名前を表します。そのスコープ内で一意の名前です。スコープは簡単です。対象となるリソースの URI です。Service Bus 名前空間の場合、スコープは、**`https://<yournamespace>.servicebus.windows.net/`** のような完全修飾ドメイン名 (FQDN) です。

ポリシーに使用可能な権限は、いずれも名前から意味がわかりやすくなっています。

  + 送信
  + リッスン
  + 管理

ポリシーを作成した後、そのポリシーには*プライマリ キー*と*セカンダリ キー*が割り当てられます。これらは、暗号化された強力なキーです。これらをなくしたり、外部に漏らしたりしないでください。これらは、常にポータルから入手可能です。生成されたキーのいずれかを使用できます。また、いつでも再生成できます。ただし、再生成するか、またはポリシーのプライマリ キーを変更する場合は、ポリシーから作成された共有アクセス署名がすべて無効になります。

Service Bus の名前空間を作成するときに、**RootManageSharedAccessKey** という名前空間全体に対してポリシーが自動的に作成され、そのポリシーにはすべての権限が与えられます。**root** としてログオンしないため、本当に正当な理由がない限り、このポリシーを使用しないでください。Azure 管理ポータルの名前空間の **[構成]** タブで、追加のポリシーを作成できます。重要なのは、Service Bus の 1 つのツリーのレベル (名前空間、キュー、イベント ハブなど) には、最大 12 個のポリシーだけを保持できることです。

## 共有アクセス署名 (トークン)

ポリシー自体は、Service Bus のアクセス トークンではありません。これは、アクセス トークンの生成元のオブジェクトで、プライマリ キーまたはセカンダリ キーを使用します。次の形式の文字列を注意深く作成することで、トークンが生成されます。

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

ここで `signature-string` は、CRLF を追加し、有効期限 (エポックとなる 1970 年 1 月 1 日の `00:00:00 UTC` からのミリ秒) を設定したトークンのスコープ (前のセクションで説明した\*\*スコープ\*\*) の SHA-256 ハッシュです。

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

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
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

## 共有アクセス署名の使用
 
Service Bus ですべてのエンティティの共有アクセス署名を作成する方法を理解したので、HTTP POST を実行する準備ができました。

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
この方法は、すべての機能に対して利用できます。キュー、トピック、サブスクリプション、イベント ハブまたはリレーに対して SAS を作成できます。イベント ハブの発行元ごとの ID を使用する場合は、`/publishers/< publisherid>` を追加するだけです。

送信者またはクライアントに SAS トークンを付与する場合は、送信者またはクライアントはキーを直接保持しないため、キーを取得するハッシュを反転できません。そのため、管理者は、送信者またはクライアントがアクセスできる対象とアクセスする期間を制御できます。重要なのは、ポリシーのプライマリ キーを変更する場合は、ポリシーから作成された共有アクセス署名がすべて無効になるということです。

## 次のステップ

これらの SAS トークンを使用してできる操作の詳細については、「[Service BUS REST API リファレンス](https://msdn.microsoft.com/library/azure/hh780717.aspx)」を参照してください。

SAS の詳細については、MSDN の「[Service Bus 認証](https://msdn.microsoft.com/library/azure/dn155925.aspx)」ノードを参照してください。

<!---HONumber=July15_HO5-->