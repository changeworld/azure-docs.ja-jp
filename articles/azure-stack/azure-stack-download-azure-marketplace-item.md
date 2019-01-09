---
title: Azure から Marketplace の項目をダウンロードする | Microsoft Docs
description: クラウド オペレーターは、Marketplace 項目を Azure から Azure Stack デプロイにダウンロードできます。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/10/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 70bbade2877b62c3d211600f69e1825677f12040
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721871"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Azure から Azure Stack に Marketplace の項目をダウンロードする

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

クラウド オペレーターとして、Azure Marketplace から項目をダウンロードし、Azure Stack で使用できるようにします。 選択できる項目は、Azure Marketplace の精選された一覧にある項目です。これらの項目は、Azure Stack で動作するように、事前にテストされ、サポートされています。 一覧には新しいアイテムが頻繁に追加されるので、定期的に新しいコンテンツを確認してください。 

Azure Marketplace に接続するには、次の 2 つのシナリオがあります。 

- **接続されているシナリオ** - Azure Stack 環境がインターネットに接続している必要があります。 Azure Stack ポータルを使用して、項目を検索し、ダウンロードします。 
- **接続されていないか、部分的に接続されているシナリオ** - Marketplace 項目をダウンロードするために、マーケットプレース シンジケーション ツールを使用してインターネットにアクセスする必要があります。 その後、ダウンロードしたファイルを、接続されていない Azure Stack インストールに転送します。 このシナリオでは、PowerShell を使用します。

ダウンロードできる Marketplace 項目の一覧については、[Azure Stack 用の Azure Marketplace 項目](azure-stack-marketplace-azure-items.md)に関するページを参照してください。


## <a name="connected-scenario"></a>接続されているシナリオ
Azure Stack がインターネットに接続している場合は、管理ポータルを使用して Marketplace 項目をダウンロードすることができます。

### <a name="prerequisites"></a>前提条件
Azure Stack デプロイは、インターネットに接続し、[Azure に登録](azure-stack-register.md)されている必要があります。

### <a name="use-the-portal-to-download-marketplace-items"></a>ポータルを使用して Marketplace 項目をダウンロードする  
1. Azure Stack 管理者ポータルにサインインします。

