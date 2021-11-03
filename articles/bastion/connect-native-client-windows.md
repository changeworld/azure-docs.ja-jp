---
title: ネイティブ Windows クライアントと Azure Bastion を使用して仮想マシンに接続する
titleSuffix: Azure Bastion
description: Bastion とネイティブ Windows クライアントを使用して、Windows コンピューターから仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e6b0caa3c5540a082bf20e3ed2a11389a4188e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092771"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer"></a>Bastion と Windows コンピューター上のネイティブ クライアントを使用して、仮想マシンに接続する

Azure Bastion では、Windows ローカル コンピューター上のネイティブ クライアントを使用して Azure のターゲット VM に接続できるようになりました。 この機能を使用すると、Azure CLI を使用して Bastion 経由でターゲット VM に接続し、サインイン オプションを拡張して、ローカル SSH キー ペア、ローカル ユーザー名とパスワード、Azure Active Directory (Azure AD) を含めることができます。 この記事では、必要な設定を使用して Bastion を構成し、VNet 内の VM に接続する方法を説明します。 詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

> [!NOTE]
> この構成には、Azure Bastion の Standard SKU が必要です。
>

現在、この機能には次の制限があります。

* カスタム ポートまたはプロトコルを使用してターゲット VM にサインインする方法は、ネイティブ クライアント サポートではまだ使用できません。 カスタム ポートまたはプロトコルを使用して Bastion 経由でターゲット VM にサインインする場合は、Azure portal エクスペリエンスを使用します。

* この機能では、Azure Key Vault に格納されている SSH プライベート キーを使用したサインインはサポートされていません。 SSH キー ペアを使用して Linux VM にログインする前に、ローカル コンピューター上のファイルにプライベート キーをダウンロードします。

## <a name="prerequisites"></a><a name="prereq"></a>前提条件

開始する前に、次の条件を満たしていることを確認します。

* 最新バージョンの CLI コマンドがインストールされています。 CLI コマンドのインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。
* Azure 仮想ネットワーク。
* 仮想ネットワーク内の仮想マシン

## <a name="configure-bastion"></a>Bastion を構成する

お使いの環境に関連する手順に従います。

### <a name="to-modify-an-existing-bastion-host"></a>既存の bastion ホストを変更するには

VNet に Bastion を既に構成している場合は、次の設定を変更します。

1. Bastion リソースの **[構成]** ページに移動します。 [SKU] が **[Standard]** になっていることを確認します。 表示されていない場合は、ドロップダウンから **[Standard]** に変更します。
1. **[ネイティブ クライアント サポート]** のチェック ボックスをオンにし、変更を適用します。

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="[ネイティブ クライアント サポート] ボックスをオンにして既存のホストを更新する場合の設定。" lightbox="./media/connect-native-client-windows/update-host-expand.png":::
1. CLI コマンドをインストールします。 最新バージョンの CLI コマンドをインストールする手順については、「[前提条件](#prereq)」を参照してください。

### <a name="to-configure-a-new-bastion-host"></a>新しい bastion ホストを構成するには

bastion ホストをまだ構成していない場合は、「[bastion ホストを作成する](tutorial-create-host-portal.md#createhost)」を参照してください。 bastion ホストの構成時に、次の設定を指定します。

1. **[基本]** タブの **[インスタンスの詳細] - -> [層]** -で **[Standard]** を選択し、Standard SKU を使用して bastion ホストを作成します。

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="Standard SKU が選択されている新しい bastion ホストの場合の設定。" lightbox="./media/connect-native-client-windows/standard.png":::
1. **[詳細設定]** タブ で、 **[Native Client サポート]** のチェック ボックスをオンにします。

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="[Native Client サポート] ボックスがオンの新しい bastion ホストの設定。" lightbox="./media/connect-native-client-windows/new-host-expand.png":::
1. CLI コマンドをインストールします。 最新バージョンの CLI コマンドをインストールする手順については、「[前提条件](#prereq)」を参照してください。

## <a name="verify-roles-and-ports"></a>ロールとポートを確認する

接続するために、次のロールとポートが構成されていることを確認します。

### <a name="required-roles"></a>必要なロール

* 仮想マシンに対する閲覧者ロール。
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
* Azure Bastion リソースに対する閲覧者ロール。

### <a name="ports"></a>Port

ネイティブ クライアント サポートを使用して Windows VM に接続するには、Windows VM で次のポートを開いておく必要があります。

* 受信ポート: RDP (3389)

ネイティブ クライアント サポートを使用して Linux VM に接続するには、Linux VM で次のポートを開いている必要があります。

* 受信ポート:SSH (22)

カスタム ポートを使用してターゲット VM に接続する場合は、代わりに Azure portal の手順を使用してください。

## <a name="connect-to-a-vm"></a><a name="connect"></a>VM への接続

このセクションでは、仮想マシンへの接続する方法について説明します。 接続する仮想マシンの種類に対応する手順を使用します。

### <a name="connect-to-a-linux-vm"></a>Linux VM に接続する

1. Azure アカウントにサインインし、Bastion リソースを含むサブスクリプションを選択します。

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

1. 次のいずれかのオプションを使用して、ターゲットの Linux VM にログインします。

   * 参加している Azure AD 参加済み (AADJ) VM にログインする場合は、次の例を使用します。 Azure AD を使用して Azure Linux VM にログインする方法の詳細については、「[Azure Linux VM と Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)」を参照してください。

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * ローカルのユーザー名とパスワードを使用してログインする場合は、次の例を使用します。

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>"
      ```

   * SSH キー ペアを使用してログインする場合は、次の例を使用します。

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### <a name="connect-to-a-windows-vm"></a>Windows VM に接続する

1. Azure アカウントにサインインし、Bastion リソースを含むサブスクリプションを選択します。

   ```azurecli-interactive
   az login
   az account list
   az account set –subscription "<subscription ID>"

1. Log into your target Windows VM using one of the following options:

   * If you are logging into an Azure AD-joined (AADJ) VM, use the following command. To learn more about how to use Azure AD to log into your Azure Windows VMs, see [Azure Windows VMs and Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "AAD"
      ```

   * ローカルのユーザー名とパスワードを使用してログインする場合:

      ```azurecli-interactive
      az network bastion rdp "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>" --password "<Password>" 
      ```

1. ターゲット VM にログインすると、ローカル コンピューター上のネイティブ クライアントが VM セッション (RDP セッションの場合は **mstc**、SSH セッションの場合は **az ssh**) で開きます。

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
