---
title: ポイントインタイムからキーと値のペアを取得する
titleSuffix: Azure App Configuration
description: Azure App Configuration でポイントインタイム スナップショットを使用して古いキーと値のペアを取得します
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523661"
---
# <a name="point-in-time-snapshot"></a>ポイントインタイム スナップショット

Azure App Configuration では、キーと値のペアに対する変更のレコードが保持されます。 このレコードからは、キーと値の変更のタイムラインが得られます。 キーと値の履歴を再構築し、過去 7 日間以内のある時点の古い値を取得できます。 この機能を使用すると、"過去にさかのぼって" 以前のキーと値を取得することができます。 たとえば、アプリケーションを以前の構成にロールバックするために、直近のデプロイの前に使用されていた構成設定を回復することができます。

## <a name="key-value-retrieval"></a>キー/値の取得

過去のキーと値の取得には Azure PowerShell を使用できます。  必要な値を取得するために適切なパラメーターを追加して `az appconfig revision list` を使用します。  ストア名 (`--name {app-config-store-name}`) を指定するか、接続文字列 (`--connection-string {your-connection-string}`) を使用するかして、Azure App Configuration インスタンスを指定します。 特定のポイントインタイム (`--datetime`) を指定し、返される項目の最大数 (`--top`) を指定して、出力を制限します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

キーと値に対する変更のレコードをすべて取得します。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

キーが `environment` でラベルが `test` と `prod` の変更のレコードをすべて取得します。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

階層的キー スペース `environment:prod` 内の変更のレコードをすべて取得します。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

特定のポイントインタイムのキー `color` に対する変更のレコードをすべて取得します。

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

自分のキーと値に対する最近 10 件の変更のレコードを取得し、`key`、`label`、`last-modified` タイム スタンプの値だけを返します。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリケーションの作成](./quickstart-aspnet-core-app.md)  
