---
title: データセンターで Azure portal を介して Azure Stack Edge Pro FPGA をデプロイする準備をするためのチュートリアル
description: Azure Stack Edge Pro FPGA の配置に関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: alkohli
ms.custom: subject-rbac-steps
ms.openlocfilehash: d215c1cc8650eb23d1702f7ef0fd782278cfb6d8
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605207"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-fpga"></a>チュートリアル: Azure Stack Edge Pro FPGA の配置を準備する  

これは、Azure Stack Edge Pro FPGA を完全に配置するために必要な配置チュートリアル シリーズの最初のチュートリアルです。 このチュートリアルでは、Azure Stack Edge リソースを配置するために Azure portal を準備する方法を説明します。 

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 新しいリソースを作成
> * アクティブ化キーの取得

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="get-started"></a>はじめに

Azure Stack Edge Pro FPGA を配置するには、次の順序どおりにチュートリアルを参照してください。

| **#** | **手順** | **概要** |
| --- | --- | --- | 
| 1. |**[Azure Stack Edge Pro FPGA のために Azure portal を準備する](azure-stack-edge-deploy-prep.md)** |Azure Stack Box Edge 物理デバイスを設置する前に、Azure Stack Edge リソースを作成して構成します。 |
| 2. |**[Azure Stack Edge Pro FPGA を設置する](azure-stack-edge-deploy-install.md)**|Azure Stack Edge Pro FPGA 物理デバイスの開梱、ラックへの取り付け、およびケーブル接続を行います。  |
| 3. |**[Azure Stack Edge Pro FPGA を接続、設定、およびアクティブ化する](azure-stack-edge-deploy-connect-setup-activate.md)** |ローカル Web UI に接続して、デバイス設定を完了し、デバイスをアクティブ化します。 このデバイスは、SMB または NFS 共有を設定する準備ができています。  |
| 4. |**[Azure Stack Edge Pro FPGA を使用してデータを転送する](azure-stack-edge-deploy-add-shares.md)** |共有を追加し、SMB または NFS を介して共有に接続します。 |
| 5. |**[Azure Stack Edge Pro FPGA でデータを変換する](azure-stack-edge-deploy-configure-compute.md)** |データを Azure に移動するときにそれを変換するように、デバイス上のコンピューティング モジュールを構成します。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="prerequisites"></a>前提条件

次に示すのは、Azure Stack Edge リソース、Azure Stack Edge Pro FPGA デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

開始する前に次の点を確認します。

