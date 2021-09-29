---
title: Azure Communication Services - 通話概要と通話診断のログを有効化してアクセスする
titleSuffix: An Azure Communication Services concept document
description: Azure Monitor の通話概要と通話診断のログにアクセスする方法
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: b5c2da9842ba21b63c7b36d5b7377f74a25a1e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672278"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>通話概要と通話診断のログを有効にしてアクセスする

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Azure Communication Services の Voice リソースと Video リソースの利用統計情報にアクセスするには、次の手順のようにします。

## <a name="enable-logging"></a>ログの有効化
1. まず、ログ用のストレージ アカウントを作成する必要があります。 このステップを完了する手順については、「[ストレージ アカウントを作成する](../../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。 さまざまなストレージ オプションの種類と機能の詳細については、「[ストレージ アカウントの概要](../../storage/common/storage-account-overview.md)」も参照してください。 Azure ストレージ アカウントが既にある場合は、ステップ 2 に進みます。
 
1. ストレージ アカウントを作成したら、次に、「[リソースの診断ログの有効化](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource)」の手順に従って、ログ記録を有効にする必要があります。 ログ "CallSummaryPRIVATEPREVIEW" と "CallDiagnosticPRIVATEPREVIEW" のチェック ボックスをオンにします。 

1. 次に、[ストレージ アカウントへのアーカイブ] ボックスをオンにしてから、下のドロップダウン メニューでログ用のストレージ アカウントを選択します。 現在、この機能のプライベート プレビューでは [Send to Analytics workspace]\(Analytics ワークスペースに送信する\) オプションは使用できませんが、この機能が公開されるときは使用できるようになります。

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Azure Monitor の診断設定":::



## <a name="access-your-logs"></a>ログにアクセスする

ログにアクセスするには、Azure portal で[ストレージ アカウント](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)に移動することにより、上のステップ 3 で指定したストレージ アカウントに移動します。 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Azure Portal のストレージ":::

そこから、すべてのログまたは個々のログをダウンロードできます。

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Azure Portal のストレージのダウンロード":::

## <a name="next-steps"></a>次の手順

- [ログ記録と診断](./logging-and-diagnostics.md)について詳しく確認します。