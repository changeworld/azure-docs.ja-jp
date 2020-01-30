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
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156490"
---
# <a name="configure-openssl-for-linux"></a>Linux 用 OpenSSL の構成

1\.9.0 より前の Speech SDK バージョンを使用している場合、[OpenSSL](https://www.openssl.org) はホストシステム バージョンに動的に構成されます。 新しいバージョンの Speech SDK では、OpenSSL (バージョン [1.1.1b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) は Speech SDK のコア ライブラリに静的にリンクされています。

## <a name="troubleshoot-connectivity"></a>接続のトラブルシューティング

Speech SDK の 1.9.0 リリースを使用しているときに接続エラーが発生する場合は、`ssl/certs` ディレクトリが確実に Linux ファイル システム内の `/usr/lib` ディレクトリに存在するようにします。 `ssl/certs` ディレクトリが "*存在しない*" 場合は、次のコマンドを使用して、OpenSSL がシステムのどこにインストールされているかを確認します。

```bash
which openssl
```

次に、OpenSSL の `certs` ディレクトリを探し、そのディレクトリの内容を `/usr/lib/ssl/certs` ディレクトリにコピーします。 次に、接続の問題が解決されたかどうかをもう一度確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK について](speech-sdk.md)