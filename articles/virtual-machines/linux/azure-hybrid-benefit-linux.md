---
title: Azure ハイブリッド特典と Linux VM
description: Azure ハイブリッド特典が、Azure で実行されている Linux 仮想マシンのコストの削減にどのように役立つかについて説明します。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 1bc108f76ac35b13474de18d473f5728dbad9d23
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560018"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Azure ハイブリッド特典を Linux 仮想マシンに適用する方法

Azure ハイブリッド特典は、クラウドで Red Hat Enterprise Linux (RHEL) および SUSE Linux Enterprise Server (SLES) 仮想マシン (VM) を実行するコストを大幅に削減するのに役立つライセンス特典です。 この特典により、ソフトウェア料金は RHEL または SLES サブスクリプションによってカバーされるため、お客様には VM のインフラストラクチャ コストのみが課金されます。 この特典は、すべての RHEL および SLES Marketplace の従量課金制 (PAYG) イメージに適用されます。

Linux VM の Azure ハイブリッド特典は、一般提供されるようになりました。

## <a name="benefit-description"></a>特典の説明

Azure ハイブリッド特典を通じて、Azure 上の既存の RHEL および SLES の PAYG VM を、サブスクリプション持ち込み (BYOS) 課金に変換することで、オンプレミスの RHEL および SLES サーバーを Azure に移行できます。 通常、PAYG イメージからデプロイされた Azure 上の VM の場合、インフラストラクチャの料金とソフトウェアの料金の両方が請求されます。 Azure ハイブリッド特典を使用すると、PAYG VM を再デプロイせずに BYOS 課金モデルに変換できるため、ダウンタイムのリスクを回避できます。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux VM での Azure ハイブリッド特典コストの視覚化。":::

RHEL または SLES VM で特典を有効にした後で、PAYG VM で通常発生する追加のソフトウェア料金に対する課金はなくなります。 代わりに、VM は BYOS の課金を開始します。これにはコンピューティング ハードウェア料金のみが含まれ、ソフトウェア料金は含まれません。

特典が有効になっている VM を PAYG 課金モデルに戻すこともできます。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux VM の Azure ハイブリッド特典の対象範囲

Azure ハイブリッド特典は、Azure Marketplace のすべての RHEL および SLES PAYG イメージで使用できます。 この特典は、現時点では Azure Marketplace の RHEL または SLES BYOS イメージまたはカスタム イメージでは使用できません。

Linux VM で予約インスタンス、Azure Dedicated Host インスタンス、および SQL ハイブリッド特典を既に使用している場合は、Azure ハイブリッド特典の対象になりません。

## <a name="get-started"></a>はじめに

### <a name="red-hat-customers"></a>Red Hat のお客様

RHEL の Azure ハイブリッド特典は、次の両方の条件を満たす Red Hat のお客様が利用できます。

- Azure での使用に適した、アクティブまたは未使用の RHEL サブスクリプションがある
- [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) プログラムを使用して、Azure で使用する 1 つ以上のサブスクリプションを有効にしている

Red Hat の特典の使用を開始するには:

