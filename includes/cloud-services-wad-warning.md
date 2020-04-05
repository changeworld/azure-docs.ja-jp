---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279632"
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
