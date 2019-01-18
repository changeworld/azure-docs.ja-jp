---
title: Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: b368ef3b5503d90b0eb928113e8154aabca9c04a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157141"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Azure Portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

Azure Portal を使用して、[Web アプリケーション ファイアウォール](application-gateway-web-application-firewall-overview.md) (WAF) のある[アプリケーション ゲートウェイ](application-gateway-introduction.md)を作成することができます。 WAF は、[OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) ルールを使用してアプリケーションを保護します。 こうしたルールには、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの攻撃に対する保護が含まれます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * WAF 対応のアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * ストレージ アカウントの作成と診断の構成

![Web アプリケーション ファイアウォールの例](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインする

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. 次のアプリケーション ゲートウェイの値を入力します。

    - *myAppGateway* - アプリケーション ゲートウェイの名前です。
    - *myResourceGroupAG* - 新しいリソース グループの名前です。
    - アプリケーション ゲートウェイのレベルとして *[WAF]* を選択します。

    ![新しいアプリケーション ゲートウェイの作成](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順にクリックし、次の仮想ネットワークの値を入力します。

    - *myVNet* - 仮想ネットワークの名前です。
    - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
    - *myAGSubnet* - サブネットの名前です。
    - *10.0.0.0/24* - サブネットのアドレス空間です。

    ![Create virtual network](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順にクリックし、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
8. リスナーの構成は既定値をそのまま使用し、Web アプリケーション ファイアウォールは無効のままにして、**[OK]** をクリックします。
9. 概要ページで設定を確認し、**[OK]** をクリックして、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myVNet]** をクリックします。
2. **[サブネット]**、**[サブネット]** の順にクリックします。

    ![サブネットの作成](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」を入力し、**[OK]** をクリックします。

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. **[新規]** をクリックします。
2. **[コンピューティング]** をクリックし、注目のリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *myVM* - 仮想マシンの名前です。
    - *azureuser* - 管理者のユーザー名です。
    - *Azure123456!* パスワードです。
    - **[既存のものを使用]**、*[myResourceGroupAG]* の順に選択します。

4. Click **OK**.
5. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
6. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。 
7. **[無効]** をクリックして、ブート診断を無効にします。
8. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。

### <a name="install-iis"></a>IIS のインストール

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 2 番目の仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 その名前および AzureRmVMExtension の VMName として「*myVM2*」を入力します。

### <a name="add-backend-servers"></a>バックエンド サーバーの追加

1. **[すべてのリソース]**、**[myAppGateway]** の順にクリックします。
2. **[バックエンド プール]** をクリックします。 既定のプールがアプリケーション ゲートウェイで自動的に作成されます。 **[appGatewayBackendPool]** をクリックします。
3. **[ターゲットの追加]** をクリックして、作成した各仮想マシンをバックエンド プールに追加します。

    ![バックエンド サーバーの追加](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. **[Save]** をクリックします。

## <a name="create-a-storage-account-and-configure-diagnostics"></a>ストレージ アカウントの作成と診断の構成

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

このチュートリアルでは、アプリケーション ゲートウェイは、検出および防止の目的で、ストレージ アカウントを使用してデータを格納します。 Log Analytics またはイベント ハブを使用して、データを記録することもできます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
3. ストレージ アカウントの名前を入力し、リソース グループでは **[既存のものを使用]** を選択して、**[myResourceGroupAG]** を選択します。 この例では、ストレージ アカウント名は *myagstore1* です。 他の設定は既定値をそのまま使用し、**[作成]** をクリックします。

## <a name="configure-diagnostics"></a>診断の構成

ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog、および ApplicationGatewayFirewallLog ログにデータが記録されるように診断を構成します。

1. 左側のメニューで、**[すべてのリソース]** をクリックし、*[myAppGateway]* を選択します。
2. [監視] の **[診断ログ]** をクリックします。
3. **[診断設定の追加]** をクリックします。
4. 診断設定の名前として「*myDiagnosticsSettings*」と入力します。
5. **[ストレージ アカウントへのアーカイブ]** を選択し、**[構成]** をクリックして、前に作成した *myagstore1* ストレージ アカウントを選択します。
6. 収集および保持するためのアプリケーション ゲートウェイ ログを選択します。
7. **[Save]** をクリックします。

    ![診断の構成](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. [概要] 画面で、アプリケーション ゲートウェイのパブリック IP アドレスを見つけます。 **[すべてのリソース]**、**[myAGPublicIPAddress]** の順にクリックします。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. パブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

    ![アプリケーション ゲートウェイのテスト](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * WAF 対応のアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * ストレージ アカウントの作成と診断の構成

アプリケーション ゲートウェイとその関連リソースの詳細を確認するには、ハウツー記事に進みます。