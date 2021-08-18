---
title: Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する
description: Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 89ba0139409a1ceef37a773403c0b7623f6c6f14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738599"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する

データ コントローラーのユーザー名とパスワードを取得する必要がある場合があります。 これらは実行するときに必要なコマンドです。 

クラスターの Kubernetes 管理者である場合。 そのため、Kubernetes シークレット ストアから、Azure Arc がそこで保持している情報を取得するコマンドを実行する特権を持ちます。

> [!NOTE]
>  データ コントローラーが作成される名前空間に別の名前を使用している場合は、次のコマンドの `-n arc` パラメーターを、データ コントローラーの作成先の名前空間の名前を使用するように変更します。


## <a name="linux"></a>Linux

次のコマンドを実行して、ユーザー名を取得します。

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

次のコマンドを実行して、パスワードを取得します。

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

次のコマンドを実行して、ユーザー名を取得します。

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

次のコマンドを実行して、パスワードを取得します。

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>次のステップ

その他の[シナリオ](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)を試してみる
