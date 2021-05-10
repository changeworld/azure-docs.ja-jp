---
title: Azure で BareMetal インフラストラクチャ インスタンスを接続する
description: Azure portal または Azure CLI を使用して BareMetal インスタンスを識別および操作する方法について説明します。
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: a7fdc17aa4271915f7dc02aaa2d7a688016bf892
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579167"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Azure で BareMetal インフラストラクチャ インスタンスを接続する

この記事では、[Azure portal](https://portal.azure.com/) 上で [BareMetal インスタンス](concepts-baremetal-infrastructure-overview.md)がどのように表示されるかを説明します。 また、この記事では、デプロイ済みの BareMetal インフラストラクチャ インスタンスに対して Azure portal で何を実行できるかについても説明します。 
 
## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
BareMetal インスタンス用の Azure リソース プロバイダーにより、このインスタンスの Azure portal 上での表示が実現します。 既定では、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションに *BareMetalInfrastructure* リソース プロバイダーが登録されます。 デプロイ済みの BareMetal インスタンスが表示されない場合は、リソース プロバイダーをサブスクリプションに登録する必要があります。 

BareMetal インスタンス リソース プロバイダーを登録するには、Azure portal または Azure CLI を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
Azure portal 上でサブスクリプションの一覧を表示し、BareMetal インスタンスのデプロイに使用したサブスクリプションをダブルクリックする必要があります。
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、サブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**BareMetalInfrastructure**」と入力します。 リソース プロバイダーは、画像に示すように **[登録済み]** になっている必要があります。
 
>[!NOTE]
>リソース プロバイダーが登録済みでない場合は、 **[登録]** を選択します。
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="登録された BareMetal インスタンスを示すスクリーンショット。":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI を使用して、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションにサインインします。 [az provider register](/cli/azure/provider#az_provider_register) コマンドを使用して、`BareMetalInfrastructure` リソース プロバイダーを登録します。

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

[az provider list](/cli/azure/provider#az_provider_list) コマンドを使用すると、使用可能なすべてのプロバイダーを表示できます。

---

リソース プロバイダーの詳細については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。  

## <a name="baremetal-instances-in-the-azure-portal"></a>Azure portal の BareMetal インスタンス
 
BareMetal インスタンスのデプロイ要求を送信するときは、BareMetal インスタンスに接続している Azure サブスクリプションを指定します。 BareMetal インスタンスに対して動作するアプリケーション レイヤーのデプロイに使っているものと同じサブスクリプションを使用してください。
 
BareMetal インスタンスのデプロイ中に、デプロイ要求で使用した Azure サブスクリプション内に新しい [Azure リソース グループ](../azure-resource-manager/management/manage-resources-portal.md)が作成されます。 この新しいリソース グループに、そのサブスクリプションでデプロイしたすべての BareMetal インスタンスが一覧表示されます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. BareMetal サブスクリプションの Azure portal で、 **[リソース グループ]** を選びます。
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="リソース グループの一覧を示すスクリーンショット。":::

1. 一覧で、新しいリソース グループを見つけます。
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="フィルター処理された [リソース グループ] 一覧内の BareMetal インスタンスを示すスクリーンショット。" lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal インスタンスのデプロイに使用したサブスクリプションでフィルター処理することができます。 適切なサブスクリプションにフィルターを適用すると、リソース グループの一覧が長くなる可能性があります。 末尾に **-Txxx** が付いたものを探します。xxx は 3 桁の数字で、たとえば **-T250** などです。

1. 詳細を表示する新しいリソース グループを選択します。 画像は、デプロイされている 1 つの BareMetal インスタンスを示しています。
   
   >[!NOTE]
   >同じ Azure サブスクリプションに複数の BareMetal インスタンス テナントをデプロイした場合は、複数の Azure リソース グループが表示されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

すべての BareMetal インスタンスを表示するには、リソース グループに対して [az baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) コマンドを実行します。

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output` パラメーターはグローバル パラメーターであり、すべてのコマンドで使用できます。 **table** 値を指定すると、出力がわかりやすい形式で表示されます。 詳細については、「[Azure CLI コマンドの出力形式](/cli/azure/format-output-azure-cli)」をご覧ください。

---

## <a name="view-the-attributes-of-a-single-instance"></a>単一インスタンスの属性の表示

1 つのインスタンスの詳細を表示できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

BareMetal インスタンスの一覧で、表示する 1 つのインスタンスを選択します。
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="1 つのインスタンスの BareMetal インスタンスの属性を示すスクリーンショット。" lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
画像内の属性は、Azure 仮想マシン (VM) の属性とはまったく異なります。 左側には、リソース グループ、Azure リージョン、サブスクリプションの名前と ID が表示されます。 タグが割り当てられている場合は、ここにも表示されます。 既定では、BareMetal インスタンスにはタグが割り当てられていません。
 
右側には、BareMetal インスタンスの名前、オペレーティング システム (OS)、IP アドレス、および CPU スレッドの数とメモリを示す SKU が表示されます。 電源の状態と、ハードウェアのバージョン (BareMetal インスタンス スタンプのリビジョン) も表示されます。 電源の状態では、ハードウェア ユニットの電源がオンかオフかが示されます。 ただし、オペレーティング システムの詳細では、起動して実行されているかどうかは示されません。
 
ハードウェア リビジョンは次のいずれかになります。

* リビジョン 3 (Rev 3)

* リビジョン 4 (Rev 4)
 
* リビジョン 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 は、既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 Azure VM と SAP HANA インスタンス間のネットワーク待機時間が大幅に改善しています。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。 詳細については、[Azure での BareMetal インフラストラクチャ](concepts-baremetal-infrastructure-overview.md)に関する記事を参照してください。

 
また、右側には、[Azure 近接配置グループ](../virtual-machines/co-location.md)の名前があります。これは、デプロイされた各 BareMetal インスタンスに対して自動的に作成されます。 アプリケーション レイヤーをホストする Azure VM をデプロイするときは、この近接配置グループを参照します。 BareMetal インスタンスに関連付けられている近接配置グループを使用すると、Azure VM が確実に BareMetal インスタンスの近くにデプロイされるようになります。
 
>[!TIP]
>リビジョン 4.x と同じ Azure データセンターでアプリケーション レイヤーを検索するには、[最適なネットワーク待ち時間のための Azure 近接配置グループ](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)に関するページを参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンスの詳細を表示するには、[az baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) コマンドを実行します。

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

インスタンス名がわからない場合は、上記で説明した `az baremetalinstance list` コマンドを実行します。

---
 
## <a name="check-activities-of-a-single-instance"></a>単一インスタンスのアクティビティを確認する
 
1 つの BareMetal インスタンスのアクティビティを確認できます。 記録される主要なアクティビティの 1 つは、インスタンスの再起動です。 表示されるデータには、アクティビティの状態、トリガーされたアクティビティのタイムスタンプ、サブスクリプション ID、およびアクティビティをトリガーした Azure ユーザーが含まれます。
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="BareMetal インスタンスのアクティビティを示すスクリーンショット。" lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Azure でインスタンスのメタデータに加えられた変更も、アクティビティ ログに記録されます。 開始された再起動以外に、**Write BareMetallnstances** のアクティビティを確認できます。 このアクティビティでは、BareMetal インスタンス自体に対する変更は行われませんが、Azure でユニットのメタデータに加えられた変更が記録されます。
 
記録されるもう 1 つのアクティビティは、インスタンスに対する[タグ](../azure-resource-manager/management/tag-resources.md)の追加または削除です。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>インスタンスの Azure タグを追加および削除する

### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
BareMetal インスタンスに対して、Azure タグの追加または削除を行うことができます。 タグは、VM にタグを割り当てるときと同様に割り当てられます。 VM と同様に、タグは Azure メタデータ内に存在します。 BareMeta インスタンスでは、タグに VM の場合と同じ制限があります。
 
タグの削除も、VM の場合と同様に機能します。 タグの適用と削除は、BareMetal インスタンスのアクティビティ ログに一覧表示されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンスへのタグの割り当ては、仮想マシンでのタグの割り当てと同様に機能します。 VM と同様に、タグは Azure メタデータ内に存在します。 BareMeta インスタンスでは、タグに VM の場合と同じ制限があります。

BareMetal インスタンスにタグを追加するには、[az baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) コマンドを実行します。

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

同じコマンドを使用して、タグを削除します。

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>インスタンスのプロパティを確認する
 
インスタンスを取得すると、[プロパティ] セクションに移動して、インスタンスについて収集されたデータを表示できます。 収集されるデータには、Azure の接続性、ストレージ バックエンド、ExpressRoute 回線 ID、一意のリソース ID、サブスクリプション ID が含まれます。 この情報は、サポート要求で、またはストレージ スナップショット構成を設定するときに使用します。
 
表示されるもう 1 つの重要な情報は、ストレージ NFS の IP アドレスです。 これにより、ストレージが BareMetal インスタンス スタック内の自分の **テナント** に分離されます。 この IP アドレスは、[ストレージ スナップショット バックアップの構成ファイル](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)を編集するときにも使用します。
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="BareMetal インスタンスのプロパティ設定を示すスクリーンショット。" lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Azure portal を使用して BareMetal インスタンスを再起動する

OS が再起動を完了できないさまざまな状況があります。その場合は、BareMetal インスタンスの電源再起動が必要になります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

インスタンスの電源再起動は、Azure portal から直接行うことができます。
 
**[再起動]** 、 **[はい]** の順に選択して、再起動を確認します。
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="BareMetal インスタンスを再起動する方法を示すスクリーンショット。":::
 
BareMetal インスタンスを再起動する際には、待ち時間が発生します。 この遅延中、電源状態が **[開始中]** から **[開始]** に移行し、OS が完全に起動したことが示されます。 そのため、再起動後、状態が **[開始]** に切り替わった時点で初めてユニットにログインできます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンスを再起動するには、[az baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) コマンドを使用します。

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>BareMetal インスタンスのメモリ容量によっては、再起動およびハードウェアとオペレーティング システムのリブートに最大 1 時間かかることがあります。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>BareMetal インスタンスのサポート要求を開く
 
BareMetal インスタンス専用のサポート要求を送信できます。
1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を入力します。
 
   - **問題の種類:** 問題の種類を選択します。
 
   - **サブスクリプション:** ご使用のサブスクリプションを選択します。
 
   - **サービス:** BareMetal インフラストラクチャ
 
   - **リソース:** インスタンスの名前を入力します。
 
   - **概要:** 要求の概要を入力します。
 
   - **問題の種類:** 問題の種類を選択します。
 
   - **問題のサブタイプ:** 問題のサブタイプを選択します。

1. **[解決策]** タブを選択して、問題の解決策を見つけます。 解決策が見つからない場合は、次のステップに進みます。

1. **[詳細]** タブを選択し、問題が VM に関するものか、BareMetal インスタンスに関するものかを選択します。 この情報は、適切な専門家にサポート要求を送るのに役立ちます。

1. 問題がいつ始まったのかを指定し、インスタンスのリージョンを選択します。

1. 要求の詳細を提供し、必要に応じてファイルをアップロードします。

1. **[確認および作成]** を選択して、リクエストを送信します。
 
サポート担当者が要求を確認するまで、最大 5 営業日かかります。

## <a name="next-steps"></a>次のステップ

ワークロードの詳細:

- [SAP HANA on Azure (L インスタンス) とは](../virtual-machines/workloads/sap/hana-overview-architecture.md)
