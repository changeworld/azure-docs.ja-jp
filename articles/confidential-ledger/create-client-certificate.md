---
title: Microsoft Azure Confidential Ledger でクライアント証明書を作成する
description: Microsoft Azure Confidential Ledger でクライアント証明書を作成する
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 125f56a6c96e37394e4e9e2565093c847e592108
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585935"
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
openssl ecparam -out "privkey_name.pem" -name "secp384r1" -genkey
openssl req -new -key "privkey_name.pem" -x509 -nodes -days 365 -out "cert.pem" -"sha384" -subj=/CN="ACL Client Cert"
```

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)