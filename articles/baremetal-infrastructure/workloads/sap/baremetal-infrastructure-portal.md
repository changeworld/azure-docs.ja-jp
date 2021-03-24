---
title: Azure での BareMetal インスタンス ユニット
description: Azure portal を通じて BareMetal インスタンス ユニットを識別および操作する方法について説明します。
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 076e84473a7d067712625dd12a2d5cae42bfa91a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548167"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Azure portal を使用して BareMetal インスタンスを管理する
 
この記事では、[Azure portal](https://portal.azure.com/) 上で [BareMetal インスタンス](baremetal-overview-architecture.md)がどのように表示されるかを説明します。 また、この記事では、デプロイされた BareMetal インスタンス ユニットを使用して Azure portal で実行できるアクティビティについても説明します。 
 
## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
BareMetal インスタンス用の Azure リソースプロバイダーでは、現在パブリック プレビュー中である Azure portal 内インスタンスの可視性が提供されています。 既定では、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションによって、*BareMetalInfrastructure* リソース プロバイダーが登録されます。 デプロイ済みの BareMetal インスタンス ユニットが表示されない場合は、リソース プロバイダーをサブスクリプションに登録する必要があります。 

BareMetal インスタンス リソース プロバイダーを登録するには、Azure portal または Azure CLI を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
Azure portal でサブスクリプションを一覧表示し、BareMetal インスタンス ユニットのデプロイに使用したサブスクリプションをダブル クリックする必要があります。
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、表示するサブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**BareMetalInfrastructure**」と入力します。 リソース プロバイダーは、画像に示すように **[登録済み]** になっている必要があります。
 
>[!NOTE]
>リソース プロバイダーが登録済みでない場合は、 **[登録]** を選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="登録済みの BareMetal インスタンス ユニットを示すスクリーンショット":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI を使用して、BareMetal インスタンスのデプロイに使用する Azure サブスクリプションにサインインします。 [az provider register](/cli/azure/provider#az_provider_register) コマンドを使用して、`BareMetalInfrastructure` リソース プロバイダーを登録します。

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

[az provider list](/cli/azure/provider#az_provider_list) コマンドを使用すると、使用可能なすべてのプロバイダーを表示できます。

---

リソース プロバイダーの詳細については、「[Azure リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure portal での BareMetal インスタンス ユニット
 
BareMetal インスタンスのデプロイ要求を送信するときに、BareMetal インスタンスに接続している Azure サブスクリプションを指定します。 BareMetal インスタンス ユニットに対して動作するアプリケーション レイヤーのデプロイに使っているのと同じサブスクリプションを使用してください。
 
BareMetal インスタンスのデプロイ中に、デプロイ要求で使用した Azure サブスクリプションに新しい [Azure リソース グループ](../../../azure-resource-manager/management/manage-resources-portal.md)が作成されます。 この新しいリソース グループには、特定のサブスクリプションにデプロイしたすべての BareMetal インスタンス ユニットが一覧表示されます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. BareMetal サブスクリプションの Azure portal で、 **[リソース グループ]** を選びます。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="リソース グループの一覧を示すスクリーンショット":::

1. 一覧で、新しいリソース グループを見つけます。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="フィルター処理された [リソース グループ] リスト内の BareMetal インスタンス ユニットを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >BareMetal インスタンスのデプロイに使用したサブスクリプションでフィルター処理を行うことができます。 適切なサブスクリプションにフィルターを適用すると、リソース グループの一覧が長くなる可能性があります。 末尾に **-Txxx** が付いたものを探します。xxx は 3 桁の数字で、たとえば **-T250** などです。

1. 新しいリソース グループを選択し、その詳細を表示します。 この画像は、デプロイされた 1 つの BareMetal インスタンス ユニットを示しています。
   
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

単一ユニットの詳細を表示できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

BareMetal インスタンスの一覧で、表示する単一インスタンスを選択します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="単一インスタンスの BareMetal インスタンス ユニットの属性を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
画像内の属性は、Azure 仮想マシン (VM) の属性とはまったく異なります。 左側には、リソース グループ、Azure リージョン、サブスクリプションの名前と ID が表示されます。 タグが割り当てられている場合は、ここにも表示されます。 既定では、BareMetal インスタンス ユニットにはタグが割り当てられていません。
 
右側には、ユニットの名前、オペレーティング システム (OS)、IP アドレス、および CPU スレッドとメモリの数を示す SKU が表示されます。 また、電源の状態とハードウェアのバージョン (BareMetal インスタンス スタンプのリビジョン) も表示されます。 電源状態では、ハードウェア ユニットの電源がオンであるかオフであるかが示されます。 ただし、オペレーティング システムの詳細では、起動して実行されているかどうかは示されません。
 
ハードウェア リビジョンは次のいずれかになります。

* リビジョン 3 (Rev 3)

* リビジョン 4 (Rev 4)
 
* リビジョン 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 は、既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 これにより、Azure VM と Rev 4 のスタンプまたは行にデプロイされた BareMetal インスタンス ユニットの間のネットワーク待ち時間が大幅に改善されます。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。 詳細については、[Azure での BareMetal インフラストラクチャ](baremetal-overview-architecture.md)に関する記事を参照してください。
 
また、右側には、[Azure 近接配置グループ](../../../virtual-machines/co-location.md)の名前があります。これは、デプロイされた各 BareMetal インスタンス ユニットに対して自動的に作成されます。 アプリケーション レイヤーをホストする Azure VM をデプロイする場合に近接配置グループを参照します。 BareMetal インスタンス ユニットに関連付けられている近接配置グループを使用すると、Azure VM が BareMetal インスタンス ユニットの近くにデプロイされるようになります。
 
>[!TIP]
>リビジョン 4.x と同じ Azure データセンターでアプリケーション レイヤーを検索するには、[最適なネットワーク待ち時間のための Azure 近接配置グループ](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)に関するページを参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンスの詳細を表示するには、[az baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) コマンドを実行します。

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

インスタンス名がわからない場合は、上記で説明した `az baremetalinstance list` コマンドを実行します。

---
 
## <a name="check-activities-of-a-single-instance"></a>単一インスタンスのアクティビティを確認する
 
単一ユニットのアクティビティを確認できます。 記録される主要なアクティビティの 1 つには、ユニットの再起動があります。 表示されるデータには、アクティビティの状態、トリガーされたアクティビティのタイムスタンプ、サブスクリプション ID、およびアクティビティをトリガーした Azure ユーザーが含まれます。
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="BareMetal インスタンス ユニットのアクティビティを示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Azure でユニットのメタデータに加えられた変更も、アクティビティ ログに記録されます。 開始された再起動以外に、**Write BareMetallnstances** のアクティビティを確認できます。 このアクティビティでは、BareMetal インスタンス ユニット自体に変更は加えられませんが、Azure のユニットのメタデータに対する変更が記載されます。
 
記録されるもう 1 つのアクティビティは、インスタンスに対する[タグ](../../../azure-resource-manager/management/tag-resources.md)の追加または削除です。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>インスタンスの Azure タグを追加および削除する

### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
Azure タグは、BareMetal インスタンス ユニットに追加することも削除することもできます。 タグが割り当てられる方法は、VM へのタグの割り当てと異なりません。 VM と同様に、タグは Azure メタデータ内に存在し、BareMetal インスタンスには VM のタグと同じ制限があります。
 
タグの削除は VM と同じように動作します。 タグの適用と削除は、BareMetal インスタンス ユニットのアクティビティ ログに一覧表示されます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンスへのタグの割り当ては、仮想マシンの場合と同様に機能します。 タグは Azure メタデータ内に存在し、BareMetal インスタンスでも、VM のタグと同じ制限があります。

BareMetal インスタンス ユニットにタグを追加するには、[az baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) コマンドを実行します。

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

同じコマンドを使用して、タグを削除します。

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>インスタンスのプロパティを確認する
 
インスタンスを取得すると、[プロパティ] セクションに移動して、インスタンスについて収集されたデータを表示できます。 収集されるデータには、Azure 接続性、ストレージ バックエンド、ExpressRoute 回線 ID、一意のリソース ID、およびサブスクリプション ID が含まれます。 この情報は、サポート要求で、またはストレージ スナップショット構成を設定するときに使用します。
 
表示されるもう 1 つの重要な情報は、ストレージ NFS の IP アドレスです。 これにより、ストレージが BareMetal インスタンス スタック内で **テナント** に分離されます。 この IP アドレスは、[ストレージ スナップショット バックアップの構成ファイル](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)を編集するときにも使用します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="BareMetal インスタンスのプロパティ設定を示すスクリーンショット" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Azure portal を使用したユニットの再起動

OS が再起動を完了しないさまざまな状況があります。この場合、BareMetal インスタンス ユニットの電源再起動が必要になります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ユニットの電源再起動は、Azure portal から直接行うことができます。
 
**[再起動]** 、次に **[はい]** を選択して、ユニットの再起動を確認します。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="BareMetal インスタンス ユニットを再起動する方法を示すスクリーンショット":::
 
BareMetal インスタンス ユニットを再起動すると、遅延が発生します。 この遅延中、電源状態が **[開始中]** から **[開始]** に移行し、OS が完全に起動したことが示されます。 その結果、再起動後、状態が **[開始]** に切り替わったらすぐにユニットにログインすることはできません。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BareMetal インスタンス ユニットを再起動するには、[az baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) コマンドを使用します。

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

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

BareMetal の詳細については、[BareMetal のワークロードの種類に関する記事](../../../virtual-machines/workloads/sap/get-started.md)を参照してください。