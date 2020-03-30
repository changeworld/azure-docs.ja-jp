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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331146"
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK について](speech-sdk.md)
