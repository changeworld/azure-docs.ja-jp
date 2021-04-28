---
title: リソース グループ、サブスクリプションまたはリージョン間で Azure リソースを移動する
description: リソース グループ、サブスクリプション、またはリージョン間で移動できる Azure リソースの種類の概要。
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730341"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>リソース グループ、サブスクリプションまたはリージョン間で Azure リソースを移動する

Azure リソースは、新しいリソース グループまたはサブスクリプションに移動することも、リージョン間で移動することもできます。

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>リソース グループまたはサブスクリプション間でリソースを移動する

Azure リソースは、別の Azure サブスクリプションまたは同じサブスクリプションの別のリソース グループに移動できます。 リソースの移動には、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用できます。 詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](move-resource-group-and-subscription.md)」を参照してください。

### <a name="upgrade-a-subscription"></a>サブスクリプションをアップグレードする

実際に Azure サブスクリプションをアップグレードする場合 (無料から従量課金制への切り替えなど)、お使いのサブスクリプションを変換する必要があります。

- 無料試用版をアップグレードするには、「[無料試用版または Microsoft Imagine Azure サブスクリプションを従量課金制にアップグレードする](../../cost-management-billing/manage/upgrade-azure-subscription.md)」をご覧ください。
- 従量課金制のアカウントを変更するには、「[Azure の従量課金制サブスクリプションを別のオファーに変更する](../../cost-management-billing/manage/switch-azure-offer.md)」をご覧ください。

サブスクリプションを変換できない場合は、[Azure サポート要求を作成](../../azure-portal/supportability/how-to-create-azure-support-request.md)してください。 問題の種類として **[サブスクリプション管理]** を選択します。

## <a name="move-resources-across-regions"></a>リソースをリージョン間で移動する

Azure の地理、リージョン、および可用性ゾーンによって Azure グローバル インフラストラクチャの基礎が形成されます。 Azure の[地域](https://azure.microsoft.com/global-infrastructure/geographies/)には通常、2 つ以上の [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)が含まれます。 リージョンとは、可用性ゾーンや複数のデータ センターが含まれている、地域内のある領域のことです。

特定の Azure リージョンにリソースをデプロイした後、それらのリソースを別のリージョンに移動したくなる理由が数多く存在します。

- **リージョンの立ち上げに合わせる**: 以前には使用できなかった、新しく導入された Azure リージョンにリソースを移動します。
- **サービス/機能に合わせる**: 特定のリージョンで使用可能になったサービスまたは機能を利用するためにリソースを移動します。
- **ビジネスの展開に対応する**: 合併や買収などのビジネスの変化に対応して、あるリージョンにリソースを移動します。
- **距離の近さに合わせる**: ビジネスの近くに存在するリージョンにリソースを移動します。
- **データの要件を満たす**: データ所在地の要件やデータ分類のニーズに合わせるためにリソースを移動します。 [詳細については、こちらを参照してください](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **デプロイ要件に対応する**: デプロイ時にエラーが発生したリソースを移動するか、または容量のニーズに対応して移動します。
- **使用停止に対応する**: リージョンの使用停止のためにリソースを移動します。

### <a name="move-resources-with-resource-mover"></a>Resource Mover を使用してリソースを移動する

[Azure Resource Mover](../../resource-mover/overview.md) を使用して、リソースを別のリージョンに移動できます。 Resource Mover によって、以下のことが可能になります。

- 単一のハブを使用してリージョン間でリソースを移動できます。
- 移動時間と複雑さを軽減できます。 必要なものをすべて 1 か所にまとめることができます。
- シンプルかつ一貫性のあるエクスペリエンスによって、さまざまな種類の Azure リソースを移動できます。
- 移動するリソース間の依存関係を簡単に識別できます。 この識別により、関連リソースをまとめて移動することができるため、移動後にすべてのリソースがターゲット リージョンで想定どおりに動作します。
- 移動後にソース リージョンのリソースを削除する場合は、自動的にクリーンアップされます。
- テスト。 移動を試してみて、完全に移動することは望まない場合は破棄することができます。

2 つの異なる方法を使用して、リソースを別のリージョンへ移動できます。

- **リソース グループからリソースの移動を開始する**: この方法を使用した場合、リソース グループ内からリージョンの移動を開始します。 移動するリソースを選択した後に、リソースの依存関係を確認するために Resource Mover ハブでプロセスが継続され、移動のプロセスを調整します。 [詳細については、こちらを参照してください](../../resource-mover/move-region-within-resource-group.md)。
- **Resource Mover ハブから直接リソースの移動を開始する**: この方法を使用した場合、ハブ上で直接、リージョンの移動プロセスを開始します。 [詳細については、こちらを参照してください](../../resource-mover/tutorial-move-region-virtual-machines.md)。

## <a name="next-steps"></a>次のステップ

- リソースの種類が移動をサポートしているかどうかを確認するには、「[リソースの移動操作のサポート](move-support-resources.md)」を参照してください。
- リージョンの移動プロセスの詳細については、「[移動プロセスについて](../../resource-mover/about-move-process.md)」を参照してください。
