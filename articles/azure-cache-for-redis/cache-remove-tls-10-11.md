---
title: Azure Cache for Redis での使用から TLS 1.0 と 1.1 を削除する
description: Azure Cache for Redis と通信するときにアプリケーションから TLS 1.0 と 1.1 を削除する方法について説明します
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812844"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Azure Cache for Redis での使用から TLS 1.0 と 1.1 を削除する

業界全体で、トランスポート層セキュリティ (TLS) バージョン 1.2 以降の排他的使用が推進されています。 TLS バージョン 1.0 と 1.1 は、BEAST や POODLE などの攻撃を受けやすく、またその他の共通脆弱性識別子 (CVE) の弱点を持つことが知られています。 また、ペイメント カード業界 (PCI) コンプライアンスの標準で推奨されている最新の暗号化方式と暗号スイートもサポートしていません。 こちらの [TLS セキュリティ ブログ](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)では、これらの脆弱性の一部について詳しく説明しています。

これらの考慮事項のいずれもすぐに問題を引き起こすわけではありませんが、Microsoft では TLS 1.0 と 1.1 の使用をただちに停止することをお勧めしています。 Azure Cache for Redis では、2020 年 3 月 31 日にこれらの TLS のバージョンのサポートが停止されます。 その日以降、お使いのアプリケーションでは TLS 1.2 以降を使用してキャッシュと通信する必要があります。

この記事では、これらの以前の TLS バージョンへの依存関係を検出してアプリケーションから削除する方法に関する一般的なガイダンスを提供します。

## <a name="check-whether-your-application-is-already-compliant"></a>アプリケーションが既に準拠しているかどうかを確認する

アプリケーションが TLS 1.2 で動作するかどうかを確認する最も簡単な方法は、 **[TLS の最小バージョン]** の値を、使用しているテスト用またはステージング キャッシュで TLS 1.2 に設定することです。 **[TLS の最小バージョン]** 設定は、Azure portal 内のキャッシュ インスタンスの [[詳細設定]](cache-configure.md#advanced-settings) にあります。 この変更後もアプリケーションが予想どおりに動作し続ける場合は、おそらく準拠しています。 アプリケーションで使用されるいくつかの具体的な Redis クライアント ライブラリを、TLS 1.2 を有効にするように構成しなければならない場合があります。これにより、そのセキュリティ プロトコルで Azure Cache for Redis に接続できます。

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2 を使用するようにアプリケーションを構成する

ほとんどのアプリケーションは、Redis クライアント ライブラリを使用して、キャッシュとの通信を処理します。 ここでは、さまざまなプログラミング言語およびフレームワークで、いくつかの一般的なクライアント ライブラリを TLS 1.2 を使用するように構成する手順について説明します。

### <a name="net-framework"></a>.NET Framework

Redis .NET クライアントは、.NET Framework 4.5.2 以前では既定で以前の TLS バージョンを使用し、.NET Framework 4.6 以降では最新の TLS バージョンを使用します。 古いバージョンの .NET Framework を使用している場合は、手動で TLS 1.2 を有効にできます。

* **StackExchange.Redis:** 接続文字列に `ssl=true` と `sslprotocols=tls12` を設定します。
* **ServiceStack.Redis:** [ServiceStack.Redis の手順](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)に従ってください。

### <a name="net-core"></a>.NET Core

Redis .NET Core クライアントは、既定で最新の TLS バージョンを使用します。

### <a name="java"></a>Java

Redis Java クライアントは、Java バージョン 6 以前では TLS 1.0 を使用します。 TLS 1.0 がキャッシュで無効になっている場合、Jedis、Lettuce、Radisson は Azure Cache for Redis に接続できません。 現在、既知の回避策はありません。

Java 7 以降では、Redis クライアントは既定では TLS 1.2 を使用しませんが、使用するように構成できます。 Lettuce と Radisson は、現時点ではこの構成をサポートしていません。 キャッシュが TLS 1.2 接続のみを受け入れる場合は、中断されます。 Jedis では、次のコード スニペットを使用して、基になる TLS 設定を指定できます。

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Node Redis と IORedis は、既定で TLS 1.2 を使用します。

### <a name="php"></a>PHP

PHP 7 では TLS 1.0 のみがサポートされるため、PHP 7 の Predis は動作しません。 PHP 7.2.1 以前では、Predis は既定で TLS 1.0 または 1.1 を使用します。 クライアント インスタンスを作成するときに、TLS 1.2 を指定できます。

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

PHP 7.3 以降では、Predis は最新の TLS バージョンを使用します。

PhpRedis は、どの PHP バージョンでも TLS をサポートしていません。

### <a name="python"></a>Python

Redis-py は、既定で TLS 1.2 を使用します。

### <a name="go"></a>GO

Redigo は、既定で TLS 1.2 を使用します。

## <a name="additional-information"></a>追加情報

- [Azure Cache for Redis の構成方法](cache-configure.md)
