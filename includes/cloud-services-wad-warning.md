---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "67181517"
---
> [!WARNING]
> 既存のロールで診断を有効にすると、パッケージがデプロイされるときに、既に設定してあるすべての拡張機能が無効になります。 チェックの内容は次のとおりです
>
> * Microsoft Monitoring Agent 診断
> * Microsoft Azure セキュリティ監視
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler Agent      
> * Microsoft Azure ドメイン拡張機能        
> * Microsoft Azure Diagnostics 拡張機能   
> * Microsoft Azure リモート デスクトップ拡張機能
> * Microsoft Azure Log Collector
>
> 更新したロールをデプロイした後、Azure Portal または PowerShell で拡張機能を再度設定できます。
>
