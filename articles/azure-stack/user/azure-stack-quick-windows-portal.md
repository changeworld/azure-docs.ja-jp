---
title: Azure Stack クイックスタート - Windows 仮想マシンの作成
description: Azure Stack クイック スタート - ポータルを使用した Windows VM の作成
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: e4e3fdbdd3bc9eb982f993a9be60ba0812c68a9d
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713743"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>クイック スタート: Azure Stack ポータルで Windows Server 仮想マシンを作成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack ポータルを使用して Windows Server 2016 仮想マシンを作成できます。 この記事の手順に従って仮想マシンを作成し、使用します。

> [!NOTE]  
> この記事の画面の画像は、Azure Stack バージョン 1808 で導入されたユーザー インターフェイスに合わせて更新されます。 1808 では、アンマネージド ディスクのほか、"*マネージド ディスク*" の使用のサポートが追加されています。 以前のバージョンを使用する場合は、ディスクの選択など、一部の画像が、この記事に示されているものとは異なります。  


## <a name="sign-in-to-the-azure-stack-portal"></a>Azure Stack ポータルへのサインイン

Azure Stack ポータルにサインインします。 Azure Stack ポータルのアドレスは、接続している Azure Stack 製品によって異なります。

* Azure Stack Development Kit (ASDK) の場合は、 https://portal.local.azurestack.external にアクセスします。
* Azure Stack 統合システムの場合は、Azure Stack オペレーターによって提供された URL にアクセスします。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. **[+ Create a resource]\(+ リソースの作成\)** > **[コンピューティング]** > **[Windows Server 2016 Datacenter – 従量課金]** > **[作成]** の順にクリックします。 **[Windows Server 2016 Datacenter - 従量課金]** エントリが表示されない場合は、Azure Stack オペレータに問い合わせてください。 「[Add the Windows Server 2016 VM image to the Azure Stack marketplace](../azure-stack-add-default-image.md)」(Windows Server 2016 VM イメージの Azure Stack Marketplace への追加) の記事の説明に従って、マーケットプレースに追加するように求めます。

    ![ポータルで Windows 仮想マシンを作成する手順](media/azure-stack-quick-windows-portal/image01.png)
2. **[基本]** で、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力します。 **[サブスクリプション]** を選択します。 **リソース グループ**を作成するか、既存のリソース グループを選択し、**[場所]** を選択して、**[OK]** をクリックします。

    ![基本設定を構成する](media/azure-stack-quick-windows-portal/image02.png)
3. **[サイズ]** で **[D1 Standard]\(D1 Standard\)** を選択して、**[選択]** をクリックします。  
    ![仮想マシンのサイズを選択する](media/azure-stack-quick-windows-portal/image03.png)

4. **[設定]** ページで、必要に応じて既定値を変更します。
   - Azure Stack バージョン 1808 以降、**ストレージ**を構成できます。ここで "*マネージド ディスク*" の使用を選択できます。 バージョン 1808 より前は、アンマネージド ディスクのみを使用できます。  
   ![仮想マシンの設定を構成する](media/azure-stack-quick-windows-portal/image04.png)  
   お使いの構成の準備が整ったら、**[OK]** を選択して続行します。

5. **[概要]** で、**[OK]** をクリックして仮想マシンを作成します。
    ![概要を表示し、仮想マシンを作成する](media/azure-stack-quick-windows-portal/image05.png)

6. 新しい仮想マシンを表示するには、**[すべてのリソース]** をクリックして仮想マシン名を検索し、検索結果の仮想マシン名をクリックします。
    ![仮想マシンを表示する](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

仮想マシンの使用を完了したら、仮想マシンとそのリソースを削除します。 そのためには、仮想マシン ページでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、基本の Windows Server 仮想マシンをデプロイしました。 Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)」に進んでください。
