---
title: 削除された App Service アプリを復元する - Azure App Service
description: PowerShell を使用して、削除された App Service アプリを復元する方法について説明します。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7dc3934f486b205febd5be3c0b484dfd2c97bb8f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755540"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>PowerShell を使用して、削除された App Service アプリを復元する

Azure App Service で誤ってアプリを削除した場合は、[Az PowerShell モジュール](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)のコマンドを使用して復元できます。

## <a name="list-deleted-apps"></a>削除したアプリの一覧を表示する

削除されたアプリのコレクションを取得するには、`Get-AzDeletedWebApp` を使用します。

削除された特定のアプリの詳細については、次を使用できます。

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

詳細情報には次のものが含まれます。

- **DeletedSiteId**:同じ名前の複数のアプリが削除されたシナリオで使用される、アプリの一意の識別子
- **SubscriptionID**:削除されたリソースを含むサブスクリプション
- **[場所]** :元のアプリの場所
- **ResourceGroupName**:元のリソース グループの名前
- **Name**:元のアプリの場所。
- **Slot**: スロットの名前。
- **削除時刻**:アプリが削除された日時  

## <a name="restore-deleted-app"></a>削除したアプリを復元する

復元するアプリが特定されたら、`Restore-AzDeletedWebApp` を使用して復元できます。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

コマンドの入力は次のとおりです。

- **リソース グループ**:アプリが復元されるターゲット リソース グループ
- **Name**:アプリの名前。グローバルに一意である必要があります。
- **TargetAppServicePlanName**:アプリにリンクされている App Service プラン

既定では `Restore-AzDeletedWebApp` によって、アプリの構成とコンテンツの両方が復元されます。 コンテンツのみを復元する場合は、このコマンドレットで `-RestoreContentOnly` フラグを使用します。

> [!NOTE]
> アプリが App Service Environment 上でホストされ、そこから削除された場合は、対応する App Service Environment が引き続き存在する場合にのみ復元できます。
>

コマンドレットの完全な参照は、次をご覧ください。[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
