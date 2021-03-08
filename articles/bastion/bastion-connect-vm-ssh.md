---
title: Azure Bastion を使用して Linux VM に接続する
description: この記事では、Azure Bastion を使用して Linux 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 5d61c2a1a0f5d7b26809621af6dfa88cf5080320
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518183"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure Bastion を使用して Linux 仮想マシンに SSH 接続する

この記事では、Azure 仮想ネットワーク内の Linux VM に安全かつシームレスに SSH 接続する方法について説明します。 VM には Azure portal から直接接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 Azure Bastion の詳細については、[概要](bastion-overview.md)に関する記事をご覧ください。

Azure Bastion を使用して、Linux 仮想マシンに SSH 接続できます。 認証には、ユーザー名/パスワードと SSH キーの両方を使用できます。 SSH キーを使用して VM に接続するには、次のいずれかを使用します。

* 手動で入力した秘密キー
* 秘密キーの情報を含むファイル

SSH 秘密キーは、`"-----BEGIN RSA PRIVATE KEY-----"` で始まり、`"-----END RSA PRIVATE KEY-----"` で終わる形式である必要があります。

## <a name="prerequisites"></a>前提条件

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](./tutorial-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 

Bastion を使用して接続する場合、Windows VM への接続には RDP を使用し、Linux VM への接続には SSH を使用していることを前提としています。 Windows VM への接続については、[VM への接続 (Windows)](bastion-connect-vm-rdp.md) に関する記事をご覧ください。

### <a name="required-roles"></a>必要なロール

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

SSH 経由で Linux VM に接続するには、お使いの VM で次のポートを開いておく必要があります。

* 受信ポート:SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>接続: ユーザー名とパスワードを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Azure portal の仮想マシンの概要のスクリーンショット。[接続] が選択されています":::
1. [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./tutorial-create-host-portal.md)に関する記事をご覧ください。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="[仮想マシンへの接続] ダイアログ ボックスのスクリーンショット。[BASTION] が選択されています":::
1. 仮想マシンに SSH 接続するためのユーザー名とパスワードを入力します。
1. キーを入力したら、 **[接続]** を選択します。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>接続: 秘密キーを手動で入力する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Azure portal の仮想マシンの概要のスクリーンショット。[接続] が選択されています":::
1. [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./tutorial-create-host-portal.md)に関する記事をご覧ください。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="[仮想マシンへの接続] ダイアログ ボックス。[BASTION] が選択されています。":::
1. ユーザー名を入力し、 **[SSH 秘密キー]** を選択します。
1. **[SSH 秘密キー]** のテキスト領域に秘密キーを入力します (または直接貼り付けます)。
1. キーを入力したら、 **[接続]** を選択します。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>接続: 秘密キー ファイルを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="[接続] を選択":::
1. [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./tutorial-create-host-portal.md)に関する記事をご覧ください。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="[BASTION] を選択。":::
1. ユーザー名を入力し、 **[SSH Private Key from Local File]\(ローカル ファイルの SSH 秘密キー\)** を選択します。
1. **[参照]** ボタン (ローカル ファイルのフォルダー アイコン) を選択します。
1. ファイルを参照し、 **[開く]** を選択します。
1. **[接続]** を選択して VM に接続します。 [接続] をクリックすると、この仮想マシンへの SSH 接続が Azure portal で直接開きます。 この接続は、仮想マシンのプライベート IP を使用して、Bastion サービスのポート 443 で HTML5 を介して行われます。

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>接続: Azure Key Vault に格納された秘密キーの使用

>[!NOTE]
>この機能のポータルの更新は、現在リージョンにロールアウトされています。
>

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。
1. [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   :::image type="content" source="./media/bastion-connect-vm-ssh/bastion.png" alt-text="[Bastion] タブ":::
1. ユーザー名を入力し、 **[SSH Private Key from Azure Key Vault]\(Azure Key Vault の SSH 秘密キー\)** を選択します。
1. **[Azure Key Vault]** ドロップダウンを選択し、SSH 秘密キーを保存したリソースを選択します。 Azure Key Vault リソースを設定していない場合は、[Key Vault の作成](../key-vault/general/quick-create-portal.md)に関する記事を参照し、SSH 秘密キーを新しい Key Vault シークレットの値として格納します。

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

Key Vault リソースに格納されているシークレットの **一覧表示** および **取得** のアクセス権を持っていることを確認します。 Key Vault リソースのアクセス ポリシーの割り当てと変更については、[Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する記事を参照してください。

1. **[Azure Key Vault Secret]\(Azure Key Vault シークレット\)** ドロップダウンを選択し、SSH 秘密キーの値が含まれている Key Vault シークレットを選択します。
1. **[接続]** を選択して VM に接続します。 [接続] をクリックすると、この仮想マシンへの SSH 接続が Azure portal で直接開きます。 この接続は、仮想マシンのプライベート IP を使用して、Bastion サービスのポート 443 で HTML5 を介して行われます。

## <a name="next-steps"></a>次のステップ

[Bastion に関する FAQ](bastion-faq.md) を読む