1. [Red Hat Cloud Access のカスタマー インターフェイス](https://access.redhat.com/management/cloud)を使用して、対象となる 1 つ以上の RHEL サブスクリプションを Azure で使用できるようにします。

   Red Hat Cloud Access の有効化プロセス中に指定した Azure サブスクリプションが、Azure ハイブリッド特典機能を使用できるようになります。
1. Azure ハイブリッド特典を、既存の RHEL PAYG VM のいずれか、および Azure Marketplace PAYG イメージからデプロイする任意の新しい RHEL VM に適用します。
1. RHEL VM の更新ソースの構成、および RHEL サブスクリプションのコンプライアンス ガイドラインについては、[次の推奨される手順](https://access.redhat.com/articles/5419341)に従ってください。


### <a name="suse-customers"></a>SUSE のお客様

SUSE の特典の使用を開始するには:

1. SUSE Public Cloud プログラムに登録します。
1. Azure CLI を介して、既存の VM に特典を適用します。
1. 特典を受けた VM を別の更新ソースに登録します。


## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Azure CLI で特典を有効または無効にする

`az vm update` コマンドを使用して、既存の VM を更新します。 RHEL VM の場合は、`--license-type` パラメーターとして `RHEL_BYOS` を指定してコマンドを実行します。 SLES VM の場合は、`--license-type` パラメーターとして `SLES_BYOS` を指定してコマンドを実行します。

### <a name="cli-example-to-enable-the-benefit"></a>特典を有効にする CLI の例
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>特典を無効にする CLI の例
特典を無効にするには、`--license-type` の値に `None` を使用します。

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>多数の VM で特典を有効にする CLI の例
多数の VM で特典を有効にするには、Azure CLI で `--ids` パラメーターを使用できます。

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

次の例は、リソース ID の一覧を取得する 2 つの方法を示しています。1 つはリソース グループ レベルで、もう 1 つはサブスクリプション レベルです。

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>VM の作成時に Azure ハイブリッド特典を適用する
既存の従量課金制 VM に Azure ハイブリッド特典を適用するだけでなく、VM の作成時にそれを呼び出すことができます。 これを行うことにより利点は 3 倍になります。
- 同じイメージおよびプロセスを使用することで、PAYG と BYOS の両方の VM をプロビジョニングできます。
- これにより、将来のライセンス モードの変更や、BYOS のみのイメージまたは独自の VM を使用する場合に利用できないものが有効になります。
- VM は、既定で Red Hat Update Infrastructure (RHUI) に接続されているので、最新の状態に保たれ安全になっています。 デプロイ後、更新したメカニズムはいつでも変更できます。

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>VM の Azure ハイブリッド特典の状態を確認する
VM の Azure ハイブリッド特典の状態を表示するには、Azure CLI を使用するか、Azure Instance Metadata Service を使用します。

### <a name="azure-cli"></a>Azure CLI

この目的には、`az vm get-instance-view` コマンドを使用できます。 応答の中から `licenseType` フィールドを探します。 `licenseType` フィールドが存在し、値が `RHEL_BYOS` または `SLES_BYOS`の場合、VM で特典が有効になっています。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 自体の中から、Azure Instance Metadata Service の構成証明済みメタデータに対してクエリを実行し、VM の `licenseType` 値を決定できます。 `RHEL_BYOS` または `SLES_BYOS` の `licenseType` 値は、VM の特典が有効になっていることを示します。 [構成証明済みメタデータの詳細についてはこちらをご覧ください](./instance-metadata-service.md#attested-data)。

## <a name="compliance"></a>コンプライアンス

### <a name="red-hat"></a>Red Hat

RHEL 向けの Azure ハイブリッド特典を使用しているお客様は、Azure Marketplace RHEL オファリングに関連付けられている標準の[法律条項](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf)および[プライバシーに関する声明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)に同意します。

RHEL 向けの Azure ハイブリッド特典を使用しているお客様には、これらの VM にソフトウェア更新プログラムとパッチを提供するための 3 つのオプションがあります。

- [Red Hat Update Infrastructure](../workloads/redhat/redhat-rhui.md) (既定のオプション)
- Red Hat Satellite Server
- Red Hat Subscription Manager

RHUI オプションを選択したお客様は、RHEL サブスクリプションを VM にアタッチしなくても、Azure ハイブリッド特典 RHEL VM の主要な更新ソースとして引き続き RHUI を使用できます。 RHUI オプションを選択したお客様は、RHEL サブスクリプションのコンプライアンスを保証する責任があります。

Red Hat Satellite Server または Red Hat Subscription Manager のいずれかを選択したお客様は、RHUI 構成を削除してから、Cloud Access が有効になっている RHEL サブスクリプションを自身の Azure ハイブリッド特典 RHEL VM にアタッチする必要があります。  

Red Hat サブスクリプションのコンプライアンス、ソフトウェア更新プログラム、Azure ハイブリッド特典 RHEL VM のソースの詳細については、[Azure ハイブリッド特典での RHEL サブスクリプションの使用に関する Red Hat の記事](https://access.redhat.com/articles/5419341)をご覧ください。

### <a name="suse"></a>SUSE

SLES VM で Azure ハイブリッド特典を使用するには、まず [SUSE Public Cloud プログラム](https://www.suse.com/media/guide/suse_public_cloud_service_provider_program_overview.pdf)に登録する必要があります。 SUSE サブスクリプションを購入した後は、これらのサブスクリプションを使用する VM を、自身の更新プログラムのソースに登録する必要があります。 この登録には、SUSE Customer Center、Subscription Management Tool サーバー、または SUSE Manager を使用します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
*Q:`RHEL_BYOS` の種類のライセンスを SLES イメージと共に使用する、またはその逆は可能ですか。*

A:いいえ、できません。 VM で実行しているディストリビューションと一致しない種類のライセンスを入力しようとすると、課金メタデータは更新されません。 ただし、誤って間違った種類のライセンスを入力した場合でも、正しい種類のライセンスになるように VM を再び更新すると、その特典が有効になります。

*Q:Red Hat Cloud Access に登録しましたが、RHEL VM で特典を有効にすることができません。どうすればよいですか。*

A:Red Hat Cloud Access サブスクリプションの登録が、Red Hat から Azure に反映されるまでに時間がかかる場合があります。 1 営業日後も引き続きエラーが表示される場合は、Microsoft サポートにお問い合わせください。

*Q:RHEL BYOS の "ゴールデン イメージ" を使用して VM をデプロイしました。これらのイメージの課金を BYOS から PAYG に変換することはできますか。*

A:いいえ、できません。 Azure ハイブリッド特典では、従量課金制のイメージでの変換のみがサポートされています。

*Q:オンプレミス (Azure Migrate、Azure Site Recovery、またはそれ以外の方法で) から Azure に独自の RHEL イメージをアップロードしました。これらのイメージの課金を BYOS から PAYG に変換することはできますか。*

A:いいえ、できません。 Azure ハイブリッド特典機能は、現在、Azure Marketplace の RHEL および SLES イメージでのみ使用できます。 

*Q:オンプレミス (Azure Migrate、Azure Site Recovery、またはそれ以外の方法で) から Azure に独自の RHEL イメージをアップロードしました。Azure ハイブリッド特典の特典を得るために何かを行う必要がありますか。*

A:いいえ、必要ありません。 アップロードした RHEL イメージは既に BYOS と見なされており、Azure インフラストラクチャのコストに対してのみ課金されます。 オンプレミス環境の場合と同じように、RHEL サブスクリプションのコストについて責任を負います。 

*Q:Azure Marketplace の RHEL および SLES の SAP イメージからデプロイされた VM で Azure ハイブリッド特典を使用できますか。*

A: はい、できます。 RHEL VM にはライセンスの種類 `RHEL_BYOS` を使用できます。Azure Marketplace の RHEL および SLES の SAP イメージからデプロイされた VM の変換には、`SLES_BYOS` を使用できます。

*Q:RHEL および SLES の仮想マシン スケール セットで Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 現時点では、仮想マシン スケール セットは RHEL および SLES の Azure ハイブリッド特典の範囲内にありません。

*Q:RHEL および SLES の予約インスタンスで Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 現時点では、予約インスタンスは RHEL および SLES の Azure ハイブリッド特典の範囲内にありません。

*Q:RHEL イメージの SQL Server 用にデプロイされた仮想マシンで Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 これらのサポートは計画されていません。
 

## <a name="common-problems"></a>一般的な問題
このセクションでは、発生する可能性のある一般的な問題と、軽減策の手順を示します。

| エラー | 対応策 |
| ----- | ---------- |
| "The action could not be completed because our records show that you have not successfully enabled Red Hat Cloud Access on your Azure subscription…."\(お使いの Azure サブスクリプションで Red Hat Cloud Access が正常に有効化されていないと記録されているため、この操作を完了できませんでした……\) | RHEL VM で特典を使用するには、まず [Azure サブスクリプションを Red Hat Cloud Access に登録する](https://access.redhat.com/management/cloud)必要があります。

## <a name="next-steps"></a>次の手順
* [Azure CLI を使用して、VM を作成および更新し、Azure ハイブリッド特典のライセンスの種類 (RHEL_BYOS、SLES_BYOS) を追加する方法を確認する](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)
