---
title: Azure ハイブリッド特典と Linux VM
description: Azure ハイブリッド特典を使用すると、Azure で実行されている Linux 仮想マシンのコストを削減できます。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 1459c80c857a436c8369d3ebe89794e0b69c33b1
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980883"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure ハイブリッド特典 - Linux Virtual Machines に適用する方法

## <a name="overview"></a>概要

Azure ハイブリッド特典を使用すると、既存の Red Hat または SUSE ソフトウェア サブスクリプションを使用して、オンプレミスの Red Hat Enterprise Linux (RHEL) サーバーと SUSE Linux Enterprise Server (SLES) 仮想マシン (VM) を Azure に簡単に移行できます。 この特典により、ソフトウェア料金は RHEL または SLES サブスクリプションによってカバーされるため、お客様には VM のインフラストラクチャ コストのみが課金されます。 この特典は、すべての RHEL および SLES Marketplace の従量課金制 (PAYG) イメージに適用されます。

> [!IMPORTANT]
> Linux VM の Azure ハイブリッド特典は、一般提供されるようになりました


## <a name="benefit-description"></a>特典の説明

Azure ハイブリッド特典を通じて、Azure 上の既存の RHEL および SLES の PAYG VM を、サブスクリプション持ち込み (BYOS) 課金に変換することで、オンプレミスの RHEL および SLES サーバーを Azure に簡単に移行できます。 通常、PAYG イメージからデプロイされた Azure 上の VM の場合、インフラストラクチャの料金とソフトウェアの料金の両方が請求されます。 Azure ハイブリッド特典を使用すると、PAYG VM を再デプロイせずに BYOS 課金モデルに変換できるため、ダウンタイムのリスクを回避できます。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Linux VM での Azure ハイブリッド特典コストの視覚化。":::

RHEL または SLES VM で特典を有効にすると、PAYG VM で通常発生する追加のソフトウェア料金に対する課金はなくなります。 代わりに、VM は BYOS の課金を開始します。これにはコンピューティング ハードウェア料金のみが含まれ、ソフトウェア料金は含まれません。

希望する場合は、特典が有効になっている VM を PAYG 課金モデルに戻すこともできます。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux VM の Azure ハイブリッド特典の対象範囲

Azure ハイブリッド特典は、すべての RHEL および SLES Marketplace の PAYG イメージで使用できます。 この特典は、現時点では RHEL または SLES Marketplace の BYOS イメージまたはカスタム イメージでは使用できません。

Linux VM で予約インスタンス、専用ホスト、および SQL ハイブリッド特典を既に使用している場合は、Azure ハイブリッド特典の対象になりません。

## <a name="how-to-get-started"></a>ファースト ステップ

### <a name="red-hat-customers"></a>Red Hat のお客様

RHEL 向けの Azure ハイブリッド特典は、Azure で使用できるアクティブまたは未使用の RHEL サブスクリプションをお持ちで、[Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) プログラムでそれらのサブスクリプションの 1 つ以上を Azure で使用できるようにしているお客様が利用できます。 

