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
ms.openlocfilehash: ff8772f7c3c3213c010b0bdbd0d0aa8897404bac
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119985"
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
SSL_CERT_DIR=/opt/ssl/certs ./helloworld
```

- OPENSSLDIR は `/etc/pki/tls` です。 証明書バンドル ファイル (`ca-bundle.pem`、`ca-bundle.crt` など) が存在します。
Speech SDK を使用するプログラムを実行する前に、環境変数 `SSL_CERT_FILE` の指し示す場所を `/etc/pki/tls/ca-bundle.pem` に設定します。 次に例を示します。
```bash
SSL_CERT_FILE=/etc/pki/tls/ca-bundle.pem ./helloworld
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK について](speech-sdk.md)
