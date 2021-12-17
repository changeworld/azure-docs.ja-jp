---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: d53bfaab5e00b95be52f652b72e9d55a76c396a6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721412"
---
次の PowerShell コマンドを使用すると、ストレージ アカウントのパブリック エンドポイントに対するすべてのトラフィックが拒否されます。 このコマンドでは、`-Bypass` パラメーターが `AzureServices` に設定されていることに注意してください。 これにより、Azure File Sync などの信頼できるファーストパーティのサービスが、パブリック エンドポイント経由でストレージ アカウントにアクセスできるようになります。

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
