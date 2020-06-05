---
title: 仮想マシンの Azure Confidential Computing
description: 仮想マシンの Azure Confidential Computing ソリューションについて説明します。
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 49b159434497d4b455a338ba88058d73d7de10ee
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773136"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 仮想マシンのソリューション

この記事では、[Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX) でバックアップされる Intel プロセッサを実行する Azure Confidential Computing 仮想マシン (VM) のデプロイに関する情報を取り上げます。 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure Confidential Computing の VM サイズ

Azure Confidential Computing 仮想マシンは、データとコードをクラウドで処理するとき、機密性と整合性を保護するように設計されています 

[DCsv2-Series](../virtual-machines/dcv2-series.md) VM は Confidential Computing の最新のサイズ ファミリです。 これらの VM では、広範囲のデプロイ機能がサポートされ、Enclave Page Cache (EPC) が 2 つ与えられ、サイズの取り揃えが DC-Series VM より多くなっています。 [DC-Series](../virtual-machines/sizes-previous-gen.md#preview-dc-series) VM は現在のところプレビュー段階です。これは非推奨となる予定で、一般提供には含まれません。

[クイックスタート チュートリアル](quick-create-marketplace.md)に従い、Microsoft コマーシャル マーケットプレースから DCsv2-Series VM のデプロイを開始します。

### <a name="current-available-sizes-and-regions"></a>現在利用できるサイズとリージョン

一般提供されている Confidential Computing VM サイズ、リージョン、可用性ゾーンをすべてまとめた一覧は [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) で次のコマンドを実行することで取得できます。

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

2020 年 5 月の時点で、これらの SKU は次のリージョンと可用性ゾーンで利用できます。

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

上記のサイズの詳細を表示するには、次のコマンドを実行します。

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

クイックスタート チュートリアルでは、10 分未満で DCsv2-Series 仮想マシンをデプロイできます。 

- **Azure サブスクリプション** - Confidential Computing VM インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)をお使いの場合、適切な量の Azure コンピューティング コアに対するクォータが与えられません。

- **価格とリージョン別の可用性** - DCsv2-Series VM の価格は[仮想マシンの価格ページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)で確認できます。 各 Azure リージョンで利用できるかどうかについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 」を参照してください。


- **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、DCsv2-Series を含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。

  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  
- **サイズ変更** - 特殊なハードウェアが使用されるため、Confidential Computing インスタンスのサイズ変更は同じサイズ ファミリ内でのみ可能です。 たとえば、DCsv2-Series の VM は DCsv2-Series のあるサイズから別のサイズにのみ変更できます。 Confidential Computing ではないサイズを Confidential Computing サイズに変更することはできません。  

- **イメージ** - Confidential Computing インスタンスで Intel Software Guard Extension (Intel SGX) をサポートするために、すべてのデプロイを Generation 2 イメージで実行する必要があります。 Azure Confidential Computing では、Ubuntu 18.04 Gen 2、Ubuntu 16.04 Gen 2、Windows Server 2019 Gen2、および Windows Server 2016 Gen 2 で実行されるワークロードがサポートされます。 サポートされているシナリオとサポートされていないシナリオの詳細については、「[Azure での第 2 世代 VM のサポート](../virtual-machines/linux/generation-2.md)」をお読みください。 

- **ストレージ** - Azure Confidential Computing 仮想マシンのデータ ディスクとエフェメラル OS ディスクは NVMe ディスク上にあります。 インスタンスでは、Premium SSD ディスクと Standard SSD ディスクのみがサポートされ、Ultra SSD と Standard HDD はサポートされません。 仮想マシン サイズ **DC8_v2** では Premium ストレージがサポートされていません。 

- **ディスク暗号化** - Confidential Computing インスタンスは現時点でディスク暗号化に対応していません。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性とディザスター リカバリーに関する考慮

Azure で仮想マシンを使用する場合、いかなるダウンタイムも回避するために高可用性とディザスター リカバリー ソリューションを実装する責任があります。 

Azure Confidential Computing では現時点で、可用性ゾーンによるゾーン冗長がサポートされていません。 Confidential Computing で可用性と冗長性を最大にするには、[可用性セット](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)を使用します。 ハードウェアに制約があるため、Confidential Computing インスタンスの可用性セットに指定できる更新ドメインは最大で 10 です。 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートによるデプロイ 

Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 Azure サブスクリプション内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 アクセス制御、ロック、タグなどの管理機能を使用して、デプロイ後にリソースを保護および整理できます。

Azure Resource Manager テンプレートについては、[テンプレートのデプロイの概要](../azure-resource-manager/templates/overview.md)に関するページを参照してください。

Azure Resource Manager テンプレートで DCsv2-Series VM をデプロイするには、[仮想マシン リソース](../virtual-machines/windows/template-description.md)を活用します。 **vmSize** と **imageReference** に確実に正しいプロパティを指定します。

### <a name="vm-size"></a>[VM サイズ]

仮想マシン リソースの Azure Resource Manager テンプレートで、次のいずれかのサイズを指定します。 この文字列は **[プロパティ]** に **vmSize** として入力されます。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
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
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>次の手順 

この記事では、Confidential Computing 仮想マシンの作成時に必要な資格と構成について学習しました。 これで Microsoft Azure Marketplace に進み、DCsv2-Series VM をデプロイできます。

> [!div class="nextstepaction"]
> [Azure Marketplace で DCsv2-Series 仮想マシンをデプロイする](quick-create-marketplace.md)