2.  Marketplace 項目をダウンロードする前に、使用可能なストレージ領域を確認してください。 後で、ダウンロードする項目を選択するときに、ダウンロード サイズと使用可能なストレージ容量を比較することができます。 容量が限られている場合は、[空き領域の管理](azure-stack-manage-storage-shares.md#manage-available-space)のためのオプションを検討してください。 

    空き領域を確認するには、**[リージョンの管理]** で対象リージョンを選択し、**[リソース プロバイダー]** > **[ストレージ]** の順に移動します。

    [ ![ストレージ領域を確認する](media/azure-stack-download-azure-marketplace-item/storagesm.png "ストレージ領域を確認する") ](media/azure-stack-download-azure-marketplace-item/storage.png#lightbox)

    
3. Azure Stack Marketplace を開き、Azure に接続します。 そのためには、**[Marketplace management]\(Marketplace 管理\)** を選択し、**[Add from Azure]\(Azure から追加\)** を選択します。

    [ ![[Add from Azure]\(Azure から追加\)](media/azure-stack-download-azure-marketplace-item/marketplacesm.png "[Add from Azure]\(Azure から追加\)") ](media/azure-stack-download-azure-marketplace-item/marketplace.png#lightbox)

    Azure Marketplace からダウンロードできる項目の一覧がポータルに表示されます。 各項目をクリックすると、項目の説明と、ダウンロード サイズなどの追加情報が表示されます。 

    [ ![Marketplace の一覧](media/azure-stack-download-azure-marketplace-item/image03sm.png "Marketplace の一覧") ](media/azure-stack-download-azure-marketplace-item/image03.png#lightbox)

4. 項目を選択し、**[ダウンロード]** を選択します。 ダウンロードの時間は項目によって異なります。

    [ ![ダウンロードのメッセージ](media/azure-stack-download-azure-marketplace-item/image04.png "ダウンロードのメッセージ") ](media/azure-stack-download-azure-marketplace-item/image04.png#lightbox)

    ダウンロードが完了したら、Azure Stack オペレーターまたはユーザーとして、新しい Marketplace 項目をデプロイできます。

5. ダウンロードした項目をデプロイするには、**[+ リソースの作成]** を選択し、カテゴリで新しいマーケットプレース項目を検索します。 次に、デプロイ処理を開始する項目を選択します。 処理は、Marketplace 項目ごとに異なります。 

## <a name="disconnected-or-a-partially-connected-scenario"></a>接続されていないか、部分的に接続されているシナリオ

Azure Stack が切断モードでインターネットに接続されていない場合は、PowerShell と*マーケットプレース シンジケーション ツール*を使用して、インターネットに接続しているマシンに Marketplace 項目をダウンロードします。 その後、Azure Stack 環境に項目を転送します。 接続されていない環境では、Azure Stack ポータルを使用して Marketplace 項目をダウンロードすることはできません。 

マーケットプレース シンジケーション ツールは、接続されているシナリオでも使用することができます。 

このシナリオは次の 2 つに分けられます。
- **パート 1:** Azure Marketplace からダウンロードする。 インターネットにアクセスできるコンピューターで PowerShell を構成し、シンジケーション ツールをダウンロードして、Azure Marketplace から項目をダウンロードします。  
- **パート 2:** Azure Stack Marketplace にアップロードして発行する。 ダウンロードしたファイルを Azure Stack 環境に移動し、Azure Stack にインポートして、Azure Stack Marketplace に発行します。  


### <a name="prerequisites"></a>前提条件
- Azure Stack のデプロイは、[Azure に登録されている](azure-stack-register.md)必要があります。  

- インターネットに接続できるコンピューターには、**Azure Stack PowerShell モジュール バージョン 1.2.11** 以降が存在する必要があります。 まだ存在しない場合は、[Azure Stack 固有の PowerShell モジュールをインストール](azure-stack-powershell-install.md)してください。  

- ダウンロードした Marketplace 項目のインポートを有効にするには、[Azure Stack オペレーター用の PowerShell 環境](azure-stack-powershell-configure-admin.md)を構成する必要があります。  

- パブリックにアクセスできるコンテナー (ストレージ BLOB) がある Azure Stack の[ストレージ アカウント](azure-stack-manage-storage-accounts.md)を持っている必要があります。 コンテナーは、Marketplace 項目のギャラリー ファイルのための一時的なストレージとして使用します。 ストレージ アカウントとコンテナーに慣れていない場合は、Azure ドキュメントの [Azure portal での BLOB の操作](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)に関するページを参照してください。

- マーケットプレース シンジケーション ツールは、最初の手順でダウンロードされます。 

- ダウンロードのパフォーマンスを最適化するために [AzCopy](../storage/common/storage-use-azcopy.md) をインストールできますが、これは必須ではありません。

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>マーケットプレース シンジケーション ツールを使用して Marketplace 項目をダウンロードする

1. インターネットに接続されているコンピューターで、PowerShell コンソールを管理者として開きます。

2. Azure Stack の登録に使用した Azure アカウントを追加します。 アカウントを追加するには、PowerShell でパラメーターなしで `Add-AzureRmAccount` を実行します。 Azure アカウントの資格情報の入力を求めるメッセージが表示されます。アカウントの構成によっては、2 要素認証を使用する必要があります。

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、登録に使用したサブスクリプションを選択します。  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 次のスクリプトを使用して、マーケットプレース シンジケーション ツールの最新バージョンをダウンロードします。  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. 次のコマンドを実行して、シンジケーション モジュールをインポートし、ツールを起動します。 `Destination folder path` を、Azure Marketplace からダウンロードしたファイルを保存する場所に置き換えます。   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

6. ツールを実行すると、次の図のような画面が表示され、使用可能なマーケットプレース項目の一覧が示されます。

   [ ![Azure Marketplace 項目のポップアップ](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace 項目") ](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. ダウンロードする項目を選択し、"*バージョン*" を書き留めておきます  (*Ctrl* キーを押しながら、複数のイメージを選択することができます)。 次の手順で項目をインポートするときに、"*バージョン*" を参照します。 
   
   また、**[Add criteria]\(条件の追加\)** オプションを使用して、イメージの一覧をフィルター処理することもできます。

8. **[OK]** を選択し、法律条項を確認して同意します。 

9. ダウンロードにかかる時間は、項目のサイズによって異なります。 ダウンロードが完了したら、その項目をスクリプトで指定したフォルダーで使用できます。 ダウンロードには、VHD ファイル (仮想マシンの場合)、または .zip ファイル (仮想マシン拡張機能の場合) が含まれています。 また、*.azpkg* 形式 (実体は .zip ファイル) のギャラリー パッケージが含まれていることもあります。

10. ダウンロードに失敗した場合は、次の PowerShell コマンドレットを再実行してやり直すことができます。

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes”
    ```

    やり直す前に、ダウンロードが失敗した製品のフォルダーを削除してください。 たとえば、ダウンロード スクリプトを `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` にダウンロードするときに失敗した場合は、`D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` フォルダーを削除した後、コマンドレットを再実行します。
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>ダウンロードのインポートと Azure Stack Marketplace への発行 (1811 以降)

1. ローカルに[ダウンロード済みの](#use-the-marketplace-syndication-tool-to-download-marketplace-items)ファイルを移動して、お使いの Azure Stack 環境で利用できるようにする必要があります。 マーケットプレース シンジケーション ツールを使用してインポート操作を実行する必要があるので、このツールも、Azure Stack 環境で利用できるようにする必要があります。

   次の図に、フォルダー構造の例を示します。 ダウンロードしたマーケットプレースのすべての項目は `D:\downloadfolder` に含まれます。 各サブフォルダーは、製品 ID 別のマーケットプレース項目です (例: `microsoft.custom-script-linux-arm-2.0.3`)。 各サブ フォルダーの内容は、マーケットプレース項目のダウンロードされたコンテンツです。

   [ ![マーケットプレースのダウンロード ディレクトリ構造](media/azure-stack-download-azure-marketplace-item/mp1sm.png "マーケットプレースのダウンロード ディレクトリ構造") ](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. [こちらの記事](azure-stack-powershell-configure-admin.md)の指示に従って、Azure Stack Operator PowerShell セッションを構成します。 

3. 次のスクリプトを実行して、シンジケーション モジュールをインポートした後、マーケットプレース シンジケーション ツールを起動します。

   ```PowerShell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -armendpoint "Environment Arm Endpoint" -AzsCredential $credential
   ```
    `-AzsCredential` パラメーターは省略可能です。 それは、有効期限が切れている場合にアクセス トークンを更新するために使用されます。 `-AzsCredential` パラメーターが指定されていないときにトークンの有効期限が切れている場合は、オペレーターの資格情報の入力を求めるメッセージが表示されます。

4. スクリプトが正常に完了すると、Azure Stack Marketplace で項目を利用できるようになります。

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>ダウンロードのインポートと Azure Stack Marketplace への発行 (1809 以前)

1. [以前にダウンロードした](#use-the-marketplace-syndication-tool-to-download-marketplace-items)仮想マシン イメージまたはソリューション テンプレートのファイルは、Azure Stack 環境でローカルに使用できるようにする必要があります。  

2. 管理ポータルを使用して、マーケットプレース項目パッケージ (.azpkg ファイル) と仮想ハード ディスク イメージ (.vhd ファイル) を Azure Stack BLOB ストレージにアップロードします。 パッケージとディスク ファイルをアップロードすると、Azure Stack で利用できるようになるため、後で Azure Stack Marketplace に項目を発行することができます。

   アップロードするには、パブリックにアクセス可能なコンテナーを持つストレージ アカウントが必要です (このシナリオの前提条件を参照)。  
   1. Azure Stack 管理ポータルで、**[すべてのサービス]** に移動します。次に、**[データ + ストレージ]** カテゴリで **[ストレージ アカウント]** を選択します。  
   
   2. サブスクリプションからストレージ アカウントを選択し、**[BLOB サービス]** で **[コンテナー]** を選択します。  
      [ ![Blob service](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob service") ](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. 使用するコンテナーを選択し、**[アップロード]** を選択して **[BLOB のアップロード]** ペインを開きます。  
      [ ![コンテナー](media/azure-stack-download-azure-marketplace-item/container.png "コンテナー") ](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. [BLOB のアップロード] ウィンドウで、ストレージに読み込むパッケージとディスク ファイルを参照し、**[アップロード]** を選択します。[ ![アップロード](media/azure-stack-download-azure-marketplace-item/uploadsm.png "アップロード") ](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. アップロードしたファイルは、コンテナー ペインに表示されます。 ファイルを選択し、**[BLOB のプロパティ]** ペインで URL をコピーします。 次の手順で Marketplace 項目を Azure Stack にインポートするときに、この URL を使用します。  次の図では、コンテナーは *blob-test-storage* で、ファイルは *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* です。  ファイル URL は *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* です。  
      [ ![BLOB のプロパティ](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "BLOB のプロパティ") ](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. **Add-AzsPlatformimage** コマンドレットを使用して、Azure Stack に VHD イメージをインポートします。 このコマンドレットを使用する場合は、*publisher*、*offer* などのパラメーター値を、インポートするイメージの値で置き換えてください。 

   AZPKG ファイルと共にダウンロードされたテキスト ファイルから、イメージの "*パブリッシャー*"、"*オファー*"、および *SKU* の値を取得できます。 テキスト ファイルは、指定の場所に保存されています。 *version* の値は、前の手順で Azure から項目をダウンロードするときに書き留めておいたバージョンです。 
 
   次のスクリプト例では、Windows Server 2016 Datacenter - Server Core 仮想マシンの値が使用されています。 *-Osuri* の値は、項目の BLOB 保存場所のパスの例です。

   このスクリプトに代わる方法として、[こちらの記事で説明されている手順](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal)に従い、Azure portal を使って .VHD イメージをインポートすることもできます。

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **ソリューション テンプレートについて:** いくつかのテンプレートには、名前が **fixed3.vhd** の小さな 3 MB .VHD ファイルを含めることができます。 Azure Stack にそのファイルをインポートする必要はありません。 Fixed3.vhd。  このファイルは、Azure Marketplace の発行要件を満たすために、いくつかのソリューション テンプレートに含まれています。

   テンプレートの説明を確認してダウンロードし、ソリューション テンプレートを操作するために必要な VHD などの追加要件をインポートします。  
   
   **拡張機能について:** 仮想マシン イメージの拡張機能を扱うときは、次のパラメーターを使用してください。
   - *発行元*
   - *種類*
   - *バージョン*  

   拡張機能に *Offer* は使用しません。   


4.  PowerShell で **Add-AzsGalleryItem** コマンドレットを使用して、Marketplace 項目を Azure Stack に発行します。 例:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. ギャラリー項目は、発行すると、使用できるようになります。 ギャラリー項目が発行されたことを確認するには、**[すべてのサービス]** に移動し、**[全般]** カテゴリで **[Marketplace]** を選択します。  ソリューション テンプレートをダウンロードする場合は、そのソリューション テンプレートのすべての依存 VHD イメージを追加します。  
  [ ![マーケットプレースの表示](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "マーケットプレースの表示") ](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Azure Stack PowerShell 1.3.0 のリリースにより、仮想マシン拡張機能を追加できるようになりました。 例: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>次の手順

[Marketplace アイテムの作成と発行](azure-stack-create-and-publish-marketplace-item.md)