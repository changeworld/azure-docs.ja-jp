---
title: Azure VMware Solution のデプロイ
description: Azure portal を使用して Azure VMware Solution をデプロイする手順。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578319"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[新しいリソースを作成]** を選択します。 **[Marketplace を検索]** テキスト ボックスに「`Azure VMware Solution`」と入力し、一覧から **[Azure VMware Solution]** を選択します。 **[Azure VMware Solution]** ウィンドウで、 **[作成]** を選択します。

1. **[基本]** タブで、各フィールドの値を入力します。 次の表に、フィールドのプロパティの一覧を示します。

   | フィールド   | 値  |
   | ---| --- |
   | **サブスクリプション** | デプロイに使用する予定のサブスクリプション。|
   | **リソース グループ** | プライベート クラウド リソースのリソース グループ。 |
   | **場所** | 場所 (**米国東部**など) を選択します。|
   | **リソース名** | Azure VMware Solution のプライベート クラウドの名前。 |
   | **SKU** | 次の SKU 値を選択します。AV36 |
   | **ホスト** | プライベート クラウド クラスターに追加するホストの数。 既定値は 3 です。この値は、デプロイ後に増減できます。  |
   | **vCenter 管理者パスワード** | クラウド管理者のパスワードを入力します。 |
   | **NSX-T Manager のパスワード** | NSX-T 管理者のパスワードを入力します。 |
   | **アドレス ブロック** | プライベート クラウドの CIDR ネットワークの IP アドレス ブロックを入力します (例: 10.175.0.0/22)。 |
   | **Virtual Network** | Virtual Network を選択するか、Azure VMware Solution プライベート クラウド用に新しいものを作成します。  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="[基本] タブで、フィールドの値を入力します。" border="true":::

1. 終わったら、 **[確認と作成]** を選択します。 次の画面で、入力した情報を確認します。 情報がすべて正しければ、 **[作成]** を選択します。

   > [!NOTE]
   > この手順には約 2 時間かかります。 

1. デプロイが成功したことを確認します。 作成したリソース グループに移動し、プライベート クラウドを選択します。  デプロイが完了すると、状態が **[成功]** として表示されます。 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="[基本] タブで、フィールドの値を入力します。" border="true":::