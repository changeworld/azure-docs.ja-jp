---
title: Azure PowerShell - BLOB ストレージ アカウントのサブスクライブ
description: この記事では、BLOB ストレージ アカウントの Event Grid イベントをサブスクライブする方法を示したサンプル Azure PowerShell スクリプトを紹介しています。
ms.devlang: powershell
ms.topic: sample
ms.date: 07/22/2021
ms.openlocfilehash: 5a224335f0e8eed4707f8123a92de28a6b5c7c61
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467364"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>PowerShell を使用した BLOB ストレージ アカウントのイベントのサブスクライブ

このスクリプトは、BLOB ストレージ アカウントのイベントに対する Event Grid サブスクリプションを作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用してイベント サブスクリプションを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid のサブスクリプションを作成する。 |

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](/powershell/azure/get-started-azureps)をご覧ください。
