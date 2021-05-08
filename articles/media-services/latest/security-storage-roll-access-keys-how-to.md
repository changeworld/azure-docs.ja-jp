---
title: ストレージ アクセス キーをローリングした後に Media Services v3 を更新する | Microsoft Docs
description: この記事は、ストレージ アクセス キー をローリングした後に Media Services v3 を更新する場合のガイダンスについて説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281423"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>ストレージ アクセス キーをローリングした後に Media Services v3 を更新する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

新しい Azure Media Services (AMS) アカウントを作成するときに、Azure Storage アカウントを選択するよう求められます。  Media Services アカウントには複数のストレージ アカウントを追加できます。 この記事では、ストレージ キーを入れ換える方法を示します。 また、メディア アカウントにストレージ アカウントを追加する方法も示します。

この記事で説明している操作を完了するには、[Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) と [PowerShell](/powershell/module/az.media) を使用する必要があります。  詳細については、[PowerShell とResource Manager を使用して Azure のリソースを管理する方法](../../azure-resource-manager/management/manage-resource-groups-powershell.md)に関するページをご覧ください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>ストレージ アクセス キーの生成

新しいストレージ アカウントが作成されると、Azure によって、ストレージ アカウントへのアクセス認証に使用される 512 ビット ストレージ アクセス キーが 2 つ生成されます。 ストレージの接続の安全性を高めるため、ストレージ アクセス キーを定期的に再生成して入れ換えてください。 片方のアクセス キーでストレージ アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ (プライマリとセカンダリ) 提供されます。 この手順は、「アクセス キーのローリング」とも呼ばれます。

Media Services は、指定されたストレージ キーに依存します。 特に、アセットのストリーミングやダウンロードに使用されるロケーターは指定されたアクセス キーに依存します。 AMS アカウントが作成されると、既定ではプライマリ ストレージ アクセス キーに依存します。 ただし、ユーザーは AMS が使用するストレージ キーを更新できます。 次の手順に従って、どのキーを使用するかを Media Services に知らせる必要があります。

>[!NOTE]
> ストレージ アカウントが複数ある場合、この手順をストレージ アカウントごとに実行する必要があります。 ストレージ キーを入れ換える順序は決まっていません。 セカンダリ キー、プライマリ キーの順に入れ換えることも、その逆の順にすることもできます。
>
> 手順を実稼働アカウントで実行する前に、実稼働前のアカウントでテストするようにしてください。
>

## <a name="steps-to-rotate-storage-keys"></a>ストレージ キーの入れ換え手順
 
 1. PowerShell コマンドレットまたは [Azure](https://portal.azure.com/) Portal を使ってストレージ アカウントのプライマリ キーを変更します。
 2. 適切なパラメーターを指定して `Sync-AzMediaServiceStorageKeys` コマンドレットを呼び出し、メディア アカウントによってストレージ アカウント キーが選択されるように強制します
 
    次の例では、キーをストレージ アカウントに同期する方法を示します。
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. 1 時間ほど待ちます。 ストリーミングのシナリオが機能していることを確認します。
 4. PowerShell コマンドレットまたは Azure portal を使ってストレージ アカウントのセカンダリ キーを変更します。
 5. 適切なパラメーターを指定して `Sync-AzMediaServiceStorageKeys` PowerShell を呼び出し、メディア アカウントによって新しいストレージ アカウント キーが選択されるように強制します。
 6. 1 時間ほど待ちます。 ストリーミングのシナリオが機能していることを確認します。
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell コマンドレットの例

次の例では、ストレージ アカウントを取得し、これを AMS アカウントと同期する方法を示します。

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS アカウントにストレージ アカウントを追加する手順

次の記事は、AMS アカウントにストレージ アカウントを追加する方法を示しています。[複数のストレージ アカウントを Media Services アカウントにアタッチする](storage-managing-multiple-storage-accounts-how-to.md)。
