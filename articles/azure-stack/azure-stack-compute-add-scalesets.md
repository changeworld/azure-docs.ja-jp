---
title: 仮想マシン スケール セットを Azure Stack で使用できるようにする | Microsoft Docs
description: クラウド オペレーターが Azure Stack Marketplace に仮想マシン スケールを追加する方法
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: cdabd2a9d336cdd8ac83d27460fe129c45b7e1c6
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>仮想マシン スケール セットを Azure Stack で使用できるようにする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

仮想マシン スケール セットは Azure Stack のコンピューティング リソースです。 これらを使用して同一の仮想マシン セットをデプロイおよび管理できます。 すべての仮想マシンが同一に構成されているため、スケール セットでは仮想マシンを事前にプロビジョニングする必要がありません。 ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードを対象にした大規模サービスを簡単に構築できます。

この記事では、Azure Stack Marketplace でスケール セットを使用できるようにするプロセスについて説明します。 この手順を完了すると、ユーザーは各自のサブスクリプションに仮想マシン スケール セットを追加できます。

Azure Stack の仮想マシン スケール セットは、Azure の仮想マシン スケール セットと同様です。 詳細については、次のビデオをご覧ください。
* [Mark Russinovich が語る Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets を Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack では、仮想マシン スケール セットは自動スケールをサポートしていません。 Azure Stack ポータル、Resource Manager テンプレート、または PowerShell を使用してスケール セットにインスタンスを追加できます。

## <a name="prerequisites"></a>前提条件
* **Powershell とツール**

   Azure Stack の PowerShell と Azure Stack ツールをインストールして構成します。 「[Get up and running with PowerShell in Azure Stack (Azure Stack での PowerShell の稼働)](azure-stack-powershell-configure-quickstart.md)」をご覧ください。

   Azure Stack ツールをインストールしたら、次の PowerShell モジュールをインポートします (AzureStack-Tools-master フォルダーの .\ComputeAdmin フォルダーからの相対パス)。

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **オペレーティング システム イメージ**

   Azure Stack Marketplace にオペレーティング システム イメージを追加していない場合は、「[Add the Windows Server 2016 VM image to the Azure Stack marketplace (Azure Stack Marketplace に Windows Server 2016 の VM イメージを追加する)](azure-stack-add-default-image.md)」をご覧ください。

   Linux のサポートでは、Ubuntu Server 16.04 をダウンロードし、```Add-AzsVMImage``` とパラメーター ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"``` を使用して追加します。


## <a name="add-the-virtual-machine-scale-set"></a>仮想マシン スケール セットの追加

お使いの環境に合わせて次の PowerShell スクリプトを編集し、スクリプトを実行して Azure Stack Marketplace に仮想マシン スケール セットを追加します。 

``$User`` は、管理者ポータルへの接続に使用するアカウントです。 たとえば、「serviceadmin@contoso.onmicrosoft.com」のように入力します。

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セットのイメージを更新する 
仮想マシン スケール セットを作成すると、スケール セットを再作成することなく、スケール セット内のイメージを更新できます。 イメージを更新するプロセスは、次のようにシナリオによって変わります。

1. 仮想マシン スケール セット デプロイ テンプレートの *version* には **latest を指定**します。  

   スケール セットのテンプレートの *imageReference* セクションで *version* を **latest** に設定していると、スケール セットに対するスケールアップ操作で、スケール セット インスタンスの最新バージョンのイメージが使用されます。 スケールアップが完了したら、以前の仮想マシン スケール セット インスタンスを削除できます  (*publisher*、*offer*、*sku* の値は変更されません)。 

   *latest* を指定する例を次に示します。  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   スケールアップで新しいイメージを使用するには、そのイメージをダウンロードしておく必要があります。  

   - Marketplace 上のイメージがスケール セットのイメージよりも新しいバージョンの場合: 以前のイメージを置き換える新しいイメージをダウンロードします。 イメージが置き換えられると、スケールアップに進むことができます。 

   - Marketplace 上のイメージのバージョンがスケール セットのイメージと同じ場合: スケール セットで使用されているイメージを削除し、新しいイメージをダウンロードします。 元のイメージを削除してから新しいイメージをダウンロードするまでの間に、スケールアップすることはできません。 
      
     このプロセスは、バージョン 1803 で導入されたスパース ファイル形式を利用するイメージを再配布するために必要です。 
 

2. 仮想マシン スケール セット デプロイ テンプレートで *version* に **latest を指定せず**、代わりにバージョン番号を指定します。  

     (使用可能なバージョンを変更する) 新しいバージョンを含むイメージをダウンロードした場合、スケール セットはスケールアップできません。 スケール セット テンプレートに指定されたイメージのバージョンを使用できる必要があるため、これは仕様です。  

詳細は、[オペレーティング システムのディスクとイメージ](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)に関するページを参照してください。  


## <a name="remove-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの削除

仮想マシン スケール セットのギャラリー アイテムを削除するには、次の PowerShell コマンドを実行します。

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> ギャラリー アイテムはすぐに削除されない場合があります。 Marketplace からアイテムが削除済みとして表示されるまで、ポータルを数回更新する必要がある場合があります。


## <a name="next-steps"></a>次の手順
[Azure Stack に関してよく寄せられる質問](azure-stack-faq.md)

