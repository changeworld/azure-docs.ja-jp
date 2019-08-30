---
title: Azure リソースを新しいサブスクリプションまたはリソース グループに移動する場合のエラーをトラブルシューティングする
description: Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 445ee2784a74a366089a49a0e2f2f17d51ef93bf
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624299"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング

この記事では、リソースを移動するときに問題の解決に役立つ方法を示します。

## <a name="upgrade-a-subscription"></a>サブスクリプションをアップグレードする

実際に Azure サブスクリプションをアップグレードする場合 (無料から従量課金制への切り替えなど)、お使いのサブスクリプションを変換する必要があります。

* 無料試用版をアップグレードするには、「[無料試用版または Microsoft Imagine Azure サブスクリプションを従量課金制にアップグレードする](../billing/billing-upgrade-azure-subscription.md)」をご覧ください。
* 従量課金制のアカウントを変更するには、「[Azure の従量課金制サブスクリプションを別のオファーに変更する](../billing/billing-how-to-switch-azure-offer.md)」をご覧ください。

サブスクリプションを変換できない場合は、[Azure サポート要求を作成](../azure-supportability/how-to-create-azure-support-request.md)してください。 問題の種類として **[サブスクリプション管理]** を選択します。

## <a name="service-limitations"></a>サービスの制限事項

一部のサービスでは、リソースを移動するときに追加の考慮が必要になります。 次のサービスを移動する場合は、必ずガイダンスと制限事項を確認してください。

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [クラシック デプロイ モデル](./move-limitations/classic-model-move-limitations.md)
* [ネットワーク](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>大規模な要求

可能であれば、大規模な移動は個別の移動操作に分けます。 1 回の操作に含まれるリソースが 800 を超えると、Resource Manager から直ちにエラーが返されます。 ただし、800 未満のリソースの移動でも、タイムアウトで失敗になることがあります。

## <a name="resource-not-in-succeeded-state"></a>正常な状態にないリソース

リソースが正常な状態にないために移動できないことを示すエラー メッセージが表示された場合、実際に移動をブロックしているのは依存リソースであることがあります。 [依存リソースの状態](./move-limitations/networking-move-limitations.md#state-of-dependent-resources)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。
