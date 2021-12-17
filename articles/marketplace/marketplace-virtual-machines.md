---
title: 仮想マシンのオファーを計画する - Microsoft コマーシャル マーケットプレース
description: この記事では、仮想マシンのオファーを Azure Marketplace に発行するための要件について説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/26/2021
ms.openlocfilehash: 56b2ce9b8b392a25c50ff4ea2d47da0237363a53
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719246"
---
# <a name="plan-a-virtual-machine-offer"></a>仮想マシン オファーの計画

この記事では、仮想マシン (VM) のオファーをコマーシャル マーケットプレースに発行するためのさまざまなオプションと要件について説明します。 VM オファーは、Azure Marketplace を通じてデプロイおよび課金される取引可能なオファーです。

開始する前に、[パートナー センターでコマーシャル マーケットプレース アカウントを作成](create-account.md)し、アカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

> [!TIP]
> コマーシャル マーケットプレースでの購入の顧客のビューを表示するには、「[Azure Marketplace での購入](/marketplace/azure-purchasing-invoicing)」を参照してください。

### <a name="technical-fundamentals"></a>技術的基礎

オファーの設計、作成、テストのプロセスには時間がかかり、Azure プラットフォームと、オファーの作成に使用される技術の両方に関する専門知識が必要です。 エンジニアリング チームは、[Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)、[Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)、[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking#networking)に関する実用的な知識を持ち、[Azure アプリケーションの設計とアーキテクチャ](https://azure.microsoft.com/solutions/architecture/)に習熟している必要があります。 次の追加のテクニカル リソースを参照してください。

- チュートリアル
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- サンプル
  - [Linux VM 用 Azure CLI サンプル](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Linux VM 用 Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Windows VM 用 Azure CLI サンプル](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Windows VM 用 Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>技術的な要件

VM オファーには、次の技術的な要件があります。

- 1 つのオペレーティング システム仮想ハード ディスク (VHD) を準備する必要があります。 データ ディスク VHD はオプションです。 これについては、以下で詳しく説明します。
- 顧客は、いつでもオファーをキャンセルできます。
- オファーに対して少なくとも 1 つのプランを作成する必要があります。 プランの価格は、選択した[ライセンス オプション](#licensing-options)に基づいて決まります。
   > [!IMPORTANT]
   > プラン内のどの VM イメージにも、同じ数のデータ ディスクが存在する必要があります。

VM には、次の 2 つのコンポーネントが含まれています。

- **オペレーティング VHD** - オファーでデプロイされるオペレーティング システムとソリューションが含まれています。 VHD の準備プロセスは、それが Linux、Windows、またはカスタムのいずれをベースにした VM であるかによって異なります。
- **データ ディスク VHD** (オプション) – VM 専用の永続的ストレージ。 永続的な情報の格納には、オペレーティング システム VHD (C: ドライブなど) を使用しないでください。 
    - 最大 16 個のデータ ディスクを含めることができます。
    - ディスクが空の場合でも、データ ディスクごとに 1 つの VHD を使用してください。

    > [!NOTE]
    > 使用するオペレーティング システムに関係なく、ソリューションに必要な最小数のデータ ディスクのみを追加します。 お客様はデプロイ時にイメージの一部であるディスクを削除することはできませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。

技術資産を準備する手順の詳細については、[承認済みのベースを使用した Azure 仮想マシンの作成](azure-vm-use-approved-base.md)または[独自のイメージを使用した仮想マシンの作成](azure-vm-use-own-image.md)に関するページを参照してください。

## <a name="preview-audience"></a>プレビュー対象ユーザー

[!INCLUDE [Test drives section](includes/preview-audience.md)]

## <a name="plans-and-pricing"></a>プランと価格

VM オファーには、1 つ以上のプランが必要です。 プランでは、ソリューションのスコープと制限、および関連する価格を定義します。 オファーに対して複数のプランを作成し、顧客にさまざまな技術やライセンスのオプション、および無料試用版を提供することができます。 価格モデル、無料試用版、プライベート プランなど、プランに関する一般的なガイダンスについては、「[コマーシャル マーケットプレース オファーのプランと価格](plans-pricing.md)」を参照してください。 

VM は商取引が可能で、従量課金制またはライセンス持ち込み (BYOL) のライセンス モデルを使用します。 商用トランザクションは Microsoft がホストし、公開元に代わって顧客に請求します。 公開元にとっては、顧客と Microsoft との間で推奨される支払い関係 (Enterprise Agreement など) を利用できる利点があります。 詳細については、「[コマーシャル マーケットプレースの販売機能](./marketplace-commercial-transaction-capabilities-and-considerations.md)」を参照してください。

> [!NOTE]
> Enterprise Agreement に関連付けられている Azure 前払い (旧称: 年額コミットメント) は、VM の Azure 利用には使えますが、公開元のソフトウェア ライセンス料金には使えません。

### <a name="licensing-options"></a>ライセンス オプション

新しいオファーを公開する準備を行う際、選択するライセンス オプションを決定する必要があります。 これにより、後でパートナー センターでオファーを作成する際に提供する必要がある追加情報が決まります。

VM オファーで使用可能なライセンス オプションは次のとおりです。

| ライセンス オプション | トランザクション プロセス |
| --- | --- |
| 無料試用版 | 1、3、または 6 か月の無料試用版を顧客に提供します。 |
| 体験版 | このオプションを使用すると、顧客は追加コストなしで VM を評価できます。 試用版を体験するには、既存の Azure 顧客である必要はありません。 詳細については、「[体験版とは](./what-is-test-drive.md)」を参照してください。 |
| BYOL | ライセンス持ち込みオプションを使用すると、顧客は既存のソフトウェア ライセンスを Azure に持ち込むことができます。\* |
| 使用量ベース | 従量課金制とも呼ばれるこのオプションを使用すると、顧客は 1 時間ごとの支払いが可能になります。 |
| 対話型デモ  | 対話型デモを使って、ソリューションのガイド付きエクスペリエンスを顧客に提供します。 そのベネフィットは、複雑なソリューションの複雑なセットアップなしで試用エクスペリエンスを提供できることです。 |
|

\* 公開元は、ソフトウェア ライセンス トランザクションのすべての側面 (注文、フルフィルメント、使用状況測定、課金、請求、支払い、収集を含みますが、これらに限定されません) をサポートします。

次の例は、使用量ベースの価格が設定された Azure Marketplace の VM オファーを示しています。

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="サンプル VM オファーの画面。":::

### <a name="private-plans"></a>プライベート プラン

イメージと価格をプライベート プランとして発行することで、VM の検出とデプロイを特定の顧客に限定できます。 プライベート プランとすることで、公開元が近しい顧客に排他的なオファーを作成し、カスタマイズされたソフトウェアと条件を提供できるようになります。 条件をカスタマイズすることによって､公開元は特別な価格と条件によるフィールド主導の取引､さらには限定的なソフトウェア リリースの利用などのさまざまなシナリオにスポットライトを当てることができます。 プライベート プランにより、限られた顧客に具体的な価格や製品を提供できます。

詳細については、「[コマーシャル マーケットプレース オファーのプランと価格](plans-pricing.md)」および「[Microsoft コマーシャル マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

## <a name="test-drive"></a>体験版

[!INCLUDE [Test drives section](includes/test-drives.md)]

今すぐ VM オファーの体験版について詳しく知りたい場合は、「[VM 体験版の構成](azure-vm-test-drive.md)」を参照してください。

## <a name="customer-leads"></a>潜在顧客

パートナー センターを使用してオファーをコマーシャル マーケットプレースに発行する場合、それを顧客関係管理 (CRM) システムに接続します。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 体験版を有効にする場合 (前のセクションを参照してください)、CRM への接続は必須です。 そうでない場合は、CRM への接続は任意です。

## <a name="legal-contracts"></a>法的契約

[!INCLUDE [Legal contracts section](includes/legal-contracts-intro.md)]
[!INCLUDE [Legal section for Standard contracts](includes/legal-contracts-standard.md)]

## <a name="cloud-solution-providers"></a>クラウド ソリューション プロバイダー

パートナー センターでオファーを作成するときに、 **[CSP を通じた再販]** タブが表示されます。このオプションを使用すると、Microsoft クラウド ソリューション プロバイダー (CSP) プログラムに参加しているパートナーは、バンドルされたオファーの一部として VM を再販できます。 すべてのライセンス持ち込み (BYOL) プランは、自動的にこのプログラムにオプトインされます。 BYOL 以外のプランをオプトインすることもできます。 詳細については、「[クラウド ソリューション プロバイダー プログラム](cloud-solution-providers.md)」を参照してください。 

> [!NOTE]
> クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。 Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[**クラウド ソリューション プロバイダー**](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Marketplace で仮想マシン オファーを作成する](azure-vm-offer-setup.md)
- [承認済みのベースを使用した Azure 仮想マシンの作成](azure-vm-use-approved-base.md)または[独自のイメージを使用した仮想マシンの作成](azure-vm-use-own-image.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
