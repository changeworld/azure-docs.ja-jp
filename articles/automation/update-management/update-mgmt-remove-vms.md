---
title: Azure Automation の Update Management から VM を削除する
description: この記事では、Update Management で管理されているマシンを削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648637"
---
# <a name="remove-vms-from-update-management"></a>Update Management から VM を削除する

環境内の VM に対する更新プログラムの管理が完了したら、[Update Management](update-mgmt-overview.md) 機能を使用して VM の管理を停止できます。 この管理を停止するには、保存された検索クエリ `MicrosoftDefaultComputerGroup` を、自分の Automation アカウントにリンクされた Log Analytics ワークスペースで編集します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Azure portal で、Azure portal の上部のナビゲーションから **[Cloud Shell]** を起動します。 Azure Cloud Shell について初めて学ぶ場合は、「[Azure Cloud Shell の概要](../../cloud-shell/overview.md)」を参照してください。

2. 次のコマンドを使用して、管理から削除するマシンの UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

4. Log Analytics ワークスペースで、 **[ログ]** を選択し、上部の [アクション] メニューから **[クエリ エクスプローラー]** を選択します。

5. 右側のウィンドウの **[クエリ エクスプローラー]** で、 **[Saved Queries\Updates]\(保存されたクエリ\更新\)** を展開し、保存されたクエリ `MicrosoftDefaultComputerGroup` を選択して編集します。

6. クエリ エディターで、クエリを確認して VM の UUID を見つけます。 VM の UUID を削除して、削除する他の VM にもこの手順を繰り返します。

7. 編集が完了したら、上部のバーから **[保存]** を選択して、保存された検索を保存します。

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内に評価されたすべてのマシンについて報告するためです。 マシンを削除した後、マシンが一覧に表示されなくなるには、24 時間待つ必要があります。

## <a name="next-steps"></a>次のステップ

仮想マシンの管理を再度有効にするには、[Azure portal の参照による Update Management の有効化](update-mgmt-enable-portal.md)に関するページまたは「[Azure VM から Update Management を有効にする](update-mgmt-enable-vm.md)」を参照してください。