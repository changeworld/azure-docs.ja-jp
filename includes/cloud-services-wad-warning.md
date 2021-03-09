---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
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
