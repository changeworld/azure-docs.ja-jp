---
title: "Azure から Marketplace の項目をダウンロードする | Microsoft Docs"
description: "Azure から Azure Stack のデプロイに Marketplace の項目をダウンロードすることができます。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: erikje
ms.openlocfilehash: 33b7be4a85723ab03e4c656a8dd28632ad854e29
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure から Azure Stack に Marketplace の項目をダウンロードする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack Marketplace に含めるコンテンツを決定するときは、Azure Marketplace から利用可能なコンテンツを検討する必要があります。 Azure Stack 上で動くことがテスト済みの Azure Marketplace の項目の精選された一覧からダウンロードすることができます。 一覧には新しいアイテムが頻繁に追加されるので、ときどき新しいコンテンツを確認してください。

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>接続されているシナリオでの Marketplace アイテムのダウンロード (インターネット接続を使用)

1. Marketplace の項目をダウンロードするには、最初に [Azure Stack を Azure に登録](azure-stack-register.md)します。 
2. Azure Stack 管理ポータル (https://portal.local.azurestack.external) にサインインします。
3. Marketplace アイテムの中には大きいものがあります。 **[リソース プロバイダー]** > **[ストレージ]** をクリックして、システムに十分な領域があることを確認してください。

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. **[その他のサービス]** > **[Marketplace Management]\(Marketplace の管理\)** をクリックします。

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. **[Add from Azure]\(Azure から追加\)** をクリックして、ダウンロードできる項目の一覧を表示します。 一覧の各項目をクリックして、説明とダウンロード サイズを見ることができます。

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. 一覧で必要な項目を選び、**[ダウンロード]** をクリックします。 選んだ項目の VM イメージのダウンロードが始まります。 ダウンロードの時間は項目によって異なります。

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. ダウンロードが完了したら、Azure Stack オペレーターまたはユーザーとして、新しい Marketplace の項目をデプロイできます。 **[+ 新規]** をクリックして、カテゴリの中から新しい Marketplace の項目を探し、選択します。
7. **[作成]** をクリックして、新しくダウンロードした項目の作成エクスペリエンスを開きます。 表示される手順に従って項目をデプロイします。

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>接続が切断されたシナリオまたは部分的に接続されているシナリオでの Marketplace アイテムのダウンロード (制限付きのインターネット接続を使用)

Azure Stack を非接続モード (インターネット接続なし) でデプロイする場合、Azure Stack ポータルを使用して Marketplace アイテムをダウンロードすることはできません。 ただし、マーケットプレース シンジケーション ツールを使用して、インターネットに接続されたマシンに Marketplace アイテムをダウンロードしてから、Azure Stack 環境に転送することができます。 

### <a name="prerequisites"></a>前提条件
マーケットプレース シンジケーション ツールを使用する前に、[自分の Azure サブスクリプションで登録済みの Azure Stack](azure-stack-register.md) があることを確認してください。  

インターネットに接続されているマシンから、次の手順に従って必要な Marketplace アイテムをダウンロードします。

1. PowerShell コンソールを管理者として開き、[Azure Stack 固有の PowerShell モジュールをインストール](azure-stack-powershell-install.md)します。 必ず、**PowerShell バージョン 1.2.11 以上**をインストールしてください。  

2. Azure Stack の登録に使用した Azure アカウントを追加します。 これを行うには、**Add-AzureRmAccount** コマンドレットをパラメーターなしで実行します。 Azure アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。  

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、登録に使用したサブスクリプションを選択します。  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 次のスクリプトを使用して、マーケットプレース シンジケーション ツールの最新バージョンをダウンロードします。  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/vnext.zip `
     -OutFile vnext.zip

   # Expand the downloaded files.
   expand-archive vnext.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-vnext

   ```

5. 次のコマンドを実行して、シンジケーション モジュールをインポートし、ツールを起動します。  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination “<Destination folder path>” `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. ツールが実行されると、Azure アカウント資格情報の入力を求められます。 Azure Stack の登録に使用した Azure アカウントにサインインします。 ログインが成功すると、使用可能な Marketplace アイテムの一覧がある、次の画面が表示されます。  

   ![Azure Marketplace アイテムのポップアップ](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. ダウンロードするイメージを選択し (Ctrl キーを押しながら複数のイメージを選択できます)、イメージのバージョンを書き留めてください。次のセクションでイメージをインポートするときに、このバージョンを使用します。**[OK]**、**[はい]** の順にクリックし、法律条項に同意します。 また、**[Add criteria]\(条件の追加\)** オプションを使用して、イメージの一覧をフィルター処理することもできます。 ダウンロードには、イメージのサイズに応じて時間がかかります。 イメージがダウンロードされたら、前に指定したダウンロード先のパスで使用できます。 ダウンロードには、Azpkg 形式で VHD ファイルとギャラリー アイテムが含まれています。  

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>イメージのインポートと Azure Stack Marketplace への発行

1. イメージとギャラリーのパッケージをダウンロードした後、それらと AzureStack-Tools-vnext フォルダーの内容をリムーバブル ディスク ドライブに保存して、Azure Stack 環境にコピーします (C:\MarketplaceImages など、ローカルの任意の場所にコピーできます)。   

2. イメージをインポートする前に、「[Azure Stack オペレーターの PowerShell 環境の構成](azure-stack-powershell-configure-admin.md)」で説明されている手順に従って、Azure Stack オペレーターの環境に接続する必要があります。  

3. Add-AzsVMImage コマンドレットを使用して、Azure Stack にイメージをインポートします。 このコマンドレットを使用する場合は、publisher、offer などのパラメーター値を、インポートするイメージの値で置き換えてください。 イメージの "publisher"、"offer"、および "sku" の値は、前にダウンロードした Azpkg ファイルの imageReference オブジェクトから取得できます。また、"version" の値は、前のセクションの手順 6. で取得できます。

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   パラメーター値を置き換えて、次のコマンドを実行します。

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2017.09.25" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Microsoft.WindowsServer2016DatacenterServerCore-ARM-Eval.2017.09.25.vhd" `
    -CreateGalleryItem $False `
    -Location Local 
   ```

4. ポータルを使用して Marketplace アイテム (.Azpkg) を Azure Stack BLOB ストレージにアップロードします。 ローカルの Azure Stack ストレージへのアップロード、または Azure Storage へのアップロードができます。 (パッケージの一時的な場所です。)BLOB がパブリックにアクセスできることを確認し、URI を書き留めておきます。  

5. **Add-AzureRMGalleryItem** を使用して、Marketplace アイテムを Azure Stack に発行します。 For example:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. ギャラリー アイテムが発行されると、**[新規]** > **[Marketplace]** ウィンドウに表示されます。  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>次のステップ

[Marketplace アイテムの作成と発行](azure-stack-create-and-publish-marketplace-item.md)
