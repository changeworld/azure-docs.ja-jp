---
title: Azure Cache for Redis での使用から TLS 1.0 と 1.1 を削除する
description: Azure Cache for Redis と通信するときにアプリケーションから TLS 1.0 と 1.1 を削除する方法について説明します
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cauribeg
ms.openlocfilehash: 8af62a70d7c33a146c6f749be057f41a500ada58
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537611"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Azure Cache for Redis での使用から TLS 1.0 と 1.1 を削除する

業界全体で、トランスポート層セキュリティ (TLS) バージョン 1.2 以降の排他的使用が推進されています。 TLS バージョン 1.0 と 1.1 は、BEAST や POODLE などの攻撃を受けやすく、またその他の共通脆弱性識別子 (CVE) の弱点を持つことが知られています。 また、ペイメント カード業界 (PCI) コンプライアンスの標準で推奨されている最新の暗号化方式と暗号スイートもサポートしていません。 こちらの [TLS セキュリティ ブログ](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)では、これらの脆弱性の一部について詳しく説明しています。

この作業の一環として、Azure Cache for Redis に対して次の変更が行われます。

* **フェーズ 1:** 新しく作成されるキャッシュ インスタンスには、既定の最小 TLS バージョンとして 1.2 が構成されます (以前は TLS 1.0 でした)。 この時点で既定のキャッシュ インスタンスが更新されることはありません。 後方互換性のために、Azure portal などの管理 API を使用して、[TLS のバージョン要件を](cache-configure.md#access-ports) 1.0 または 1.1 以上に変更できます。
* **フェーズ 2:** TLS 1.1 と TLS 1.0 のサポートは停止されます。 この変更以降、お使いのアプリケーションでは、TLS 1.2 以降を使用してキャッシュと通信する必要があります。 Azure Cache for Redis サービスは、TLS 1.2 以降のみをサポートするための移行中に、使用できると予想されます。

  > [!NOTE]
  > フェーズ 2 は COVID-19 のために延期されています。 今すぐこの変更の計画を開始し、TLS 1.2 以降をサポートするように事前にクライアントを更新することを強くお勧めします。 
  >

この変更の一環として、安全でない古い暗号スイートのサポートは、今後廃止します。 キャッシュで TLS のバージョン要件を 1.2 以上に設定する場合、サポートされる暗号スイートは次のものに限られます。

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

この記事では、これらの以前の TLS バージョンへの依存関係を検出してアプリケーションから削除する方法に関する一般的なガイダンスを提供します。

これらの変更が有効になる日付は次のとおりです。

| クラウド                | フェーズ 1 の開始日 | フェーズ 2 の開始日         |
|----------------------|--------------------|----------------------------|
| Azure (グローバル)       |  2020 年 1 月 13 日  | COVID-19 の影響により延期  |
| Azure Government     |  2020 年 3 月 13 日    | COVID-19 の影響により延期  |
| Azure Germany        |  2020 年 3 月 13 日    | COVID-19 の影響により延期  |
| Azure China 21Vianet |  2020 年 3 月 13 日    | COVID-19 の影響により延期  |

> [!NOTE]
> フェーズ 2 は COVID-19 のために延期されています。 この記事は、具体的な日付が設定されたときに更新されます。
>

## <a name="check-whether-your-application-is-already-compliant"></a>アプリケーションが既に準拠しているかどうかを確認する

アプリケーションが TLS 1.2 で機能するかどうかを確認するには、テストまたはステージング キャッシュで **TLS のバージョン要件** を 1.2 以上に設定して、テストを実行します。 **[TLS の最小バージョン]** 設定は、Azure portal 内のキャッシュ インスタンスの [[詳細設定]](cache-configure.md#advanced-settings) にあります。  この変更後もアプリケーションが予想どおりに動作し続ける場合は、おそらく準拠しています。 場合によっては、Azure Cache for Redis に接続するために、アプリケーションで使用する Redis クライアント ライブラリで、TLS 1.2 を有効にするよう設定する必要があります。

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2 を使用するようにアプリケーションを構成する

ほとんどのアプリケーションは、Redis クライアント ライブラリを使用して、キャッシュとの通信を処理します。 ここでは、さまざまなプログラミング言語およびフレームワークで、いくつかの一般的なクライアント ライブラリを TLS 1.2 を使用するように構成する手順について説明します。

### <a name="net-framework"></a>.NET Framework

Redis .NET クライアントは、.NET Framework 4.5.2 以前では既定で以前の TLS バージョンを使用し、.NET Framework 4.6 以降では最新の TLS バージョンを使用します。 古いバージョンの .NET Framework を使用している場合は、TLS 1.2 を手動で有効にします。

* **StackExchange.Redis:** 接続文字列に `ssl=true` と `sslprotocols=tls12` を設定します。
* **ServiceStack.Redis:** [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) の手順に従い、ServiceStack.Redis v5.6 以降が必要です。

### <a name="net-core"></a>.NET Core

既定では、Redis .NET Core クライアントで、OS 既定の TLS バージョンを使用します。これは OS ごとに異なります。 

OS のバージョンと適用したパッチにより、既定で有効になる TLS のバージョンは異なります。 詳細については、[このページ](/dotnet/framework/network-programming/#support-for-tls-12)を参照してください。

ただし、古い OS を使用している場合またはバージョンを確実に指定したい場合は、クライアントから、希望の TLS バージョンを手動で設定することをお勧めします。


### <a name="java"></a>Java

Redis Java クライアントは、Java バージョン 6 以前では TLS 1.0 を使用します。 TLS 1.0 がキャッシュで無効になっている場合、Jedis、Lettuce、Redisson は Azure Cache for Redis に接続できません。 新しい TLS バージョンを使用するように Java フレームワークをアップグレードします。

Java 7 の場合、Redis クライアントは既定では TLS 1.2 を使用しませんが、使用するように構成できます。 Jedis では、次のコード スニペットを使用して、基になる TLS 設定を指定できます。

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Lettuce と Redisson のクライアントには、TLS バージョンを指定する機能がまだありません。 キャッシュが TLS 1.2 接続のみを受け入れる場合は、中断されます。 これらのクライアントの修正プログラムはレビュー中であるため、これらのパッケージによって、このサポートに更新されたバージョンがないかどうか確認してください。

Java 8 では、既定で TLS 1.2 が使用され、ほとんどの場合、クライアント構成に更新プログラムは必要ありません。 念のため、アプリケーションをテストしてください。

### <a name="nodejs"></a>Node.js

Node Redis と IORedis は、既定で TLS 1.2 を使用します。

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7 より前のバージョン:Predis では TLS 1.0 のみがサポートされます。 これらのバージョンでは TLS 1.2 がサポートされていません。TLS 1.2 を使用するには、アップグレードする必要があります。
 
* Php 7.0 から PHP 7.2.1:Predis は、規定では TLS 1.0 または1.1 のみを使用します。 TLS 1.2 を使用するには、次の回避策を使用できます。 クライアント インスタンスを作成するときに、TLS 1.2 を指定します。

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

* PHP 7.3 および以降のバージョン:Predis は最新の TLS バージョンを使用します。

#### <a name="phpredis"></a>PhpRedis

PhpRedis は、どの PHP バージョンでも TLS をサポートしていません。

### <a name="python"></a>Python

Redis-py は、既定で TLS 1.2 を使用します。

### <a name="go"></a>GO

Redigo は、既定で TLS 1.2 を使用します。

## <a name="additional-information"></a>関連情報

- [Azure Cache for Redis の構成方法](cache-configure.md)