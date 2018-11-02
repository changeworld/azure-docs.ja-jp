---
title: Azure Marketplace 向けの VHD からの VM のデプロイ | Microsoft Docs
description: Azure にデプロイされた VHD からの VM を登録する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639129"
---
# <a name="deploy-a-vm-from-your-vhds"></a>VHD からの VM のデプロイ

この記事では、Azure にデプロイされた仮想ハード ディスク (VHD) からの仮想マシン (VM) を登録する方法について説明します。  必要なツールのリストと、それらのツールを使ってユーザー VM イメージを作成してから、それを [Microsoft Azure portal](https://ms.portal.azure.com/) または PowerShell スクリプトのいずれかを使って Azure にデプロイする方法について示します。 

仮想ハード ディスク (VHD) —汎用化されたオペレーティング システム VHD および 0 個以上のデータ ディスク VHD—を Azure ストレージ アカウントにアップロードした後、これらをユーザー VM イメージとして登録できます。 その後、そのイメージをテストできます。 オペレーティング システム VHD は汎用化されるため、VHD URL を指定して VM を直接デプロイすることはできません。

VM イメージの詳細については、次のブログを参照してください。

- [VM イメージ](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To' (VM イメージ PowerShell ハウツー)](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>必要なツールをセットアップする

Azure PowerShell と Azure CLI をまだインストールしていない場合は、次の指示に従ってインストールします。

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>ユーザー VM イメージの作成

次に、汎用化した VHD からアンマネージド イメージを作成します。

#### <a name="capture-the-vm-image"></a>VM イメージのキャプチャ

VM のキャプチャに関する次の記事から、アクセス方法に対応する手順を使用してください。

-  PowerShell: [Azure VM からアンマネージド VM イメージを作成する方法](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [仮想マシンまたは VHD のイメージを作成する方法](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines - キャプチャ](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>VM イメージの汎用化

以前に汎用化した VHD からユーザー イメージを生成しているため、これも汎用化する必要があります。  ここでも、アクセス メカニズムに対応する記事を次から選択します。  (ディスクをキャプチャしたときに、既に汎用化している可能性があります。)

-  PowerShell: [VM の汎用化](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [手順 2. VM イメージを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines - 汎用化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>ユーザー VM イメージからの VM のデプロイ

次に、Azure portal または PowerShell を使用して、ユーザー VM イメージから VM をデプロイします。

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Azure portal からの VM のデプロイ

次のプロセスを使用して、Azure portal から VM をデプロイします。

1.  [Azure Portal](https://portal.azure.com) にサインインします。

2.  **[新規]** をクリックして、**[テンプレートのデプロイ]** を検索し、**[エディターで独自のテンプレートを作成する]** を選択します。  <br/>
  ![Azure portal で VHD デプロイ テンプレートを作成する](./media/publishvm_021.png)

3. この [JSON テンプレート](./cpp-deploy-json-template.md)をコピーしてエディターに貼り付け、**[保存]** をクリックします。 <br/>
  ![Azure portal で VHD デプロイ テンプレートを保存する](./media/publishvm_022.png)

4. 表示されている**カスタム デプロイ**のプロパティ ページのパラメーター値を指定します。

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **パラメーター**              |   **説明**                                                            |
   |  -------------              |   ---------------                                                            |
   | ユーザー ストレージ アカウント名   | 汎用化された VHD が配置されているストレージ アカウント名                    |
   | ユーザー ストレージ コンテナー名 | 汎用化された VHD が配置されているコンテナー名                          |
   | パブリック IP の DNS 名      | パブリック IP DNS 名                                                           |
   | 管理ユーザー名             | 新しい VM の管理者アカウントのユーザー名                                  |
   | 管理パスワード              | 新しい VM の管理者アカウントのパスワード                                  |
   | [OS Type]\(OS の種類\)                     | VM オペレーティング システム: `Windows` \| `Linux`                                    |
   | サブスクリプション ID             | 選択したサブスクリプションの識別子                                      |
   | Location                    | デプロイの地理的な場所                                        |
   | VM サイズ                     | [Azure VM サイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)、例`Standard_A2` |
   | パブリック IP アドレス名      | パブリック IP アドレスの名前                                               |
   | VM 名                     | 新しい VM の名前                                                           |
   | 仮想ネットワーク名        | VM で使用する仮想ネットワークの名前                                   |
   | NIC 名                    | 仮想ネットワークを実行しているネットワーク インターフェイス カードの名前               |
   | VHD の URL                     | OS ディスクの VHD の URL を完了する                                                     |
   |  |  |
            
5. これらの値を指定した後、**[購入]** をクリックします。 

Azure はデプロイを開始します。これにより、指定のストレージ アカウント パスに、指定されたアンマネージド VHD を含む新しい VM が作成されます。  ポータルの左側にある **[Virtual Machines]** をクリックして Azure portal で進捗状況を追跡できます。  VM が作成されると、状態は `Starting` から `Running` に変わります。 


### <a name="deploy-a-vm-from-powershell"></a>PowerShell からの VM のデプロイ

作成したばかりの汎用化された VM イメージから大規模な VM をデプロイするには、次のコマンドレットを使用します。

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>次の手順

VM のデプロイが完了したら、[VM を構成](./cpp-configure-vm.md)する準備が整います。
