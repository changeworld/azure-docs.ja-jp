---
title: Azure portal を使用して Azure 専用ホストにデプロイする
description: Azure portal を使用して専用ホストに VM をデプロイします。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c8e2ac929b3285b0ba122928485b423e34dc8f4f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835131"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>ポータルを使用して専用ホストに VM をデプロイする

この記事では、仮想マシン (VM) をホストするための Azure [専用ホスト](dedicated-hosts.md)を作成する方法について説明します。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>VM の作成

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. Azure Marketplace リソースの一覧の上にある検索ボックスで Canonical の **Ubuntu Server 16.04 LTS** を検索して選択し、 **[作成]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、 **[リソース グループ]** として *myDedicatedHostsRG* を選択します。 
1. **[Instance details] (インスタンスの詳細)** で、 **[仮想マシン名]** として「*myVM*」と入力し、 **[場所]** として *[米国東部]* を選択します。
1. **[可用性オプション]** で **[可用性ゾーン]** を選択し、ドロップダウンから *[1]* を選択します。
1. サイズでは、 **[サイズの変更]** を選択します。 使用可能なサイズの一覧で、Esv3 シリーズの 1 つを選択します (**Standard E2s v3** など)。 使用可能なサイズをすべて表示するには、フィルターをクリアすることが必要な場合があります。
1. **[Administrator account] (管理者アカウント)** で、 **[SSH Public Key] (SSH 公開キー)** を選択し、ユーザー名を入力して公開キーをテキスト ボックスに貼り付けます。 公開キーの先頭または末尾の空白はすべて削除します。

    ![[Administrator account] (管理者アカウント)](./media/quick-create-portal/administrator-account.png)

1. **[受信ポートの規則]**  >  **[パブリック受信ポート]** で、 **[選択したポートを許可する]** を選択して、ドロップダウンから **[SSH (22)]** を選択します。 
1. ページの上部にある **[詳細設定]** タブを選択し、 **[ホスト]** セクションで、 **[ホスト グループ]** として *myHostGroup* を、 **[ホスト]** として *myHost* を選択します。 
    ![ホスト グループとホストを選択する](./media/dedicated-hosts-portal/advanced.png)
1. 残りの既定値はそのままにして、ページの一番下にある **[Review + create] (確認および作成)** ボタンを選択します。
1. 検証が成功したことを示すメッセージが表示されたら、 **[作成]** を選択します。

VM がデプロイされるまでに数分かかります。

## <a name="next-steps"></a>次のステップ

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- [Azure CLI](dedicated-hosts-cli.md) を使用して専用ホストをデプロイすることもできます。



