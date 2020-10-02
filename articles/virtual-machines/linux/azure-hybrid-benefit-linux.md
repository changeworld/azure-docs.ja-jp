---
title: Azure ハイブリッド特典と Linux VM
description: Azure ハイブリッド特典を使用すると、Azure で実行されている Linux 仮想マシンのコストを削減できます。
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: westonh
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: a9ea3b21908d1f461745ffe2689b36f8efcb6f89
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993217"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure ハイブリッド特典 - Linux Virtual Machines に適用する方法

## <a name="overview"></a>概要

Azure ハイブリッド特典を使用すると、既存の Red Hat または SUSE ソフトウェア サブスクリプションを使用して、オンプレミスの Red Hat Enterprise Linux (RHEL) サーバーと SUSE Linux Enterprise (SLES) サーバーを Azure に簡単に移行できます。 この特典により、ソフトウェア料金は RHEL または SLES サブスクリプションによってカバーされるため、お客様には VM のインフラストラクチャ コストのみが課金されます。 この特典は、すべての RHEL および SLES Marketplace の従量課金制 (PAYG) イメージに適用されます。

## <a name="benefit-description"></a>特典の説明

Azure ハイブリッド特典を通じて、Azure 上の既存の RHEL および SLES の PAYG VM を、サブスクリプション持ち込み (BYOS) 課金に変換することで、オンプレミスの RHEL および SLES サーバーを Azure に簡単に移行できます。 通常、PAYG イメージからデプロイされた Azure 上の VM の場合、インフラストラクチャの料金とソフトウェアの料金の両方が請求されます。 Azure ハイブリッド特典を使用すると、PAYG VM を再デプロイせずに BYOS 課金モデルに変換できるため、ダウンタイムのリスクを回避できます。

![Linux VM での Azure ハイブリッド特典コストの視覚化](./media/ahb-linux/azure-hybrid-benefit-cost.png)

RHEL または SLES VM で特典を有効にすると、PAYG VM で通常発生する追加のソフトウェア料金に対する課金はなくなります。 代わりに、VM は BYOS の課金を開始します。これにはコンピューティング ハードウェア料金のみが含まれ、ソフトウェア料金は含まれません。

希望する場合は、特典が有効になっている VM を PAYG 課金モデルに戻すこともできます。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux VM の Azure ハイブリッド特典の対象範囲

Azure ハイブリッド特典は、すべての RHEL および SLES Marketplace の PAYG イメージで使用できます。 この特典は、現時点では RHEL または SLES Marketplace の BYOS イメージまたはカスタム イメージでは使用できません。

Linux VM で予約インスタンス、専用ホスト、および SQL ハイブリッド特典を既に使用している場合は、Azure ハイブリッド特典の対象になりません。

## <a name="how-to-get-started"></a>ファースト ステップ

Azure ハイブリッド特典は現在、Linux VM 向けのプレビュー段階にあります。 プレビューにアクセスできるようになったら、Azure portal または Azure CLI を使用して特典を有効にすることができます。

### <a name="preview"></a>[プレビュー]

