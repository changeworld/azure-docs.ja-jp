---
title: Azure での BareMetal インスタンス ユニット
description: Azure portal を通じて BareMetal インスタンス ユニットを識別および操作する方法について説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: shortpatti
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2020
ms.author: v-patsho
ms.openlocfilehash: 8d94b54d909b6d0528150e6a678dd6e819ea78cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675756"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Azure portal を使用して BareMetal インスタンスを管理する
 
この記事では、[Azure portal](https://portal.azure.com/) で BareMetal インスタンスを表示する方法について説明します。 また、この記事では、デプロイされた BareMetal インスタンス ユニットを使用して Azure portal で実行できるアクティビティについても説明します。 
 
## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
BareMetal インスタンス用の Azure リソースプロバイダーでは、現在パブリック プレビュー中である Azure portal 内インスタンスの可視性が提供されています。 既定では、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションによって、*BareMetalInfrastructure* リソース プロバイダーが登録されます。 デプロイ済みの BareMetal インスタンス ユニットが表示されない場合は、リソース プロバイダーをサブスクリプションに登録する必要があります。 BareMetal インスタンス リソース プロバイダーを登録するには、2 つの方法があります。
 
* [Azure CLI](#azure-cli)
 
* [Azure Portal](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
Azure CLI を使用して、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションにサインインします。 BareMetalInfrastructure リソース プロバイダーを登録するには、次のようにします。

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
詳細については、「[Azure リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)」の記事を参照してください。
 
### <a name="azure-portal"></a>Azure portal
 
Azure portal を使用して BareMetalInfrastructure リソース プロバイダーを登録するには、次のようにします。
 
Azure portal でサブスクリプションを一覧表示し、BareMetal インスタンス ユニットのデプロイに使用したサブスクリプションをダブル クリックする必要があります。
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、表示するサブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**BareMetalInfrastructure**」と入力します。 リソース プロバイダーは、画像に示すように **[登録済み]** になっている必要があります。
 
>[!NOTE]
>リソース プロバイダーが登録済みでない場合は、 **[登録]** を選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="登録済みの BareMetal インスタンス ユニットを示すスクリーンショット":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure portal での BareMetal インスタンス ユニット
 
BareMetal インスタンスのデプロイ要求を送信するときに、BareMetal インスタンスに接続している Azure サブスクリプションを指定します。 BareMetal インスタンス ユニットに対して動作するアプリケーション レイヤーのデプロイに使っているのと同じサブスクリプションを使用してください。
 
BareMetal インスタンスのデプロイ中に、デプロイ要求で使用した Azure サブスクリプションに新しい [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)が作成されます。 この新しいリソース グループには、特定のサブスクリプションにデプロイしたすべての BareMetal インスタンス ユニットが一覧表示されます。

1. BareMetal サブスクリプションの Azure portal で、 **[リソース グループ]** を選びます。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="リソース グループの一覧を示すスクリーンショット":::

1. 一覧で、新しいリソース グループを見つけます。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="フィルター処理された [リソース グループ] リスト内の BareMetal インスタンス ユニットを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal インスタンスのデプロイに使用したサブスクリプションでフィルター処理を行うことができます。 適切なサブスクリプションにフィルターを適用すると、リソース グループの一覧が長くなる可能性があります。 末尾に **-Txxx** が付いたものを探します。xxx は 3 桁の数字で、たとえば **-T250** などです。

1. 新しいリソース グループを選択し、その詳細を表示します。 この画像は、デプロイされた 1 つの BareMetal インスタンス ユニットを示しています。
   
   >[!NOTE]
   >同じ Azure サブスクリプションに複数の BareMetal インスタンス テナントをデプロイした場合は、複数の Azure リソース グループが表示されます。
 
## <a name="view-the-attributes-of-a-single-instance"></a>単一インスタンスの属性の表示
 
単一ユニットの詳細を表示できます。 BareMetal インスタンスの一覧で、表示する単一インスタンスを選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="単一インスタンスの BareMetal インスタンス ユニットの属性を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
画像内の属性は、Azure 仮想マシン (VM) の属性とはまったく異なります。 左側には、リソース グループ、Azure リージョン、サブスクリプションの名前と ID が表示されます。 タグが割り当てられている場合は、ここにも表示されます。 既定では、BareMetal インスタンス ユニットにはタグが割り当てられていません。
 
右側には、ユニットの名前、オペレーティング システム (OS)、IP アドレス、および CPU スレッドとメモリの数を示す SKU が表示されます。 また、電源の状態とハードウェアのバージョン (BareMetal インスタンス スタンプのリビジョン) も表示されます。 電源状態では、ハードウェア ユニットの電源がオンであるかオフであるかが示されます。 ただし、オペレーティング システムの詳細では、起動して実行されているかどうかは示されません。
 
考えられるハードウェア リビジョンは次のとおりです。
 
* Revision 3
 
* Revision 4
 
* リビジョン 4.2
 
>[!NOTE]
>リビジョン 4.2 は、リビジョン 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Azure VM とリビジョン 4 のスタンプまたは行にデプロイされている BareMetal インスタンス ユニットとの間のネットワーク待機時間が大幅に改善しています。
 
また、右側には [Azure 近接配置グループ](../../../virtual-machines/linux/co-location.md)の名前があります。これは、デプロイされた各 BareMetal インスタンス ユニットに対して自動的に作成されます。 アプリケーション レイヤーをホストする Azure VM をデプロイする場合に近接配置グループを参照します。 BareMetal インスタンス ユニットに関連付けられている近接配置グループを使用すると、Azure VM が BareMetal インスタンス ユニットの近くにデプロイされるようになります。
 
>[!TIP]
>リビジョン 4.x と同じ Azure データセンターでアプリケーション レイヤーを検索するには、[最適なネットワーク待ち時間のための Azure 近接配置グループ](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)に関するページを参照してください。
 
## <a name="check-activities-of-a-single-instance"></a>単一インスタンスのアクティビティを確認する
 
単一ユニットのアクティビティを確認できます。 記録される主要なアクティビティの 1 つには、ユニットの再起動があります。 表示されるデータには、アクティビティの状態、トリガーされたアクティビティのタイムスタンプ、サブスクリプション ID、およびアクティビティをトリガーした Azure ユーザーが含まれます。
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="BareMetal インスタンス ユニットのアクティビティを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Azure でユニットのメタデータに加えられた変更も、アクティビティ ログに記録されます。 開始された再起動以外に、**Write BareMetallnstances** のアクティビティを確認できます。 このアクティビティでは、BareMetal インスタンス ユニット自体に変更は加えられませんが、Azure のユニットのメタデータに対する変更が記載されます。
 
記録されるもう 1 つのアクティビティは、インスタンスに対する[タグ](../../../azure-resource-manager/management/tag-resources.md)の追加または削除です。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>インスタンスの Azure タグを追加および削除する
 
Azure タグは、BareMetal インスタンス ユニットに追加することも削除することもできます。 タグが割り当てられる方法は、VM へのタグの割り当てと異なりません。 VM と同様に、タグは Azure メタデータ内に存在し、BareMetal インスタンスには VM のタグと同じ制限があります。
 
タグの削除は VM と同じように動作します。 タグの適用と削除は、BareMetal インスタンス ユニットのアクティビティ ログに一覧表示されます。
 
## <a name="check-properties-of-an-instance"></a>インスタンスのプロパティを確認する
 
インスタンスを取得すると、[プロパティ] セクションに移動して、インスタンスについて収集されたデータを表示できます。 収集されるデータには、Azure 接続性、ストレージ バックエンド、ExpressRoute 回線 ID、一意のリソース ID、およびサブスクリプション ID が含まれます。 この情報は、サポート要求で、またはストレージ スナップショット構成を設定するときに使用します。
 
表示されるもう 1 つの重要な情報は、ストレージ NFS の IP アドレスです。 これにより、ストレージが BareMetal インスタンス スタック内で **テナント** に分離されます。 この IP アドレスは、[ストレージ スナップショット バックアップの構成ファイル](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)を編集するときにも使用します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="BareMetal インスタンスのプロパティ設定を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Azure portal を使用したユニットの再起動
 
OS が再起動を完了しないさまざまな状況があります。この場合、BareMetal インスタンス ユニットの電源再起動が必要になります。 ユニットの電源再起動は、Azure portal から直接行うことができます。
 
**[再起動]** 、次に **[はい]** を選択して、ユニットの再起動を確認します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="BareMetal インスタンス ユニットを再起動する方法を示すスクリーンショット":::
 
BareMetal インスタンス ユニットを再起動すると、遅延が発生します。 この遅延中、電源状態が **[開始中]** から **[開始]** に移行し、OS が完全に起動したことが示されます。 その結果、再起動後、状態が **[開始]** に切り替わったらすぐにユニットにログインすることはできません。
 
>[!IMPORTANT]
>BareMetal インスタンス ユニットのメモリ容量によっては、ハードウェアとオペレーティング システムの再開と再起動に最大 1 時間かかることがあります。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal インスタンスのサポート要求を開く
 
BareMetal インスタンス ユニット専用のサポート要求を送信できます。
1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を入力します。
 
   - **問題の種類:** 問題の種類を選択する
 
   - **サブスクリプション:** サブスクリプションを選択します。
 
   - **サービス:** BareMetal インフラストラクチャ
 
   - **リソース:** インスタンスの名前を提供します
 
   - **概要:** 要求の概要を提供します
 
   - **問題の種類:** 問題の種類を選択します
 
   - **問題のサブタイプ:** 問題のサブタイプを選択します

1. **[解決策]** タブを選択して、問題の解決策を見つけます。 解決策が見つからない場合は、次のステップに進みます。

1. **[詳細]** タブを選択し、問題が VM にあるのか BareMetal インスタンス ユニットにあるのかを選択します。 この情報は、適切な専門家にサポート要求を送るのに役立ちます。

1. 問題がいつ始まったのかを指定し、インスタンスのリージョンを選択します。

1. 要求の詳細を提供し、必要に応じてファイルをアップロードします。

1. **[確認および作成]** を選択して、リクエストを送信します。
 
サポート担当者が要求を確認するまで、最大 5 営業日かかります。

## <a name="next-steps"></a>次のステップ

BareMetal の詳細については、[BareMetal のワークロードの種類](../../../virtual-machines/workloads/sap/get-started.md)に関するページを参照してください。