* ご利用の Microsoft Azure サブスクリプションで Azure Stack Edge リソースが有効になっていること。 [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/)、[クラウド ソリューション プロバイダー (CSP)](/partner-center/azure-plan-lp)、[Microsoft Azure スポンサープラン](https://azure.microsoft.com/offers/ms-azr-0036p/)など、サポートされているサブスクリプションを使用していることを確認してください。 従量課金制サブスクリプションはサポートされていません。

* RBAC ロール: Azure ロールベースのアクセス制御 (RBAC) には、次のロールの割り当てがあります。

  * リソース、Azure Stack Edge、IoT Hub および Azure ストレージ リソースを作成するには、ユーザーがリソース グループ スコープで共同作成者ロールまたは所有者ロールを持っている必要があります。

  * リソース グループ スコープのユーザーに共同作成者ロールを割り当てるには、サブスクリプション スコープで所有者ロールが必要です。

  詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

* リソース プロバイダー: 次のリソース プロバイダーが登録されます。 

  * Azure Stack Edge/Data Box Gateway リソースを作成するには、`Microsoft.DataBoxEdge` プロバイダーが登録 されている必要があります。

  * IoT Hub リソースを作成するには、`Microsoft.Devices` プロバイダーが登録されていることを確認します。

  * Azure Storage リソースを作成するには、Azure Storage が登録されていることを確認します。 Azure Storage リソース プロバイダー (SRP) は既定で登録済みのリソース プロバイダーですが、場合によっては登録が必要になる場合があります。

  **リソース プロバイダーを登録するには、上記の関連 RBAC ロールが割り当てられている必要があります。**

  登録する方法については、[「リソース プロバイダーを登録する」](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)を参照してください。

* Azure Active Directory Graph API に対して管理者またはユーザーのアクセス権を持っていること。 詳細については、[Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) に関するページをご覧ください。

* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。

* システム管理者が設定した Azure Policy 割り当てによってブロックされていない。 Azure Policy の詳細については、「[クイックスタート: 準拠していないリソースを識別するためのポリシー割り当てを作成する](../governance/policy/assign-policy-portal.md)」を参照してください。


### <a name="for-the-azure-stack-edge-pro-fpga-device"></a>Azure Stack Edge Pro FPGA デバイスの場合

物理デバイスを配置する前に次の点を確認します。

* 配送パッケージに同梱されている安全性情報を確認済みであること。
* デバイスをラック マウントするため、データセンターの標準 19 インチ ラックに空いている 1U スロットが 1 つあること。
* デバイスを安全に置くことができる、平らで安定した水平の作業面が用意されていること。
* デバイスをセットアップする予定の場所に、独立した電源からの標準 AC 電源、または無停電電源装置 (UPS) を備えたラック配電ユニット (PDU) があること。
* 物理デバイスにアクセスできること。

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

* Azure Stack Edge Pro FPGA デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、「[Azure Stack Edge Pro FPGA のシステム要件](azure-stack-edge-system-requirements.md)」を参照してください。

* Azure Stack Edge Pro FPGA の通常の動作条件下で、以下が存在すること。

  * デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
  * ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-new-resource-for-existing-device"></a>既存のデバイス用に新しいリソースを作成する

Azure Stack Edge Pro FPGA の既存のお客様の場合、既存のデバイスを交換またはリセットする必要があるときは、次の手順に従って新しいリソースを作成してください。

新しいお客様は、ワークロードに対して Azure Stack Edge Pro - GPU デバイスの使用を検討することをお勧めします。 詳細については、「[GPU 搭載の Azure Stack Edge Pro とは](azure-stack-edge-gpu-overview.md)」を参照してください。 GPU デバイス搭載の Azure Stack Edge Pro を注文する方法の詳細については、[Azure Stack Edge Pro - GPU 用に新しいリソースを作成する](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)ことに関する記事を参照してください。

既存のデバイス用に新しい Azure Stack Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure の資格情報を使用して、次のサイトにサインインします。

    - Azure portal (URL: [https://portal.azure.com](https://portal.azure.com))。
    - または、Azure Government ポータル (URL: [https://portal.azure.us](https://portal.azure.us))。 詳細については、[ポータルを使用して Azure Government に接続する](../azure-government/documentation-government-get-started-connect-with-portal.md)方法に関するページを参照してください。

1. **[+ リソースの作成]** を選択します。 **[Azure Stack Edge]** を検索して選択します。 **[作成]** を選択します。

1. Azure Stack Edge Pro FPGA デバイスのサブスクリプションと、デバイスの出荷先の国を **[発送先]** から選択します。

   ![デバイスのサブスクリプションと出荷先の国を選択する](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. 表示されるデバイスの種類の一覧で、 **[Azure Stack Edge Pro - FPGA]** を選択します。 次に **[選択]** を選択します。 

   **[Azure Stack Edge Pro - FPGA]** のデバイスの種類は、既存のデバイスがある場合にのみ表示されます。 新しいデバイスを注文する必要がある場合は、[Azure Stack Edge Pro - GPU の新しいリソースを作成する](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)ことに関する記事を参照してください。

   ![Azure Stack Edge サービスを検索する](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. **[基本]** タブで、次の操作を行います。

   1. 次の **プロジェクトの詳細** を入力または選択します。
    
       |設定  |値  |
       |---------|---------|
       |サブスクリプション    |この値は、事前に選択した内容に応じて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
       |Resource group  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。     |

   1. 次の **インスタンス情報** を入力または選択します。

       |設定  |値  |
       |---------|---------|
       |名前   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 から 50 文字で、英字、数字、ハイフンを含みます。<br> 名前の最初と最後には、英字か数字を使用します。        |
       |リージョン     |Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。<br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|

   1. **[Review + create]\(レビュー + 作成\)** を選択します。

    ![プロジェクトとインスタンスの詳細](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. **[確認と作成]** タブで、 **[使用条件]** 、 **[価格の詳細]** 、リソースの詳細を確認します。 **[作成]** を選択します。

    ![Azure Stack Edge リソースの詳細とプライバシー条件を確認する](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. リソースの作成には数分かかります。 リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。

   ![Azure Stack Edge リソースに移動する](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Microsoft は受け取った注文を確認し、発送の詳細と共にお客様に (メールで) 連絡します。

![Azure Stack Edge Pro FPGA の注文確認通知](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Azure Stack Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、Azure Stack Edge Pro FPGA デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 作成したリソースに移動し、 **[概要]** を選択します。 注文の処理結果に関する通知が表示されます。

    ![[概要] を選択する](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. 注文が処理されてデバイスが配送されると、 **[概要]** の情報が更新されます。 既定の **Azure Key Vault 名** をそのまま使用するか、新しい名前を入力します。 **[アクティブ化キーの生成]** を選択します。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

    ![アクティブ化キーの取得](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * アクティブ化キーは生成後 3 日間で有効期限が切れます。
> * キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge Pro FPGA に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * 新しいリソースを作成
> * アクティブ化キーの取得

次のチュートリアルに進み、Azure Stack Edge Pro FPGA を設置する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro FPGA を設置する](./azure-stack-edge-deploy-install.md)