1.  [Red Hat Cloud Access のカスタマー インターフェイス](https://access.redhat.com/management/cloud)を使用して、対象となる 1 つ以上の RHEL サブスクリプションを Azure で使用できるようにします。
1.  Red Hat Cloud Access 有効化プロセス中に指定した Azure サブスクリプションが、Azure ハイブリッド特典機能を使用できるようになります。
1.  Azure ハイブリッド特典を、既存の RHEL PAYG VM のいずれか、および Azure Marketplace PAYG イメージからデプロイする任意の新しい RHEL VM に適用します。
1.  RHEL VM の更新ソースの構成、および RHEL サブスクリプションのコンプライアンス ガイドラインについては、[次の推奨される手順](https://access.redhat.com/articles/5419341)に従ってください。


### <a name="suse-customers"></a>SUSE のお客様

1.    SUSE Public Cloud プログラムに登録します
1.    Azure CLI を使用して、既存の VM に特典を適用します
1.    特典を受けた VM を別の更新ソースに登録します


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Azure CLI で特典を有効または無効にする

'az vm update' コマンドを使用して、既存の VM を更新することができます。 RHEL VM の場合は、--license-type パラメーターとして "RHEL_BYOS" を指定してコマンドを実行します。 SLES VM の場合は、--license-type パラメーターとして "SLES_BYOS" を指定してコマンドを実行します。

#### <a name="cli-example-to-enable-the-benefit"></a>特典を有効にする CLI の例:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>特典を無効にする CLI の例:
特典を無効にするには、license-type の値に "None" を使用します。
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>多数の VM で特典を有効にする CLI の例
多数の VM で特典を有効にするには、Azure CLI で `--ids` パラメーターを使用できます。

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
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

## <a name="check-ahb-status-of-a-vm"></a>VM の AHB の状態を確認する
VM の AHB の状態を表示するには、Azure CLI を使用する方法、または Azure Instance Metadata Service (Azure IMDS) を使用する方法の 2 つがあります。


### <a name="azure-cli"></a>Azure CLI

この目的には、`az vm get-instance-view` コマンドを使用できます。 応答の中から licenseType フィールドを探します。 LicenseType フィールドが存在し、値が 'RHEL_BYOS' または 'SLES_BYOS' である場合は、VM で特典が有効になっています。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 自体から、IMDS 構成証明済みメタデータに対してクエリを実行し、VM の licenseType を特定することができます。 LicenseType 値が 'RHEL_BYOS' または 'SLES_BYOS' の場合、VM の特典が有効になっていることを示します。 構成証明済みメタデータの詳細については、[こちら](./instance-metadata-service.md#attested-data)を参照してください。

## <a name="compliance"></a>コンプライアンス

### <a name="red-hat"></a>Red Hat

RHEL 向けの Azure ハイブリッド特典を使用しているお客様は、Azure Marketplace RHEL オファリングに関連付けられている標準の[法律条項](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf)および[プライバシーに関する声明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf)に同意します。

RHEL 向けの Azure ハイブリッド特典を使用しているお客様には、これらの VM にソフトウェア更新プログラムとパッチを提供するための 3 つのオプションがあります。

1.  [Red Hat Update Infrastructure (RHUI)](../workloads/redhat/redhat-rhui.md) (既定のオプション)
1.  Red Hat Satellite Server
1.  Red Hat Subscription Manager

RHUI オプションを選択したお客様は、RHEL サブスクリプションを VM にアタッチしなくても、AHB RHEL VM の主要な更新ソースとして引き続き RHUI を使用できます。  RHUI オプションを選択したお客様は、RHEL サブスクリプションのコンプライアンスを保証する責任があります。

Red Hat Satellite Server または Red Hat Subscription Manager のいずれかを選択したお客様は、RHUI 構成を削除してから、Cloud Access が有効になっている RHEL サブスクリプションを自身の AHB RHEL VM にアタッチする必要があります。  

Red Hat サブスクリプションのコンプライアンス、ソフトウェア更新プログラム、AHB RHEL VM 用のソースの詳細については、[こちら](https://access.redhat.com/articles/5419341)を参照してください。

### <a name="suse"></a>SUSE

SLES VM で Azure ハイブリッド特典を使用するには、まず SUSE Public Cloud プログラムに登録する必要があります。 プログラムの詳細については、こちらを参照してください。 SUSE サブスクリプションを購入した後は、SUSE Customer Center、Subscription Management Tool サーバー、または SUSE Manager を使用して、購入したサブスクリプションを使用して VM を自身の更新プログラムのソースに登録する必要があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
*Q:"RHEL_BYOS" の種類のライセンスを SLES イメージと共に使用する、またはその逆は可能ですか。*

A:いいえ、できません。 VM で実行しているディストリビューションと一致しない種類のライセンスを入力しようとすると、課金メタデータは更新されません。 ただし、誤って間違った種類のライセンスを入力した場合でも、正しい種類のライセンスになるように VM を再び更新すると、その特典が有効になります。

*Q:Red Hat Cloud Access に登録しましたが、RHEL VM で特典を有効にすることができません。どうすればよいですか。*

A:Red Hat Cloud Access サブスクリプションの登録が、Red Hat から Azure に反映されるまでに時間がかかる場合があります。 1 営業日後も引き続きエラーが表示される場合は、Microsoft サポートにお問い合わせください。

*Q:RHEL BYOS の "ゴールデン イメージ" を使用して VM をデプロイしました。これらのイメージの課金を BYOS から PAYG に変換することはできますか。*

A:いいえ、できません。 Azure ハイブリッド特典では、従量課金制のイメージでの変換のみがサポートされています。

*Q:RHEL BYOS の "ゴールデン イメージ" を使用して VM をデプロイしました。これらのイメージの課金を BYOS から PAYG に変換することはできますか。*

A:いいえ、できません。 Azure ハイブリッド特典では、従量課金制のイメージでの変換のみがサポートされています。

*Q:オンプレミス (ASR 経由またはそれ以外) から Azure に独自の RHEL イメージをアップロードしました。これらのイメージの課金を BYOS から PAYG に変換することはできますか。*

A:いいえ、できません。 Azure ハイブリッド特典機能は、現在、RHEL および SLES の Marketplace イメージでのみ使用できます。 

*Q:Marketplace の RHEL および SLES の SAP イメージからデプロイされた VM で Azure ハイブリッド特典を使用できますか。*

A: はい、できます。 RHEL VM にはライセンスの種類 'RHEL_BYOS' を使用できます。Marketplace の RHEL および SLES の SAP イメージからデプロイされた VM の変換には、'SLES_BYOS' を使用できます。

*Q:RHEL および SLES の仮想マシン スケール セット (VMSS) で Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 現在、RHEL および SLES の VMSS は Azure ハイブリッド特典のスコープに含まれていません。

*Q:RHEL および SLES の予約インスタンス (RI) で Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 現在、RHEL および SLES の RI は Azure ハイブリッド特典のスコープに含まれていません。

*Q:RHEL イメージの SQL Server 用にデプロイされた仮想マシンで Azure ハイブリッド特典を使用できますか。*

A:いいえ、できません。 これらのサポートは計画されていません。
 

## <a name="common-issues"></a>一般的な問題
このセクションには、発生する可能性のある一般的な問題の一覧と、軽減策の手順が含まれています。

| エラー | 対応策 |
| ----- | ---------- |
| "The action could not be completed because our records show that you have not successfully enabled Red Hat Cloud Access on your Azure subscription…."\(お使いの Azure サブスクリプションで Red Hat Cloud Access が正常に有効化されていないと記録されているため、この操作を完了できませんでした……\) | RHEL VM で特典を使用するには、まず Azure サブスクリプションを Red Hat Cloud Access に登録する必要があります。 Azure サブスクリプションを Red Hat Cloud Access に登録する方法の詳細については、こちらのリンクをご覧ください。

## <a name="next-steps"></a>次のステップ
* Azure CLI を使用して、VM を作成および更新し、Azure ハイブリッド特典のライセンスの種類 (RHEL_BYOS、SLES_BYOS) を追加する方法については、[こちら](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)をご覧ください。
