---
title: Azure での BareMetal インスタンス ユニット
description: Azure portal を使用して BareMetal インスタンス ユニットを識別および操作する方法について説明します。
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: 30e1661e82546dbaf6d8dc4288ad896df89f401e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861034"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Azure portal を使用して BareMetal インスタンスを管理する
 
この記事では、[Azure portal](https://portal.azure.com/) 上で [BareMetal インスタンス](baremetal-overview-architecture.md)がどのように表示されるかを説明します。 また、この記事では、デプロイ済みの BareMetal インスタンス ユニットに対して Azure portal 上で実行できるアクティビティについても説明します。 
 
## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
BareMetal インスタンス用の Azure リソース プロバイダー (現在はパブリック プレビュー) により、このインスタンスの Azure portal 上での表示が提供されます。 既定では、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションに *BareMetalInfrastructure* リソース プロバイダーが登録されます。 デプロイ済みの BareMetal インスタンス ユニットが表示されない場合は、リソース プロバイダーをサブスクリプションに登録する必要があります。 

BareMetal インスタンス リソース プロバイダーを登録するには、2 つの方法があります。
 
* [Azure CLI](#azure-cli)
 
* [Azure Portal](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
Azure CLI を使用して、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションにサインインします。 BareMetalInfrastructure リソース プロバイダーを登録するには、次のようにします。

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
詳細については、「[Azure リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)」の記事を参照してください。
 
### <a name="azure-portal"></a>Azure portal
 
Azure portal を使用して BareMetalInfrastructure リソース プロバイダーを登録できます。
 
Azure portal 上でサブスクリプションのリストを表示し、BareMetal インスタンス ユニットのデプロイに使用したサブスクリプションをダブルクリックする必要があります。
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、表示するサブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**BareMetalInfrastructure**」と入力します。 リソース プロバイダーは、画像に示すように **[登録済み]** になっている必要があります。
 
>[!NOTE]
>リソース プロバイダーが登録済みでない場合は、 **[登録]** を選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="登録済みの BareMetal インスタンス ユニットを示すスクリーンショット":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure portal 上の BareMetal インスタンス ユニット
 
BareMetal インスタンスのデプロイ要求を送信するときは、BareMetal インスタンスに接続している Azure サブスクリプションを指定します。 BareMetal インスタンス ユニットに対して動作するアプリケーション レイヤーのデプロイに使っているのと同じサブスクリプションを使用してください。
 
BareMetal インスタンスのデプロイ中に、デプロイ要求で使用した Azure サブスクリプション内に新しい [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)が作成されます。 この新しいリソース グループには、その特定のサブスクリプションにデプロイしたすべての BareMetal インスタンス ユニットが一覧表示されます。

1. Azure portal 上の BareMetal サブスクリプション内で、 **[リソース グループ]** を選択します。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="リソース グループのリストを示すスクリーンショット":::

1. リスト内で、新しいリソース グループを見つけます。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="フィルター処理された [リソース グループ] リスト内の BareMetal インスタンス ユニットを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal インスタンスのデプロイに使用したサブスクリプションでフィルター処理することができます。 正しいサブスクリプションでフィルター処理した後も、リソース グループのリストが長いままの場合があります。 末尾に **-Txxx** が付いたものを探します。xxx は 3 桁の数字で、たとえば **-T250** のようになります。

1. 新しいリソース グループを選択して、その詳細を表示します。 画像は、デプロイされている 1 つの BareMetal インスタンス ユニットを示しています。
   
   >[!NOTE]
   >同じ Azure サブスクリプションに複数の BareMetal インスタンス テナントをデプロイした場合は、複数の Azure リソース グループが表示されます。
 
## <a name="view-the-attributes-of-a-single-instance"></a>1 つのインスタンスの属性の表示
 
1 つのユニットの詳細を表示できます。 BareMetal インスタンスのリストで、表示する 1 つのインスタンスを選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="1 つのインスタンスの BareMetal インスタンス ユニットの属性を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
画像に示された属性は、Azure 仮想マシン (VM) の属性と大きな違いはありません。 左側には、リソース グループ、Azure リージョン、サブスクリプションの名前と ID が表示されます。 タグが割り当てられている場合は、それもここに表示されます。 既定では、BareMetal インスタンス ユニットにはタグが割り当てられていません。
 
右側には、ユニットの名前、オペレーティング システム (OS)、IP アドレス、および CPU スレッドの数とメモリを示す SKU が表示されます。 電源の状態と、ハードウェアのバージョン (BareMetal インスタンス スタンプのリビジョン) も表示されます。 電源の状態では、ハードウェア ユニットの電源がオンかオフかが示されます。 ただし、オペレーティング システムの詳細では、稼働中かどうかは示されません。
 
ハードウェア リビジョンは次のいずれかになります。

* リビジョン 3 (Rev 3)

* リビジョン 4 (Rev 4)
 
* リビジョン 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 は、既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 これにより、Azure VM と Rev 4 のスタンプまたは行にデプロイされた BareMetal インスタンス ユニットの間のネットワーク待ち時間が大幅に改善されます。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。 詳細については、[Azure での BareMetal インフラストラクチャ](baremetal-overview-architecture.md)に関する記事を参照してください。
 
また、右側には、[Azure 近接配置グループ](../../../virtual-machines/linux/co-location.md)の名前があります。これは、デプロイされた各 BareMetal インスタンス ユニットに対して自動的に作成されます。 アプリケーション レイヤーをホストする Azure VM をデプロイするときは、この近接配置グループを参照します。 BareMetal インスタンス ユニットに関連付けられている近接配置グループを使用すると、Azure VM が確実に BareMetal インスタンス ユニットの近くにデプロイされるようになります。
 
>[!TIP]
>リビジョン 4.x と同じ Azure データセンター内にアプリケーション レイヤーを配置する方法については、[最適なネットワーク待ち時間を実現するための Azure 近接配置グループに関する記事](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)を参照してください。
 
## <a name="check-activities-of-a-single-instance"></a>1 つのインスタンスのアクティビティの確認
 
1 つのユニットのアクティビティを確認できます。 記録される主要なアクティビティの 1 つは、ユニットの再起動です。 表示されるデータには、アクティビティの状態、アクティビティがトリガーされたときのタイムスタンプ、サブスクリプション ID、アクティビティをトリガーした Azure ユーザーが含まれます。
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="BareMetal インスタンス ユニットのアクティビティを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Azure でユニットのメタデータに加えられた変更も、アクティビティ ログに記録されます。 開始された再起動に加えて、**Write BareMetallnstances** のアクティビティを確認できます。 このアクティビティでは、BareMetal インスタンス ユニット自体に対する変更は行われませんが、Azure でユニットのメタデータに加えられた変更が記録されます。
 
記録されるもう 1 つのアクティビティは、インスタンスに対する[タグ](../../../azure-resource-manager/management/tag-resources.md)の追加または削除です。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>インスタンスに対する Azure タグの追加と削除
 
BareMetal インスタンス ユニットに対して、Azure タグの追加または削除を行うことができます。 タグの割り当て方法は、VM へのタグの割り当てと変わりません。 VM の場合と同様に、タグは Azure メタデータ内に存在し、BareMetal インスタンスでも、VM のタグと同じ制限があります。
 
タグの削除は VM の場合と同様に動作します。 タグの適用と削除は、BareMetal インスタンス ユニットのアクティビティ ログに一覧表示されます。
 
## <a name="check-properties-of-an-instance"></a>インスタンスのプロパティの確認
 
インスタンスを取得したら、[プロパティ] セクションに移動して、インスタンスに関して収集されたデータを表示できます。 収集されるデータには、Azure 接続性、ストレージ バックエンド、ExpressRoute 回線 ID、一意のリソース ID、サブスクリプション ID が含まれます。 この情報は、サポート リクエストで使用したり、ストレージ スナップショットの構成を設定する際に使用します。
 
表示されるもう 1 つの重要な情報は、ストレージ NFS の IP アドレスです。 これにより、ストレージが BareMetal インスタンス スタック内の自分の **テナント** に分離されます。 この IP アドレスは、[ストレージ スナップショット バックアップの構成ファイル](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)を編集するときに使用します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="BareMetal インスタンスのプロパティ設定を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Azure portal からのユニットの再起動
 
OS が再起動を完了できないさまざまな状況があります。その場合は、BareMetal インスタンス ユニットの電源再起動が必要になります。 ユニットの電源再起動は、Azure portal から直接行うことができます。
 
**[再起動]** 、 **[はい]** の順に選択して、ユニットの再起動を確認します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="BareMetal インスタンス ユニットを再起動する方法を示すスクリーンショット":::
 
BareMetal インスタンス ユニットを再起動する際には、待ち時間が発生します。 この待ち時間中に、電源の状態が **[Starting]\(起動中\)** から **[Started]\(起動済み\)** に移行します。これは OS が完全に起動したことを意味します。 そのため、再起動後、状態が **[Started]\(起動済み\)** に切り替わったらすぐにユニットにログインすることはできません。
 
>[!IMPORTANT]
>BareMetal インスタンス ユニットのメモリ容量によっては、再起動とハードウェアのリブートに最大 1 時間かかることがあります。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal インスタンスのサポート リクエストのオープン
 
BareMetal インスタンス ユニット専用のサポート リクエストを送信できます。
1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を入力します。
 
   - **問題の種類:** 問題の種類を選択する
 
   - **サブスクリプション:** サブスクリプションを選択します。
 
   - **サービス:** BareMetal インフラストラクチャ
 
   - **リソース:** インスタンスの名前を入力します
 
   - **概要:** 要求の概要を入力します
 
   - **問題の種類:** 問題の種類を選択します
 
   - **問題のサブタイプ:** 問題のサブタイプを選択します

1. **[解決策]** タブを選択して、問題の解決策を見つけます。 解決策が見つからない場合は、次のステップに進みます。

1. **[詳細]** タブを選択し、問題が VM に関するものか、BareMetal インスタンス ユニットに関するものかを選択します。 この情報は、適切な専門家にサポート リクエストを送るのに役立ちます。

1. 問題がいつ始まったのかを指定し、インスタンスのリージョンを選択します。

1. 要求の詳細を入力し、必要に応じてファイルをアップロードします。

1. **[確認および作成]** を選択して、リクエストを送信します。
 
サポート担当者が要求を確認するまで、最大 5 営業日かかります。

## <a name="next-steps"></a>次のステップ

BareMetal の詳細については、[BareMetal のワークロードの種類に関する記事](../../../virtual-machines/workloads/sap/get-started.md)を参照してください。