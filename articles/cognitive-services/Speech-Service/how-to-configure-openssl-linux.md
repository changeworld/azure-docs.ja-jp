---
title: Linux の OpenSSL を構成する方法
titleSuffix: Azure Cognitive Services
description: Linux の OpenSSL を構成する方法について説明します。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577446"
---
# <a name="configure-openssl-for-linux"></a>Linux 用 OpenSSL の構成

1\.9.0 より前の Speech SDK バージョンを使用している場合、[OpenSSL](https://www.openssl.org) はホストシステム バージョンに動的に構成されます。 新しいバージョンの Speech SDK では、OpenSSL (バージョン [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) は Speech SDK のコア ライブラリに静的にリンクされています。

確実に接続できるよう、OpenSSL 証明書がシステムにインストールされていることを確認します。 次のコマンドを実行します。
```bash
openssl version -d
```

Ubuntu ベースまたは Debian ベースのシステムでは、次のような出力が返されます。
```
OPENSSLDIR: "/usr/lib/ssl"
```

OPENSSLDIR に `certs` サブディレクトリが存在するかどうかを確認します。 上記の例では `/usr/lib/ssl/certs` です。

* `/usr/lib/ssl/certs` が存在していて、そこに個別の証明書ファイル (拡張子は `.crt` または `.pem`) が多数格納されている場合、以降の操作は不要です。

* OPENSSLDIR が `/usr/lib/ssl` 以外であったり、(複数のファイルが個別に存在するのではなく) 証明書のバンドル ファイルが 1 つあるだけであったりした場合、証明書の場所を示す適切な SSL 環境変数を設定する必要があります。

## <a name="examples"></a>例

- OPENSSLDIR は `/opt/ssl` です。 `certs` サブディレクトリが存在し、そこに `.crt` ファイルまたは `.pem` ファイルが多数格納されています。
Speech SDK を使用するプログラムを実行する前に、環境変数 `SSL_CERT_DIR` の指し示す場所を `/opt/ssl/certs` に設定します。 次に例を示します。
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR は `/etc/pki/tls` (RHEL/CentOS ベースのシステムの場合と同様) です。 `certs` 証明書バンドル ファイルが付属するサブディレクトリ (`ca-bundle.crt` など) が存在します。
Speech SDK を使用するプログラムを実行する前に、環境変数 `SSL_CERT_FILE` の指し示す場所をそのファイルに設定します。 次に例を示します。
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>証明書失効の確認
Speech SDK は、Speech Service に接続するときに、Speech Service で使用される TLS 証明書が失効していないことを確認します。 この確認を行うため、Speech SDK は Azure で使用される証明機関の CRL 配布ポイントにアクセスする必要があります。 可能性がある CRL のダウンロード場所の一覧については、[このドキュメント](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)を参照してください。 証明書が失効しているか、CRL をダウンロードできない場合、Speech SDK は接続を中止し、Canceled イベントを発生させます。

Speech SDK が使用されているネットワークが CRL のダウンロード場所へのアクセスを許可しない方法で構成されている場合は、CRL チェックを無効にするか、CRL を取得できない場合は失敗ではないように設定できます。 この構成は、認識エンジン オブジェクトの作成に使用される構成オブジェクトを介して行われます。

CRL を取得できないときに接続を続行するには、プロパティ OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE を設定します。

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
"true" に設定すると、CRL の取得が試行され、取得が成功した場合、証明書の失効が確認されます。取得に失敗した場合、接続の続行が許可されます。

証明書の失効確認を完全に無効にするには、プロパティ OPENSSL_DISABLE_CRL_CHECK を "true" に設定します。
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> また、Linux の一部のディストリビューションでは、TMP または TMPDIR 環境変数が定義されていないことにも注意してください。 これにより、Speech SDK は、有効期限が切れるまで証明書失効リスト (CRL) を再利用できるようにディスクにキャッシュするのではなく、毎回 CRL をダウンロードします。 初回接続のパフォーマンスを向上させるには、[TMPDIR という名前の環境変数を作成し、選択した一時ディレクトリのパスに設定します](https://help.ubuntu.com/community/EnvironmentVariables)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK について](speech-sdk.md)
