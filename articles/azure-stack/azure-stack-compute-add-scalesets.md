---
title: 仮想マシン スケール セットを Azure Stack で使用できるようにする | Microsoft Docs
description: クラウド オペレーターが Azure Stack Marketplace に仮想マシン スケール セットを追加する方法
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/22/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: 2c615dd781b40c3ebb78ae291453c5b4b2d2ef4d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971829"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>仮想マシン スケール セットを Azure Stack で使用できるようにする

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*
  
仮想マシン スケール セットは Azure Stack のコンピューティング リソースです。 これらを使用して同一の仮想マシン セットをデプロイおよび管理できます。 すべての仮想マシンが同一に構成されているため、スケール セットでは仮想マシンを事前にプロビジョニングする必要がありません。 ビッグ コンピューティング、ビッグ データ、コンテナー化されたワークロードを対象にした大規模サービスを簡単に構築できます。

この記事では、Azure Stack Marketplace でスケール セットを使用できるようにするプロセスについて説明します。 この手順を完了すると、ユーザーは各自のサブスクリプションに仮想マシン スケール セットを追加できます。

Azure Stack の仮想マシン スケール セットは、Azure の仮想マシン スケール セットと同様です。 詳細については、次のビデオをご覧ください。
* [Mark Russinovich が語る Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets を Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack では、仮想マシン スケール セットは自動スケールをサポートしていません。 Resource Manager テンプレート、CLI、または PowerShell を使用して、スケール セットに複数のインスタンスを追加できます。

## <a name="prerequisites"></a>前提条件

- **Marketplace:** Marketplace で項目の可用性を有効にするには、Azure Stack をグローバル Azure に登録します。 [Azure Stack の Azure への登録](azure-stack-registration.md)に関する記事の手順に従います。
- **オペレーティング システム イメージ:** 仮想マシン スケール セット (VMSS) を作成する前に、[Azure Stack Marketplace](azure-stack-download-azure-marketplace-item.md) から VMSS で使用する VM イメージをダウンロードする必要があります。 ユーザーが新しい VMSS を作成する前にイメージが既に存在している必要があります。 

## <a name="use-the-azure-stack-portal"></a>Azure Stack ポータルの使用 

>[!IMPORTANT]  
> このセクションの情報は、1808 以降の Azure Stack バージョンを使用するときに適用されます。 お使いのバージョンが 1807 以前であれば、[仮想マシン スケール セットを追加する (1808 より前の)](#add-the-virtual-machine-scale-set-prior-to-version-1808) を参照してください。

1. Azure Stack ポータルにサインインします。 **[すべてのサービス]**、**[仮想マシン スケール セット]** に進み、**[コンピューター]** の下の **[仮想マシン スケール セット]** を選択します。 
   ![[仮想マシン スケール セット] を選択する](media/azure-stack-compute-add-scalesets/all-services.png)

2. ***[仮想マシン スケール セットの作成]*** を選択します。
   ![仮想マシン スケール セットを作成する](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 空のフィールドに入力し、**[オペレーティング システムのディスク イメージ]**、**[サブスクリプション]**、および **[インスタンス サイズ]** のドロップダウン リストから選択します。 **[管理ディスクを使用]** で **[はい]** を選択します。 **[作成]** をクリックします。
    ![構成と作成](media/azure-stack-compute-add-scalesets/create.png)

4. 新しい仮想マシン スケール セットを表示するには、**[すべてのリソース]** に進んで仮想マシン スケール セット名を検索し、検索でその名前を選択します。 
   ![スケール セットの表示](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>仮想マシン スケール セットを追加する (バージョン 1808 より前)

>[!IMPORTANT]  
> このセクションの情報は、1808 より前の Azure Stack バージョンを使用するときに適用されます。 1808 以降のバージョンを使用している場合は、[Azure Stack ポータルを使用する](#use-the-azure-stack-portal)を参照してください。

1. Azure Stack Marketplace を開き、Azure に接続します。 **[Marketplace Management]** を選択し、**[+ Azure から追加]** をクリックします。

    ![Marketplace Management](media/azure-stack-compute-add-scalesets/image01.png)

2. 仮想マシン スケール セット マーケットプレース項目を追加してダウンロードします。

    ![仮想マシン スケール セット](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セット内のイメージを更新する

仮想マシン スケール セットを作成すると、スケール セットを再作成することなく、スケール セット内のイメージを更新できます。 イメージを更新するプロセスは、次のようにシナリオによって変わります。

1. 仮想マシン スケール セット デプロイ テンプレートの **version** には **latest** を指定します。  

   スケール セットの `imageReference` セクションで `version` を **latest** に設定していると、スケール セットに対するスケールアップ操作で、スケール セット インスタンスの最新バージョンのイメージが使用されます。 スケールアップが完了したら、以前の仮想マシン スケール セット インスタンスを削除できます。 `publisher`、`offer`、および `sku` の値は変わりません。 

   次の JSON の例では、`latest` を以下のように指定しています。  

    ```json  
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
 
2. 仮想マシン スケール セット デプロイ テンプレートで **version** に **latest を指定せず**、代わりにバージョン番号を指定します。  

    (使用可能なバージョンを変更する) 新しいバージョンを含むイメージをダウンロードした場合、スケール セットはスケールアップできません。 スケール セット テンプレートに指定されたイメージのバージョンを使用できる必要があるため、これは仕様です。  

詳細は、[オペレーティング システムのディスクとイメージ](./user/azure-stack-compute-overview.md#operating-system-disks-and-images)に関するページを参照してください。  

## <a name="scale-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを拡大縮小する

*仮想マシン スケール セット*のサイズをスケールして大きく、または小さくすることができます。  

1. ポータルで、スケール セットを選択し、**[拡大縮小]** を選択します。

2. スライドバーを使用してこの仮想マシン スケール セットのスケーリングの新しいレベルを設定し、**[保存]** をクリックします。
     ![セットを拡大縮小する](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを削除する

仮想マシン スケール セットのギャラリー アイテムを削除するには、次の PowerShell コマンドを実行します。

```PowerShell  
Remove-AzsGalleryItem
```

> [!NOTE]
> ギャラリー アイテムはすぐに削除されない場合があります。 Marketplace からアイテムが削除済みとして表示されるまで、ポータルを数回更新する必要がある場合があります。

## <a name="next-steps"></a>次の手順

[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)