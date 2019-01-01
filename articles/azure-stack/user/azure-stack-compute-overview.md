---
title: Azure Stack 仮想マシンの概要
description: Azure Stack 仮想マシンについての詳細
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: a0d75b13369cff4e99bef6f57a3b01f3d8eee6e4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227165"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Azure Stack 仮想マシンの概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack は、オンデマンドでスケーラブルなコンピューティング リソースの一種として仮想マシン (VM) を提供します。 コンピューティング環境を他の手段より細かく管理する必要がある場合に、VM を選択できます。 VM を作成する前に、この記事で詳細を説明します。

Azure Stack VM により柔軟な仮想化が可能になります。クラスターや個別のコンピューターを管理する必要はありません。 ただし、VM のメンテナンス、つまり VM 上で動作するソフトウェアの構成、その修正プログラムの適用、インストールは必要です。

Azure Stack 仮想マシンは、さまざまな方法で利用できます。 例: 

- **開発とテスト**  
    Azure Stack VM は、アプリケーションのコーディングとテストに必要な特定の構成でコンピューターをすばやく簡単に作成する手段を提供します。

- **クラウドのアプリケーション**  
    アプリケーションの需要は変動する可能性があるため、Azure Stack 内の VM でアプリケーションを実行することは経済的に理に適っています。 VM が必要になったら追加分の料金を支払い、不要になったらシャットダウンすることができます。

- **データセンターの拡張**  
    Azure Stack 仮想ネットワーク内の仮想マシンは、組織のネットワークや Azure に簡単に接続できます。

アプリケーションで使用する VM は、ニーズに応じてスケールアップまたはスケールアウトできます。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM の作成前に検討する必要のある事項

Azure Stack でアプリケーション インフラストラクチャを構築する際には、多数の設計上の考慮事項が必ず存在します。 インフラストラクチャの作成を開始する前に、VM の次の側面を考慮することが重要です。

- アプリケーション リソースの名前。
- VM のサイズ。
- 作成できる VM の最大数。
- VM で実行されるオペレーティング システム。
- 開始した後の VM の構成。
- VM で必要な関連リソース。

### <a name="naming"></a>名前を付ける

仮想マシンには名前が割り当てられ、コンピューター名がオペレーティング システムの一部として構成されます。 VM の名前は最大で 15 文字です。

Azure Stack を使用してオペレーティング システム ディスクを作成する場合、コンピューター名と仮想マシン名は同じになります。 以前構成されたオペレーティング システムが含まれる独自のイメージをアップロードして使用し、それを基に仮想マシンを作成する場合は、別の名前にすることができます。 独自のイメージ ファイルをアップロードするときは、ベスト プラクティスとして、オペレーティング システムのコンピューター名と仮想マシン名を同じにします。

### <a name="vm-size"></a>VM サイズ

使用する VM のサイズは、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure Stack では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

### <a name="vm-limits"></a>VM の制限

サブスクリプションにはそれぞれ既定のクォータ制限が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。

### <a name="operating-system-disks-and-images"></a>オペレーティング システム ディスクおよびイメージ

仮想マシンは、仮想ハード ディスク (VHD) を使用して、オペレーティング システム (OS) およびデータを保存します。 VHD は、OS をインストールするために選択できるイメージの保存にも使用できます。
Azure Stack には、オペレーティング システムのさまざまなバージョンと種類で使用できるマーケットプレースが用意されています。 Marketplace イメージは、イメージの発行元、プラン、SKU、およびバージョン (通常は最新バージョンとして指定) によって識別されます。

次の表に、イメージに関する情報を見つける方法をいくつか示します。

