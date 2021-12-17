---
title: 仮想マシンの Azure Confidential Computing
description: 仮想マシンの Azure Confidential Computing ソリューションについて説明します。
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1805b153d33915c2ec252c0e4a932eceb710b59f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331901"
---
# <a name="solutions-on-azure-for-intel-sgx"></a>Intel SGX 向けの Azure でのソリューション

この記事では、Intel SGX VM の展開に関する情報について説明します。

### <a name="current-available-sizes-and-regions"></a>現在利用できるサイズとリージョン

使用可能なリージョンと可用性ゾーンの Intel SGX VM サイズの一覧を取得するには、[Azure CLI](/cli/azure/install-azure-cli-windows) で次のコマンドを実行します。

```azurecli-interactive
az vm list-skus `
    --size Standard_DC `
    --all `
    --output table
```

### <a name="dedicated-host-requirements"></a>専用ホストの要件

**Standard_DC8_v2**、**Standard_DC48s_v3**、**Standard_DC48ds_v3** 仮想マシンを展開すると、ホストが完全に占有され、他のテナントやサブスクリプションとは共有されません。 この VM SKU ファミリでは、専用ホスト サービスを使用することによって通常満たされるコンプライアンスとセキュリティの規制要件を満たすために必要な分離が提供されます。 これらのサイズを選択すると、物理ホスト サーバーによって、EPC メモリを含むすべての使用可能なハードウェア リソースが、ユーザーの仮想マシンだけに割り当てられます。 このデプロイは、他の Azure VM ファミリによって提供される [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) サービスと同じものではありません。


## <a name="deployment-considerations"></a>デプロイに関する考慮事項

クイックスタート チュートリアルでは、10 分未満で DCsv2-Series 仮想マシンをデプロイできます。 

- **Azure サブスクリプション** - Confidential Computing VM インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)をお使いの場合、適切な量の Azure コンピューティング コアに対するクォータが与えられません。

- **価格とリージョン別の可用性** - DCsv2、DCsv3 および DCdsv3 VM の価格は [仮想マシンの価格ページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)で確認できます。 各 Azure リージョンで利用できるかどうかについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 」を参照してください。

- **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、DCsv2-Series を含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-portal/supportability/per-vm-quota-requests.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。

  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  
- **サイズ変更** - 特殊なハードウェアが使用されるため、これらのインスタンスのサイズ変更は同じサイズ ファミリ内でのみ可能です。 たとえば、DCsv2-Series の VM は DCsv2-Series のあるサイズから別のサイズにのみ変更できます。 

- **イメージ** - Confidential Computing インスタンスで Intel Software Guard Extension (Intel SGX) をサポートするために、すべてのデプロイを Generation 2 イメージで実行する必要があります。 Azure Confidential Computing は、Ubuntu 20.04 Gen 2、Ubuntu 18.04 Gen 2、Windows Server 2019 Gen 2 で実行されるワークロードをサポートします。 サポートされているシナリオとサポートされていないシナリオの詳細については、「[Azure での第 2 世代 VM のサポート](../virtual-machines/generation-2.md)」をお読みください。 

- **Storage** - DCsv2 シリーズでは、DC8_v2 を除いて、Standard SSD と Premium SSD がサポートされます。 DCsv3 シリーズと DCdsv3 シリーズでは、Standard SSD、Premium SSD、および Ultra Disk がサポートされます。

- **ディスク暗号化** - Confidential Computing インスタンスは現時点でディスク暗号化に対応していません。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性とディザスター リカバリーに関する考慮

Azure で仮想マシンを使用する場合、いかなるダウンタイムも回避するために高可用性とディザスター リカバリー ソリューションを実装する責任があります。 

Azure Confidential Computing では現時点で、可用性ゾーンによるゾーン冗長がサポートされていません。 Confidential Computing で可用性と冗長性を最大にするには、[可用性セット](../virtual-machines/availability-set-overview.md)を使用します。 ハードウェアに制約があるため、Confidential Computing インスタンスの可用性セットに指定できる更新ドメインは最大で 10 です。 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートを使用したデプロイ

Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 Azure サブスクリプション内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 アクセス制御、ロック、タグなどの管理機能を使用して、デプロイ後にリソースを保護および整理できます。

ARM テンプレートの詳細については、[Template deployment の概要](../azure-resource-manager/templates/overview.md)に関するページを参照してください。

ARM テンプレートを使用して展開するには、[仮想マシン リソース](../virtual-machines/windows/template-description.md)を活用します。 **vmSize** と **imageReference** に確実に正しいプロパティを指定します。

### <a name="vm-size"></a>VM サイズ

仮想マシン リソースの ARM テンプレートで次のサイズのいずれかを指定します。 この文字列は **[プロパティ]** に **vmSize** として入力されます。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2",
        "Standard_DC1s_v3",
        "Standard_DC2s_v3",
        "Standard_DC4s_v3",
        "Standard_DC8s_v3",
        "Standard_DC16s_v3",
        "Standard_DC24s_v3",
        "Standard_DC32s_v3",
        "Standard_DC48s_v3",
        "Standard_DC1ds_v3",
        "Standard_DC2ds_v3",
        "Standard_DC4ds_v3",
        "Standard_DC8ds_v3",
        "Standard_DC16ds_v3",
        "Standard_DC24ds_v3",
        "Standard_DC32ds_v3",
        "Standard_DC48ds_v3",
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS イメージ

**[プロパティ]** の下では、 **[storageProfile]** 下のイメージも参照する必要があります。 **imageReference** には、次のイメージのうち "*1 つだけ*" を使用します。

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>次のステップ 

この記事では、Intel SGX VM の作成時に必要な資格と構成について学習しました。

> [!div class="nextstepaction"]
> [Azure Marketplace で Intel SGX VM を作成する](quick-create-marketplace.md)
