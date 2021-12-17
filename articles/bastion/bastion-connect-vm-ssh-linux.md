---
title: SSH を使用して Linux VM に接続する
titleSuffix: Azure Bastion
description: SSH を使用して Linux VM に接続するために Azure Bastion を使用する方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0c852cf6df97619f7a319da4e13a406c572d4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069294"
---
# <a name="create-an-ssh-connection-to-a-linux-vm-using-azure-bastion"></a>Azure Bastion を使用して Linux VM への SSH 接続を作成する

この記事では、Azure portal から直接、Azure 仮想ネットワークに置かれている Linux VM への SSH 接続を安全かつシームレスに作成する方法について説明します。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 RDP を使用して Linux VM に接続することもできます。 詳細については、[Linux VM への RDP 接続の作成](bastion-connect-vm-rdp-linux.md)に関する記事を参照してください。

Azure Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。 詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

SSH を使用して Linux 仮想マシンに接続する場合は、認証にユーザー名およびパスワードと SSH キーの両方を使用できます。 SSH キーを使用して VM に接続するには、次のいずれかを使用します。

* 手動で入力した秘密キー
* 秘密キーの情報を含むファイル

SSH 秘密キーは、`"-----BEGIN RSA PRIVATE KEY-----"` で始まり、`"-----END RSA PRIVATE KEY-----"` で終わる形式である必要があります。

## <a name="prerequisites"></a>前提条件

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](./tutorial-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 

### <a name="required-roles"></a>必要なロール

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

SSH 経由で Linux VM に接続するには、お使いの VM で次のポートを開いておく必要があります。

* 受信ポート: SSH (22) ***または***
* 受信ポート: カスタム値 (Azure Bastion 経由で VM に接続するときに、このカスタム ポートを指定する必要がある)

   > [!NOTE]
   > カスタム ポート値を指定する場合、Standard SKU を使用して Azure Bastion を構成する必要があります。 Basic SKU では、カスタム ポートを指定できません。
   >

## <a name="connect-using-username-and-password"></a><a name="username"></a>接続: ユーザー名とパスワードを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Azure portal の仮想マシンの概要のスクリーンショット。[接続] が選択されています" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::

1. [Bastion] を選択してから **[Bastion を使用する]** をクリックします。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./quickstart-host-portal.md)に関する記事をご覧ください。
1. **[Azure Bastion を使用して接続する]** ページで、 **[ユーザー名]** と **[パスワード]** を入力します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/password.png" alt-text="パスワード認証のスクリーンショット。":::
1. **[接続]** を選択して VM に接続します。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>接続: 秘密キーを手動で入力する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Azure portal の仮想マシンの概要のスクリーンショット。[接続] が選択されています。" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. [Bastion] を選択してから **[Bastion を使用する]** をクリックします。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./quickstart-host-portal.md)に関する記事をご覧ください。
1. **[Azure Bastion を使用して接続する]** ページで、 **[ユーザー名]** と **[SSH 秘密キー]** を入力します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-private-key.png" alt-text="SSH 秘密キー認証のスクリーンショット。":::
1. **[SSH 秘密キー]** のテキスト領域に秘密キーを入力します (または直接貼り付けます)。
1. **[接続]** を選択して VM に接続します。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>接続: 秘密キー ファイルを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Azure portal の仮想マシンの概要を示すスクリーンショット。[接続] が選択されています。" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. [Bastion] を選択してから **[Bastion を使用する]** をクリックします。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./quickstart-host-portal.md)に関する記事をご覧ください。
1. **[Azure Bastion を使用して接続する]** ページで、 **[ユーザー名]** と **[ローカル ファイルの SSH 秘密キー]** を入力します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-file.png" alt-text="SSH 秘密キー ファイルのスクリーンショット。":::

1. ファイルを参照し、 **[開く]** を選択します。
1. **[接続]** を選択して VM に接続します。 [接続] をクリックすると、この仮想マシンへの SSH 接続が Azure portal で直接開きます。 この接続は、仮想マシンのプライベート IP を使用して、Bastion サービスのポート 443 で HTML5 を介して行われます。

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>接続: Azure Key Vault に格納された秘密キーの使用

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Azure portal の仮想マシンの概要を示すスクリーンショット。[接続] が選択されています" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. [Bastion] を選択してから **[Bastion を使用する]** をクリックします。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](./quickstart-host-portal.md)に関する記事をご覧ください。
1. **[Azure Bastion を使用して接続する]** ページで、 **[ユーザー名]** を入力し、 **[Azure Key Vault の SSH 秘密キー]** を選択します。

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-key-vault.png" alt-text="[SSH Private Key from Azure Key Vault]\(Azure Key Vault の SSH 秘密キー\) を示すスクリーンショット。":::
1. **[Azure Key Vault]** ドロップダウンを選択し、SSH 秘密キーを保存したリソースを選択します。 

   * Azure Key Vault リソースを設定していない場合は、[Key Vault の作成](../key-vault/secrets/quick-create-powershell.md)に関する記事を参照し、SSH 秘密キーを新しい Key Vault シークレットの値として格納します。

   * Key Vault リソースに格納されているシークレットの **一覧表示** および **取得** のアクセス権を持っていることを確認します。 Key Vault リソースのアクセス ポリシーの割り当てと変更については、[Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する記事を参照してください。

     > [!NOTE]
     > **PowerShell** または **Azure CLI** エクスペリエンスを使用し、SSH 秘密キーをシークレットとして Azure Key Vault に保存してください。 Azure Key Vault ポータル エクスペリエンスを使用して秘密キーを保存すると、書式設定に支障をきたし、ログインに失敗します。 ポータル エクスペリエンスを使用して秘密キーをシークレットとして保存し、元の秘密キー ファイルにアクセスできなくなった場合は、「[SSH キーを更新する](../virtual-machines/extensions/vmaccess.md#update-ssh-key)」を参照して、新しい SSH キー ペアでターゲット VM へのアクセスを更新してください。
     >

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-stored.png" alt-text="Azure Key Vault を示すスクリーンショット。" lightbox="./media/bastion-connect-vm-ssh-linux/private-key-stored.png":::

1. **[Azure Key Vault Secret]\(Azure Key Vault シークレット\)** ドロップダウンを選択し、SSH 秘密キーの値が含まれている Key Vault シークレットを選択します。
1. **[接続]** を選択して VM に接続します。 **[接続]** をクリックすると、この仮想マシンへの SSH 接続が Azure portal で直接開きます。 この接続は、仮想マシンのプライベート IP を使用して、Bastion サービスのポート 443 で HTML5 を介して行われます。

## <a name="next-steps"></a>次のステップ

Azure Bastion の詳細については、[Bastion に関する FAQ](bastion-faq.md) をご覧ください。
