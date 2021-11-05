---
title: Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する
description: Azure portal のフレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/25/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3f6b0512d97b78111be5d88d20bf7800ebe9937c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008505"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

この記事では、Azure portal を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal (https://portal.azure.com ) にログインします。


## <a name="create-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する

Windows Server イメージまたは Linux イメージ (RHEL、CentOS、Ubuntu、SLES など) を含むスケール セットをデプロイできます。

1. Azure portal 検索バーで、「**仮想マシン スケール セット**」を検索して選択します。
1. **[仮想マシン スケール セット]** ページで **[作成]** を選択します。

1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、リソース グループ リストから *[myVMSSResourceGroup]* を選択します。  
1. **[スケール セットの詳細]** で、スケール セット名に *myScaleSet* を設定し、ご自分の地域に近い **[リージョン]** を選択します。
1. **[オーケストレーション]** の **[オーケストレーション モード]** で、 *[Flexible]\(フレキシブル\)* オプションを選択します。 
1. **[インスタンスの詳細]** で、 **[イメージ]** のマーケットプレース イメージを選択します。 この例では、 *[Ubuntu Server 18.04 LTS]* を選択しました。
1. 目的のユーザー名を入力して、任意の認証の種類を選択します。
   - **パスワード** は、12 文字以上で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 つの要件のうち、3 つを満たしている必要があります。 詳細については、[ユーザー名とパスワードの要件](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)を参照してください。
   - Linux OS ディスク イメージを選択した場合は、代わりに **[SSH public key]\(SSH 公開キー\)** を選択できます。 公開キーのみを指定してください ( *~/.ssh/id_rsa.pub* など)。 ポータルから Azure Cloud Shell を使用して、[SSH キー](../virtual-machines/linux/mac-create-ssh-keys.md)を作成および使用することができます。

1. **[次へ]** を選択して、次のページに進みます。 

1. **[ディスク]** ページの既定値はそのままにします。

1. **[次へ]** を選択して、次のページに進みます。 

1. **[ネットワーク]** ページの **[負荷分散]** で、 **[ロード バランサーを使用する]** チェック ボックスをオンにして、スケール セット インスタンスをロード バランサーの背後に配置します。 
1. **[負荷分散のオプション]** で、 **[Azure Load Balancer]** を選択します。
1. **[ロード バランサーを選択します]** で、ロード バランサーを選択するか、新しいロード バランサーを作成します。
1. **[Select a backend pool] (バックエンド プールを選択します)** で、 **[新規作成]** を選択し、「*myBackendPool*」と入力して **[作成]** を選択します。

    > [!NOTE]
    > フレキシブル スケール セットのネットワークに関する関連情報については、[フレキシブル スケール セットのスケーラブルなネットワーク接続](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity)に関するページを参照してください。

1. **[次へ]** を選択して、次のページに進みます。

1. **[スケーリング]** ページで、 **[初期インスタンス数]** フィールドを *5* に設定します。 この数値は、1000 まで設定できます。 
1. **[スケーリング ポリシー]** については、 *[手動]* のままにします。 

1. 完了したら、 **[確認および作成]** を選択します。 
1. 検証に合格したら、 **[作成]** を選択してスケール セットをデプロイします。


## <a name="clean-up-resources"></a>リソースをクリーンアップする
必要がなくなったら、リソース グループ、スケール セット、およびすべての関連リソースを削除します。 それを行うには、スケール セットのリソース グループを選択してから **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure CLI でフレキシブル スケールを作成する方法について説明します。](flexible-virtual-machine-scale-sets-cli.md)