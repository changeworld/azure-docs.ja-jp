---
title: 仮想マシン スケール セットを Azure Stack で使用できるようにする | Microsoft Docs
description: クラウド オペレーターが Azure Stack Marketplace に仮想マシン スケール セットを追加する方法
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 4e77e187d969af7ea2a12754b18d4a218daceed6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411908"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>仮想マシン スケール セットを Azure Stack で使用できるようにする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

仮想マシン スケール セットは Azure Stack のコンピューティング リソースです。 これらを使用して同一の仮想マシン セットをデプロイおよび管理できます。 すべての仮想マシンが同一に構成されているため、スケール セットでは仮想マシンを事前にプロビジョニングする必要がありません。 ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードを対象にした大規模サービスを簡単に構築できます。

この記事では、Azure Stack Marketplace でスケール セットを使用できるようにするプロセスについて説明します。 この手順を完了すると、ユーザーは各自のサブスクリプションに仮想マシン スケール セットを追加できます。

Azure Stack の仮想マシン スケール セットは、Azure の仮想マシン スケール セットと同様です。 詳細については、次のビデオをご覧ください。
* [Mark Russinovich が語る Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets を Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack では、仮想マシン スケール セットは自動スケールをサポートしていません。 Resource Manager テンプレート、CLI、または PowerShell を使用して、スケール セットに複数のインスタンスを追加できます。

## <a name="prerequisites"></a>前提条件

- **Marketplace**  
    Marketplace で項目の可用性を有効にするには、Azure Stack をグローバル Azure に登録します。 [Azure Stack の Azure への登録](azure-stack-registration.md)に関する記事の手順に従います。
- **オペレーティング システム イメージ**  
    Azure Stack Marketplace にオペレーティング システム イメージを追加していない場合は、[Azure からの Azure Stack マーケットプレース項目の追加](asdk/asdk-marketplace-item.md)に関する記事をご覧ください。

## <a name="add-the-virtual-machine-scale-set"></a>仮想マシン スケール セットを追加する

1. Azure Stack Marketplace を開き、Azure に接続します。 **[Marketplace Management]**> **[+ Azure から追加]** を選択します。

    ![Marketplace Management](media/azure-stack-compute-add-scalesets/image01.png)

2. 仮想マシン スケール セット マーケットプレース項目を追加してダウンロードします。

    ![仮想マシン スケール セット](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セット内のイメージを更新する

仮想マシン スケール セットを作成すると、スケール セットを再作成することなく、スケール セット内のイメージを更新できます。 イメージを更新するプロセスは、次のようにシナリオによって変わります。

1. 仮想マシン スケール セット デプロイ テンプレートの *version* には **latest を指定**します。  

   スケール セットのテンプレートの *imageReference* セクションで *version* を **latest** に設定していると、スケール セットに対するスケールアップ操作で、スケール セット インスタンスの最新バージョンのイメージが使用されます。 スケールアップが完了したら、以前の仮想マシン スケール セット インスタンスを削除できます  (*publisher*、*offer*、*sku* の値は変更されません)。 

   *latest* を指定する例を次に示します。  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   スケールアップで新しいイメージを使用するには、そのイメージをダウンロードしておく必要があります。  

   - Marketplace 上のイメージがスケール セットのイメージよりも新しいバージョンの場合: 以前のイメージを置き換える新しいイメージをダウンロードします。 イメージが置き換えられると、スケールアップに進むことができます。 

   - Marketplace 上のイメージのバージョンがスケール セットのイメージと同じ場合: スケール セットで使用されているイメージを削除し、新しいイメージをダウンロードします。 元のイメージを削除してから新しいイメージをダウンロードするまでの間に、スケールアップすることはできません。 
      
     このプロセスは、バージョン 1803 で導入されたスパース ファイル形式を利用するイメージを再配布するために必要です。 
 

2. 仮想マシン スケール セット デプロイ テンプレートで *version* に **latest を指定せず**、代わりにバージョン番号を指定します。  

    (使用可能なバージョンを変更する) 新しいバージョンを含むイメージをダウンロードした場合、スケール セットはスケールアップできません。 スケール セット テンプレートに指定されたイメージのバージョンを使用できる必要があるため、これは仕様です。  

詳細は、[オペレーティング システムのディスクとイメージ](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)に関するページを参照してください。  


## <a name="remove-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを削除する

仮想マシン スケール セットのギャラリー アイテムを削除するには、次の PowerShell コマンドを実行します。

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> ギャラリー アイテムはすぐに削除されない場合があります。 Marketplace からアイテムが削除済みとして表示されるまで、ポータルを数回更新する必要がある場合があります。

## <a name="next-steps"></a>次の手順
[Azure Stack に関してよく寄せられる質問](azure-stack-faq.md)