|方法|説明|
|---------|---------|
|Azure Stack ポータル|値は、使用するイメージを選択する際に自動的に指定されます。|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API     |[イメージ発行元の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[イメージ プランの一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[イメージ SKU の一覧表示](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

独自のイメージをアップロードして使用することもできます。 その場合、発行元の名前、プラン、および SKU は使用されません。

### <a name="extensions"></a>拡張機能

VM の拡張機能は、デプロイ後の構成と自動化タスクを通じて VM に追加の機能を提供します。
拡張機能を使用して、次のような一般的なタスクを実行できます。

- **カスタム スクリプトの実行**  
    カスタム スクリプト拡張機能を利用すれば、VM のプロビジョニングの際にスクリプトを実行して、VM でワークロードを構成できます。

- **構成のデプロイと管理**  
    PowerShell Desired State Configuration (DSC) 拡張機能を利用すれば、VM で DSC をセットアップして構成と環境を管理できます。

- **診断データの収集**  
    Azure 診断拡張機能を利用すれば、アプリケーションの正常性を監視するために使用できる診断データを収集するように VM を構成できます。

### <a name="related-resources"></a>関連リソース

次の表のリソースは VM によって使用されるため、VM の作成時に存在するか、作成する必要があります。


|リソース|必須|説明|
|---------|---------|---------|
|リソース グループ|はい|VM は、リソース グループに含まれる必要があります。|
|ストレージ アカウント|いいえ |マネージド ディスクを使用する場合、仮想ハード ディスクを格納するためにストレージ アカウントは VM に必要ありません。 <br>アンマネージド ディスクを使用する場合、VM には仮想ハード ディスクを格納するためにストレージ アカウントが必要です。|
|仮想ネットワーク|はい|VM は、仮想ネットワークのメンバーである必要があります。|
|パブリック IP アドレス|いいえ |VM には、リモートでアクセスするためのパブリック IP アドレスを割り当てることができます。|
|Linux|はい|VM には、ネットワークで通信するためのネットワーク インターフェイスが必要です。|
|データ ディスク|いいえ |VM には、ストレージ容量を拡張するためのデータ ディスクを含めることができます。|

## <a name="create-your-first-vm"></a>最初の VM の作成

VM の作成方法にはいくつかの選択肢があります。 どれを選ぶかは環境によって異なります。
次の表は、VM の作成を開始するうえでの情報を提供します。


|方法|記事|
|---------|---------|
|Azure Stack ポータル|Azure Stack ポータルで Windows 仮想マシンを作成する<br>[Azure Stack ポータルで Linux 仮想マシンを作成する](azure-stack-quick-linux-portal.md)|
|テンプレート|Azure Stack のクイック スタート テンプレートは次の場所にあります。<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Azure Stack で PowerShell を使用して Windows 仮想マシンを作成する](azure-stack-quick-create-vm-windows-powershell.md)<br>[Azure Stack で PowerShell を使用して Linux 仮想マシンを作成する](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Azure Stack で CLI を使用して Windows 仮想マシンを作成する](azure-stack-quick-create-vm-windows-cli.md)<br>[Azure Stack で CLI を使用して Linux 仮想マシンを作成する](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>VM の管理

VM は、ブラウザーベースのポータル、スクリプトがサポートされるコマンドライン ツール、または直接 API を使用して管理できます。 実施する一般的な管理タスクをいくつか次に示します。

- VM に関する情報の取得
- VM への接続
- 可用性の管理
- バックアップの作成

### <a name="get-information-about-your-vm"></a>VM に関する情報を取得する

次の表に、VM に関する情報の取得方法の一部を示します。


|方法|説明|
|---------|---------|
|Azure Stack ポータル|ハブ メニューの [Virtual Machines (仮想マシン)] をクリックし、一覧から VM を選択します。 その VM のページで、概要情報を確認したり、値の設定やメトリックの監視を実行したりできます。|
|Azure PowerShell|VM の管理は、Azure と Azure Stack とで似ています。 PowerShell の使用について詳しくは、次の Azure トピックをご覧ください。<br>[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|クライアント SDK|C# による VM の管理は、Azure と Azure Stack とで似ています。 詳細については、次の Azure トピックを参照してください。<br>[C# を使用して Azure で Windows VM を作成および管理する](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>VM に接続する

Azure Stack ポータルの **[接続]** ボタンを使用して、VM に接続できます。

## <a name="next-steps"></a>次の手順

- [Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)