このフェーズでは、[こちら](https://aka.ms/ahb-linux-form)からフォームに入力することによって、特典へのアクセスが得られます。 フォームに入力すると、3 営業日以内に Azure サブスクリプションが特典に対して有効になり、Microsoft から確認メッセージを受け取ります。

### <a name="red-hat-customers"></a>Red Hat のお客様

1.    上のプレビュー要求フォームに入力します
1.    [Red Hat Cloud Access プログラム](https://aka.ms/rhel-cloud-access)に登録します
1.    Cloud Access に対して Azure サブスクリプションを有効にし、特典を使用する VM が含まれているサブスクリプションを有効にします
1.    Azure portal または Azure CLI を使用して、既存の VM に特典を適用します
1.    特典を受けた VM を別の更新ソースに登録します

### <a name="suse-customers"></a>SUSE のお客様

1.    上のプレビュー要求フォームに入力します
1.    SUSE Public Cloud プログラムに登録します
1.    Azure portal または Azure CLI を使用して、既存の VM に特典を適用します
1.    特典を受けた VM を別の更新ソースに登録します

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Azure portal で特典を有効または無効にする

既存の VM に対する特典を有効にするには、 **[構成]** ブレードにアクセスし、そこに記載されている手順に従ってください。 VM の作成エクスペリエンス中に、新しい VM の特典を有効にすることもできます。

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
VM の AHB の状態は、ポータルでの確認、Azure CLI の使用、Azure Instance Metadata Service (Azure IMDS) の使用の 3 つの方法で表示できます。


### <a name="portal"></a>ポータル

[構成] ブレードを表示し、[ライセンスの状態] をチェックして AHB が VM で有効になっているかどうかを確認します。

### <a name="azure-cli"></a>Azure CLI

この目的には、`az vm get-instance-view` コマンドを使用できます。 応答の中から licenseType フィールドを探します。 LicenseType フィールドが存在し、値が 'RHEL_BYOS' または 'SLES_BYOS' である場合は、VM で特典が有効になっています。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

VM 自体から、IMDS 構成証明済みメタデータに対してクエリを実行し、VM の licenseType を特定することができます。 LicenseType 値が 'RHEL_BYOS' または 'SLES_BYOS' の場合、VM の特典が有効になっていることを示します。 構成証明済みメタデータの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#attested-data)を参照してください。

## <a name="compliance"></a>コンプライアンス

### <a name="red-hat"></a>Red Hat

RHEL VM で Azure ハイブリッド特典を使用するには、まず Red Hat Cloud Access プログラムに登録する必要があります。 この操作は、こちらの Red Hat Cloud Access のサイトで行うことができます。 VM で特典を有効にしたら、Red Hat Subscription Manager または Red Hat Satellite を使用して、VM を自身の更新プログラムのソースに登録する必要があります。 更新のための登録を行うことで、サポートされている状態を維持できます。

### <a name="suse"></a>SUSE

SLES VM で Azure ハイブリッド特典を使用するには、まず SUSE Public Cloud プログラムに登録する必要があります。 プログラムの詳細については、こちらを参照してください。 SUSE サブスクリプションを購入した後は、SUSE Customer Center、Subscription Management Tool サーバー、または SUSE Manager を使用して、購入したサブスクリプションを使用して VM を自身の更新プログラムのソースに登録する必要があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
*Q:"RHEL_BYOS" の種類のライセンスを SLES イメージと共に使用する、またはその逆は可能ですか。*

A:いいえ、できません。 VM で実行しているディストリビューションと一致しない種類のライセンスを入力しようとすると、課金メタデータは更新されません。 ただし、誤って間違った種類のライセンスを入力した場合でも、正しい種類のライセンスになるように VM を再び更新すると、その特典が有効になります。

*Q:Red Hat Cloud Access に登録しましたが、RHEL VM で特典を有効にすることができません。どうすればよいですか。*

A:Red Hat Cloud Access サブスクリプションの登録が、Red Hat から Azure に反映されるまでに時間がかかる場合があります。 1 営業日後も引き続きエラーが表示される場合は、Microsoft サポートにお問い合わせください。

## <a name="common-errors"></a>一般的なエラー
このセクションには、一般的なエラーの一覧と対応策が含まれています。

| エラー | 対応策 |
| ----- | ---------- |
| "The subscription is not registered for the Linux preview of Azure Hybrid Benefit. For step-by-step instructions, refer to https://aka.ms/ahb-linux"\(サブスクリプションは、Azure ハイブリッド特典の Linux プレビュー用に登録されていません。詳細の手順については、リンク先を参照してください\) | https://aka.ms/ahb-linux-form でフォームに入力し、Azure ハイブリッド特典の Linux プレビューに登録します。
| "The action could not be completed because our records show that you have not successfully enabled Red Hat Cloud Access on your Azure subscription…."\(お使いの Azure サブスクリプションで Red Hat Cloud Access が正常に有効化されていないと記録されているため、この操作を完了できませんでした……\) | RHEL VM で特典を使用するには、まず Azure サブスクリプションを Red Hat Cloud Access に登録する必要があります。 Azure サブスクリプションを Red Hat Cloud Access に登録する方法の詳細については、こちらのリンクをご覧ください。

## <a name="next-steps"></a>次のステップ
* プレビューの使用を開始するには、[こちら](https://aka.ms/ahb-linux-form)のフォームに入力してください。
