---
title: Azure Marketplace 向けの仮想マシン イメージのオンプレミスでの作成 | Microsoft Docs
description: VM イメージをオンプレミスで作成し、他のユーザーが購入できるように Azure Marketplace にデプロイするための手順を理解して実行します。
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''

ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte

---
# Azure Marketplace 向けの仮想マシン イメージのオンプレミスでの作成
Azure 仮想ハード ディスク (VHD) は、リモート デスクトップ プロトコルを使用してクラウドで直接作成することを強くお勧めします。ただし、必要な場合は、VHD をダウンロードして、オンプレミスのインフラストラクチャを使用して開発できます。

オンプレミスでの開発では、作成済みの VM のオペレーション システム VHD をダウンロードする必要があります。これらの手順は、手順 3.3 の一部として実行します。

## VHD イメージをダウンロードする
### BLOB URL の検出
VHD をダウンロードするには、まず、オペレーティング システム ディスクの BLOB URL を探します。

新しい [Microsoft Azure ポータル](https://portal.azure.com)から BLOB URL を見つけます。

1. **[参照]**、**[仮想マシン]** の順に移動し、デプロイされた VM を選択します。
2. **[構成]** で **[ディスク]** タイルを選択し、[ディスク] ブレードを開きます。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. **[OS ディスク]** を選択します。これにより、VHD の場所を含むディスク プロパティが表示された別のブレードが開きます。
4. この BLOB URL をコピーします。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. デプロイされた VM を削除します。バック ディスクは削除しません。削除するのではなく、VM を停止することもできます。VM の実行中は、オペレーティング システム VHD をダウンロードしないでください。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### VHD のダウンロード
BLOB URL が分かったら、[Azure ポータル](http://manage.windowsazure.com/)または PowerShell を使用して VHD をダウンロードできます。

> [!NOTE]
> このガイドの作成時点では、VHD をダウンロードするための機能は、新しい Microsoft Azure ポータルにはまだ存在していません。
> 
> 

**現在の [Azure ポータル](http://manage.windowsazure.com/)経由でのオペレーティング システム VHD のダウンロード**

1. まだサインインしていない場合は、Azure ポータルにサインインします。
2. **[ストレージ]** タブをクリックします。
3. VHD が格納されているストレージ アカウントを選択します。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. これにより、ストレージ アカウントのプロパティが表示されます。**[コンテナー]** タブを選択します。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. VHD が格納されているコンテナーを選択します。既定では、ポータルから作成されるときに、VHD は vhds コンテナーに格納されます。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. URL を保存済みの URL と比較して、適切なオペレーティング システム VHD を選択します。
7. **[Download]** をクリックします。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### PowerShell を使用した VHD のダウンロード
Azure ポータルを使用する以外に、[Save-AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) コマンドレットを使用して、オペレーティング システム VHD をダウンロードすることもできます。

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
例: Save-AzureVhd -Source “https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” -LocalFilePath “C:\\Users\\Administrator\\Desktop\\baseimagevm.vhd” -StorageKey <String>

> [!NOTE]
> **Save-azurevhd** には、並列処理を強化してダウンロードで利用できる帯域幅を最大限に活用するために使用できる **NumberOfThreads** オプションもあります。
> 
> 

## VHD を Azure ストレージ アカウントにアップロードする
VHD をオンプレミスで準備した場合は、それらを Azure のストレージ アカウントにアップロードする必要があります。この手順は、VHD をオンプレミスで作成した後、VM イメージの認定を取得する前に実行します。

### ストレージ アカウントとコンテナーの作成
VHD は、米国内のリージョンのストレージ アカウントにアップロードすることをお勧めします。1 つの SKU のすべての VHD は、1 つのストレージ アカウント内の 1 つのコンテナーに配置する必要があります。

ストレージ アカウントを作成するには、[Microsoft Azure ポータル](https://portal.azure.com/)、PowerShell、または Linux コマンドライン ツールを使用できます。

**Microsoft Azure ポータルからのストレージ アカウントの作成**

1. **[新規]** をクリックします。
2. **[ストレージ]** を選択します。
3. ストレージ アカウント名を入力し、場所を選択します。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. **[作成]** をクリックします。
5. 作成したストレージ アカウント用のブレードが開きます。開かない場合は、**[参照]**、**[ストレージ アカウント]** の順に選択します。[ストレージ アカウント] ブレードで、先ほど作成したストレージ アカウントを選択します。
6. **[コンテナー]** を選択します。
   
   ![図](media/marketplace-publishing-vm-image-creation-on-premise/img09.png)
7. [コンテナー] ブレードで、**[追加]** を選択し、コンテナー名とコンテナーのアクセス許可を入力します。コンテナーのアクセス許可には **[プライベート]** を選択します。

> [!TIP]
> 発行する予定の SKU ごとに 1 つのコンテナーを作成することをお勧めします。
> 
> 

  ![図](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### PowerShell を使用したストレージ アカウントの作成
PowerShell の [New-AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) コマンドレットを使用して、ストレージ アカウントを作成します。

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

その後、そのストレージ アカウント内に、[NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) コマンドレットを使用してコンテナーを作成できます。

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> これらのコマンドでは、現在のストレージ アカウントのコンテキストが PowerShell に設定されていることを前提としています。PowerShell のセットアップの詳細については、「[Azure PowerShell のセットアップ](marketplace-publishing-powershell-setup.md)」を参照してください。
> 
> ### Mac と Linux 用のコマンドライン ツールを使用したストレージ アカウントの作成
> [Linux コマンドライン ツール](../virtual-machines/virtual-machines-linux-cli-manage.md)から、次のようにストレージ アカウントを作成します。
> 
> 

        azure storage account create mystorageaccount --location "West US"

コンテナーを次のように作成します。

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## VHD のアップロード
ストレージ アカウントとコンテナーを作成したら、準備した VHD をアップロードできます。PowerShell、Linux コマンドライン ツール、またはその他の Azure Storage 管理ツールを使用できます。

### PowerShell を使用して VHD をアップロードする
[Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) コマンドレットを使用します。

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### Mac と Linux 用のコマンドライン ツールを使用した VHD のアップロード
[Linux コマンドライン ツール](../virtual-machines/command-line-tools.md)では、次のコマンドを使用します。azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>

## 関連項目
* [Marketplace 向けの仮想マシン イメージの作成](marketplace-publishing-vm-image-creation.md)
* [Azure PowerShell の設定](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0615_2016-->