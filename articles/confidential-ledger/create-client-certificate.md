---
title: Microsoft Azure Confidential Ledger でクライアント証明書を作成する
description: Microsoft Azure Confidential Ledger でクライアント証明書を作成する
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 6d384ea07c49a8b7936e3cdf634b9775847018f0
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386653"
---
# <a name="creating-a-client-certificate"></a>クライアント証明書を作成する

Confidential Ledger API ではクライアント証明書による認証が必要です。 Confidential Ledger Functional API の呼び出しに使用できるのは、台帳の作成時または更新時に許可リストに追加した証明書だけです。

証明書は PEM 形式である必要があります。 Ledger Update API で複数の証明書の作成や証明書の追加、削除ができます。

## <a name="openssl"></a>OpenSSL

証明書は OpwnSSL を使用して生成することをお勧めします。 git をインストールしている場合は、git シェルで OpenSSL を実行できます。 その他、使用している OS に OpenSSL をインストールすることもできます。

- **Windows**: [Chocolatey for Windows](https://chocolatey.org/install) をインストールし、PowerShell のターミナルを管理者モードで開き、`choco install openssl` を実行します。 その他、OpenSSL for Windows を[ここ](http://gnuwin32.sourceforge.net/packages/openssl.htm)から直接インストールすることもできます。
- **Linux**: `sudo apt-get install openssl` を実行します

次に、Bash または PowerShell のターミナルで `openssl` を実行して証明書を生成します。

```bash
openssl req -new -newkey rsa:1024 -days 365 -nodes -x509 -keyout user_privk.pem -out user_cert.pem -subj=/CN="User Client Certificate"
```

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)