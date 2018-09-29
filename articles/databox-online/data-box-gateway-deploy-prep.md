---
title: 'チュートリアル: Data Box Gateway をデプロイするための Azure portal の準備 | Microsoft Docs'
description: Azure Data Box Gateway をデプロイする最初のチュートリアルでは、Azure portal の準備を行います。
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 4c21245e05625f65b6b5dcf5f7081f5f5d1abb52
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030607"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>チュートリアル: Azure Data Box Gateway のデプロイを準備する (プレビュー)


これは、Azure Data Box Gateway を完全にデプロイするために必要な一連のチュートリアルのうち最初のものです。 このチュートリアルでは、Data Box Gateway リソースをデプロイするために Azure portal を準備する方法を説明します。 

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


> [!IMPORTANT]
> - Data Box Gateway はプレビュー段階にあります。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 

### <a name="get-started"></a>作業開始

Data Box Gateway をデプロイするには、以下の順序どおりにチュートリアルを参照してください。

| **#** | **手順** | **概要** |
| --- | --- | --- | 
| 1. |**[Data Box Gateway のために Azure portal を準備する](data-box-gateway-deploy-prep.md)** |Data Box Gateway リソースを作成して構成した後で、Data Box Gateway 仮想デバイスをプロビジョニングします。 |
| 2. |**[Hyper-V で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[VMware で Data Box Gateway をプロビジョニングする](data-box-gateway-deploy-provision-vmware.md)**|Hyper-V の場合、Windows Server 2016 または Windows Server 2012 R2 上で Hyper-V を実行するホスト システムに Data Box Gateway 仮想デバイスをプロビジョニングして接続します。 <br><br><br> VMware の場合、VMware ESXi 6.0 または 6.5 を実行するホスト システムに Data Box Gateway 仮想デバイスをプロビジョニングして接続します。<br></br> |
| 手順 3. |**[Data Box Gateway を接続、設定、アクティブ化する](data-box-gateway-deploy-connect-setup-activate.md)** |ローカル Web UI に接続して、デバイス設定を完了し、デバイスをアクティブ化します。 これで、SMB 共有をプロビジョニングできます。  |
| 4. |**[Data Box Gateway でデータを転送する](data-box-gateway-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="prerequisites"></a>前提条件

ここでは、Data Box Gateway リソース、Data Box Gateway デバイス、データセンター ネットワークについて構成の前提条件を示します。

### <a name="for-the-data-box-gateway-resource"></a>Data Box Gateway リソースの場合

開始する前に次の点を確認します。

* Microsoft Azure サブスクリプションで Data Box Gateway リソースが有効になっていること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-data-box-gateway-device"></a>Data Box Gateway デバイスの場合

仮想デバイスをデプロイする前に次の点を確認します。

* Windows Server 2012 R2 以降の Hyper-V または VMware (ESXi 6.0 または 6.5) を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。
* ホスト システムで、次のリソースを Data Box 仮想デバイスのプロビジョニング専用に使用できること。
  
  * 最小で 4 コア。
  * 少なくとも 8 GB の RAM。 
  * ネットワーク インターフェイス 1 つ。
  * 250 GB の OS ディスク。
  * システム データ用の 2 TB の仮想ディスク。

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

* Data Box Gateway デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)に関する記事を参照してください。

* Data Box Gateway で 20 Mbps 以上の専用インターネット帯域幅が常に利用できること。 この帯域幅はその他のアプリケーションと共有できません。 ネットワーク調整を使用する場合は、調整が機能するために、32 Mbps 以上のインターネット帯域幅を使用することをお勧めします。

## <a name="create-a-new-resource"></a>新しいリソースを作成

新しい Data Box Gateway リソースを作成するには、次の手順を実行します。 

仮想マシンを管理するために既存の Data Box Gateway リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Azure portal で次の手順を実行して Data Box リソースを作成します。
1. Microsoft Azure 資格情報を使用し、Azure プレビュー ポータルにログインします。URL は [https://aka.ms/databox-edge](https://aka.ms/databox-edge) です。 

2. Data Box Edge プレビューに使用するサブスクリプションを選択します。 Data Box Edge リソースをデプロイするリージョンを選択します。 **[Data Box Gateway]** オプションで **[作成]** をクリックします。

    ![Data Box Gateway サービスの検索](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. 新しいリソースについて、次の情報を入力または選択します。
    
    |Setting  |値  |
    |---------|---------|
    |リソース名   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 - 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |サブスクリプション    |サブスクリプションは、課金アカウントにリンクされます。 |
    |リソース グループ  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。     |
    |Location     |このリリースでは、米国東部、米国西部 2、東南アジア、西ヨーロッパを選択できます。 <br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|
    
    ![Data Box Gateway リソースの作成](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Click **OK**.
 
リソースの作成には数分かかります。 リソースが正常に作成されると通知が表示されます。


## <a name="download-the-virtual-device-image"></a>仮想デバイスのイメージのダウンロード

Data Box Gateway リソースを作成したら、ホスト システムに仮想デバイスをプロビジョニングするために、適切な仮想デバイスのイメージをダウンロードする必要があります。 仮想デバイスのイメージはオペレーティング システムに固有で、Azure portal でリソースの **[クイック スタート]** ページからダウンロードできます。

> [!IMPORTANT]
> Data Box Gateway 上で実行されるソフトウェアは、Data Box Gateway リソースに対してのみ使用できます。


[Azure Portal](https://portal.azure.com/) で、次の手順を実行します。

1. 作成したリソースをクリックしてから、**[概要]** をクリックします。 既存の Azure Data Box Gateway リソースがある場合は、そのリソースをクリックしてから、**[概要]** に進みます。

    ![新しい Data Box Gateway リソース](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. 右側のウィンドウのクイック スタートで、ダウンロードするイメージに対応するリンクをクリックします。 イメージ ファイルは約 4.8 GB です。
   
   * [Windows Server 2012 R2 以降の Hyper-V 用 VHDX](https://aka.ms/dbe-vhdx-2012)
   * [VMWare ESXi 6.0 または 6.5 用 VMDK](https://aka.ms/dbe-vmdk).

5. ファイルをローカル ドライブにダウンロードして解凍し、解凍したファイルの場所をメモしておきます。


## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Data Box Gateway リソースが起動して稼働中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Data Box Gateway デバイスをアクティブ化し、リソースに接続します。

アクティブ化キーを使用して、Data Box Gateway リソースに対してアクティブ化する必要があるすべての Data Box Gateway デバイスを登録します。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースをクリックしてから、**[概要]** をクリックします。

    ![新しい Data Box Gateway リソース](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. **[キーの生成]** をクリックして、アクティブ化キーを作成します。 コピー アイコンをクリックしてキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。 
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * 仮想デバイスのイメージのダウンロード
> * アクティブ化キーの取得

次のチュートリアルに進んで、Data Box Gateway の仮想マシンをプロビジョニングする方法を学びます。 使用するホスト オペレーティング システムに応じて手順は異なります。

> [!div class="nextstepaction"]
> [Hyper-V で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-hyperv.md)

または

> [!div class="nextstepaction"]
> [VMware で Data Box Gateway をプロビジョニングする](./data-box-gateway-deploy-provision-vmware.md)


