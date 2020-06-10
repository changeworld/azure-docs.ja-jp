---
title: ファイルを含める
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465118"
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