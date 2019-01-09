---
title: Edge をデプロイするための Azure portal の準備に関するチュートリアル | Microsoft Docs
description: Edge のデプロイに関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 25f68b011d1fcba450903e9a691b98dfe9e87281
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726121"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>チュートリアル:Edge のデプロイを準備する  


これは、Edge を完全にデプロイするために必要な一連のデプロイ チュートリアルの中で最初のものです。 このチュートリアルでは、Edge リソースをデプロイするために Azure portal を準備する方法を説明します。 

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。  

### <a name="get-started"></a>作業開始

Edge をデプロイするには、以下の順序どおりにチュートリアルを参照してください。

| **#** | **手順** | **概要** |
| --- | --- | --- | 
| 1. |**[Edge のために Azure portal を準備する](data-box-edge-deploy-prep.md)** |Edge 物理デバイスをインストールする前に、Edge リソースを作成して構成します。 |
| 2. |**[Edge をインストールする](data-box-edge-deploy-install.md)**|Edge 物理デバイスの開梱、ラックへの取り付け、ケーブル接続を行います。  |
| 手順 3. |**[Edge を接続、設定、アクティブ化する](data-box-edge-deploy-connect-setup-activate.md)** |ローカル Web UI に接続して、デバイス設定を完了し、デバイスをアクティブ化します。 このデバイスは、SMB または NFS 共有を設定する準備ができています。  |
| 4. |**[Edge を使用してデータを転送する](data-box-edge-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |
| 5. |**[Edge を使用してデータを変換する](data-box-edge-deploy-configure-compute.md)** |データを Azure に移動するときにデータを変換するように、デバイス上の Edge モジュールを構成します。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="prerequisites"></a>前提条件

次に示すのは、Edge リソース、Edge デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-data-box-edge-resource"></a>Edge リソースに対して

開始する前に次の点を確認します。

* Microsoft Azure サブスクリプションで Edge リソースが有効になっていること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

### <a name="for-the-data-box-edge-device"></a>Edge デバイスに対して

物理デバイスを配置する前に次の点を確認します。
- デバイスをラック マウントするため、データセンターの標準 19 インチ ラックに空いている 1 U スロットが 1 つあること。 
- デバイスを安全に置くことができる、平らで安定した水平の作業面が用意されていること。
- デバイスをセットアップする予定の場所に、独立した電源からの標準 AC 電源、または無停電電源装置 (UPS) を備えたラック配電ユニット (PDU) があること。
- 物理デバイスにアクセスできること。


### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

* Edge デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳しくは、[Edge のシステム要件](data-box-gateway-system-requirements.md)に関する記事をご覧ください。

* Edge で 20 Mbps 以上の専用インターネット帯域幅を常時利用できること。 この帯域幅はその他のアプリケーションと共有できません。 帯域幅調整を使用する場合は、帯域幅調整が正しく機能するように 32 Mbps 以上のインターネット帯域幅を使用することをお勧めします。

## <a name="create-a-new-resource"></a>新しいリソースを作成

新しい Edge リソースを作成するには、次の手順を実行します。 

物理デバイスを管理する既存の Edge リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用し、Azure プレビュー ポータルにサインインします。URL は [https://aka.ms/databox-edge](https://aka.ms/databox-edge) です。 

2. Edge プレビューに使用するサブスクリプションを選択します。 Data Box Edge リソースをデプロイするリージョンを選択します。 **Edge** オプションで、**[作成]** を選択します。

    ![Edge サービスを検索する](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. 新しいリソースについて、次の情報を入力または選択します。
    
    |Setting  |値  |
    |---------|---------|
    |リソース名   | リソースを識別するわかりやすい名前を入力します。<br>リソース名は 2 - 50 文字で、英字、数字、ハイフンを使用します。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |サブスクリプション    |サブスクリプションは、課金アカウントにリンクされます。 |
    |リソース グループ  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/resource-group-overview.md)をご覧ください。     |
    |場所     |このリリースでは、米国東部、米国西部 2、東南アジア、西ヨーロッパを選択できます。 <br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|
    
    ![Edge リソースを作成する](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. **[OK]** を選択します。
 
リソースの作成には数分かかります。 リソースが正常に作成されると通知が表示されます。


## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Edge デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースを選択してから、**[概要]** を選択します。

2. **[キーの生成]** を選択して、アクティブ化キーを作成します。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。 
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Edge に関する以下のトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースの作成
> * アクティブ化キーの取得

次のチュートリアルに進み、Edge をインストールする方法を学習してください。 

> [!div class="nextstepaction"]
> [Edge をインストールする](./data-box-edge-deploy-install.md)



