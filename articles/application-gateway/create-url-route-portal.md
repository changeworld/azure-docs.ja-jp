---
title: チュートリアル - URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して、アプリケーション ゲートウェイと仮想マシン スケール セットの URL パスベースのルーティング規則を作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134884"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用してパス ベースのルーティング規則のあるアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](overview.md)を作成するときに、Azure Portal を使用して [URL パス ベースのルーティング規則](url-route-overview.md)を構成できます。 このチュートリアルでは、仮想マシンを使用してバックエンド プールを作成します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するようにルーティング規則を作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * パス ベースのルーティング規則の作成

![URL ルーティングの例](./media/create-url-route-portal/scenario.png)

好みに応じて、[Azure CLI](tutorial-url-route-cli.md) または [Azure PowerShell](tutorial-url-route-powershell.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure portal の左上にある **[新規]** を選択します。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. 次のアプリケーション ゲートウェイの値を入力します。

   - *myAppGateway* - アプリケーション ゲートウェイの名前です。
   - *myResourceGroupAG* - 新しいリソース グループの名前です。

     ![新しいアプリケーション ゲートウェイの作成](./media/create-url-route-portal/application-gateway-create.png)

4. 他の設定は既定値をそのまま使用し、**[OK]** を選択します。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順に選択し、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。

     ![Create virtual network](./media/create-url-route-portal/application-gateway-vnet.png)

6. **[OK]** を選択して、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順に選択し、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** を選択します。
8. リスナーの構成には既定値をそのまま使用し、Web アプリケーション ファイアウォールは無効のままにして、**[OK]** を選択します。
9. 概要ページで設定を確認し、**[OK]** を選択して、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で **[myVNet]** を選びます。
2. **[サブネット]** を選択し、**[サブネット]** を選択します。

    ![サブネットの作成](./media/create-url-route-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」と入力し、**[OK]** を選択します。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 3 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。

1. **[新規]** を選択します。
2. **[Compute]** をクリックし、おすすめのリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *myVM1* - 仮想マシンの名前です。
    - *azureuser* - 管理者のユーザー名です。
    - *Azure123456!* パスワードです。
    - **[既存のものを使用]**、*[myResourceGroupAG]* の順に選択します。

4. **[OK]** を選択します。
5. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** を選択します。
6. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。 
7. **[無効]** を選択して、ブート診断を無効にします。
8. **[OK]** を選択し、概要ページの設定を確認して、**[作成]** を選択します。

### <a name="install-iis"></a>IIS のインストール

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/create-url-route-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. さらに 2 つの仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 名前および Set-AzVMExtension の VMName の値として、*myVM2* と *myVM3* という名前を入力します。

## <a name="create-backend-pools-with-the-virtual-machines"></a>仮想マシンでのバックエンド プールの作成

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。
2. **[バックエンド プール]** を選択します。 既定のプールがアプリケーション ゲートウェイで自動的に作成されます。 **[appGatewayBackendPool]** を選択します。
3. **[ターゲットの追加]** を選択して、*myVM1* を appGatewayBackendPool に追加します。

    ![バックエンド サーバーの追加](./media/create-url-route-portal/application-gateway-backend.png)

4. **[保存]** を選択します。
5. **[バックエンド プール]** を選択し、**[追加]** を選択します。
6. *imagesBackendPool* という名前を入力し、**[ターゲットの追加]** を使用して *myVM2* を追加します。
7. **[OK]** を選択します。
8. **[追加]** を再度選択して、*videoBackendPool* という名前の別のバックエンド プールを追加し、それに *myVM3* を追加します。

## <a name="create-a-backend-listener"></a>バックエンド リスナーの作成

1. **[リスナー]** を選択し、**[Basic]\(基本\)** を選択します。
2. 名前として「*myBackendListener*」を、フロントエンド ポートの名前として「*myFrontendPort*」を、リスナーのポートとして「*8080*」を入力します。
3. **[OK]** を選択します。

## <a name="create-a-path-based-routing-rule"></a>パス ベースのルーティング規則の作成

1. **[規則]** を選択し、**[パス ベース]** を選択します。
2. 名前として「*rule2*」を入力します。
3. 最初のパスの名前として「*イメージ*」と入力します。 パスには「*/images/*\*」と入力します。 バックエンド プールとして **[imagesBackendPool]** を選択します。
4. 2 番目のパスの名前として「*ビデオ*」と入力します。 パスには「*/video/*\*」と入力します。 バックエンド プールとして **[videoBackendPool]** を選択します。

    ![パス ベース ルールの作成](./media/create-url-route-portal/application-gateway-route-rule.png)

5. **[OK]** を選択します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]** を選択し、**[myAGPublicIPAddress]** を選択します。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 http://40.121.222.19 など。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/create-url-route-portal/application-gateway-iistest.png)

3. URL を http://&lt;ip-address&gt;:8080/images/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

    ![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. URL を http://&lt;ip-address&gt;:8080/video/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

    ![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。 

リソース グループを削除するには:

1. Azure portal の左側のメニューにある **[リソース グループ]** を選択します。
2. **[リソース グループ]** ページで、リストの **myResourceGroupAG** を探して選択します。
3. **[リソース グループ] ページ**で、**[リソース グループの削除]** を選択します。
4. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「*myResourceGroupAG*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Application Gateway で実行できる操作の詳細を確認する](application-gateway-introduction.md)
