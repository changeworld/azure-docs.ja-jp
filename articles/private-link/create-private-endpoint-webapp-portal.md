---
title: Azure プライベート エンドポイントを使用して Web アプリにプライベートで接続する
description: この記事では、Azure プライベート エンドポイント を使用して Web アプリにプライベートに接続する方法について説明します。
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772840"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Azure プライベート エンドポイントを使用して Web アプリにプライベートで接続する

Azure プライベート エンドポイントは、Azure Private Link の基本的な構成要素です。 プライベート エンドポイントを使用することで、Web アプリにプライベートに接続することができます。 この記事では、プライベート エンドポイントを使用して Web アプリをデプロイした後、仮想マシン (VM) から Web アプリに接続する方法について説明します。

詳細については、[Azure Web アプリでのプライベート エンドポイントの使用][privateendpointwebapp]に関するページを参照してください。

> [!Note]
> プライベート エンドポイントは、PremiumV2 レベル、PremiumV3 レベルの Windows Web アプリ、Linux Web アプリ、Azure Functions Premium プラン (エラスティック Premium プランとも呼ばれます) に対するパブリック リージョンで利用できます。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

始める前に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-network-and-virtual-machine"></a>仮想ネットワークと仮想マシンを作成する

このセクションでは、プライベート エンドポイントを介して Web アプリにアクセスするために使用する VM をホストする仮想ネットワークとサブネットを作成します。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークとサブネットを作成するには、次のようにします。

1. 左側のペインで、 **[+ リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** を選択します。

1. **[仮想ネットワークの作成]** ペインで **[基本]** タブを選択し、次のような情報を入力します。

   > [!div class="mx-imgBorder"]
   > ![Azure portal での [仮想ネットワークの作成] ペインのスクリーンショット。][1]

1. **[IP アドレス]** タブを選択し、次のような情報を入力します。

   > [!div class="mx-imgBorder"]
   > ![[仮想ネットワークの作成] ペインの [IP アドレス] タブのスクリーンショット。][2]

1. **[サブネット]** セクションで **[サブネットの追加]** を選択し、ここに表示されている情報を入力して、 **[追加]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[サブネットの追加] ペインのスクリーンショット。][3]

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 検証が成功したら、 **[作成]** を選択します。

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

仮想マシンを接続するには、次のようにします。

1. Azure portal の左側のペインで、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。

1. **[仮想マシンの作成] - [基本]** ペインで、次に示す情報を入力します。

   > [!div class="mx-imgBorder"]
   > ![[仮想マシンの作成] ペインのスクリーンショット。][4]

1. **ディスク** を選択します。

1. **[ディスク]** ペインで、既定の設定をそのまま使用し、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。

1. **[ネットワーク]** ペインで、次に示す情報を入力します。

   > [!div class="mx-imgBorder"]
   > ![[仮想マシンの作成] ペインの [ネットワーク] タブのスクリーンショット。][5]

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 検証が成功したら、 **[作成]** を選択します。

## <a name="create-a-web-app-and-a-private-endpoint"></a>Web アプリとプライベート エンドポイントを作成する

このセクションでは、プライベート エンドポイントを使用するプライベート Web アプリを作成します。

> [!Note]
> プライベート エンドポイント機能は、PremiumV2 および PremiumV3 レベルでのみ使用できます。

### <a name="create-the-web-app"></a>Web アプリの作成

1. Azure portal の左側のペインで、 **[リソースの作成]**  >  **[Web]**  >  **[Web アプリ]** を選択します。

1. **[Web アプリ]** ペインで **[基本]** タブを選択し、次のような情報を入力します。

   > [!div class="mx-imgBorder"]
   > ![[Web アプリ] ペインの [基本] タブのスクリーンショット。][6]

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 検証が成功したら、 **[作成]** を選択します。

### <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する

1. Web アプリのプロパティの **[設定]** で **[ネットワーク]** を選択し、**[プライベート エンドポイント接続]** で **[プライベート エンドポイント接続の構成]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![Web アプリの [ネットワーク] ペインの [プライベート エンドポイント接続の構成] リンクのスクリーンショット。][7]

1. **[プライベート エンドポイント接続]** ウィザードで、 **[追加]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[プライベート エンドポイント接続] ウィザードの [追加] ボタンのスクリーンショット。][8]

1. **[サブスクリプション]** 、 **[仮想ネットワーク]** 、 **[サブネット]** の各ドロップダウン リストで正しい情報を選択し、 **[OK]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[プライベート エンドポイントの追加] ウィンドウのスクリーンショット。][9]

1. プライベート エンドポイントの作成の進行状況を監視します。

   > [!div class="mx-imgBorder"]
   > ![プライベート エンドポイントの追加の進行状況のスクリーンショット。][10]
   > ![新しく作成されたプライベート エンドポイントのスクリーンショット。][11]

## <a name="connect-to-the-vm-from-the-internet"></a>インターネットから VM に接続する

1. Azure portal の **[検索]** ボックスに、「**myVm**」と入力します。
1. **[接続]** を選択し、次に **[RDP]** を選択します。

   > [!div class="mx-imgBorder"]
   > !["MyVM" ペインの [RDP] ボタンのスクリーンショット。][12]

1. **[RDP で接続する]** ペインで **[RDP ファイルのダウンロード]** を選択します。  

   > [!div class="mx-imgBorder"]
   > ![[RDP で接続する] ペインの [RDP ファイルのダウンロード] ボタンのスクリーンショット。][13]

   Azure によってリモート デスクトップ プロトコル (RDP) ファイルが作成され、お使いのコンピューターにダウンロードされます。   

1. ダウンロードされた RDP ファイルを開きます。

   a. プロンプトで **[接続]** を選択します。  
   b. VM の作成時に指定したユーザー名とパスワードを入力します。

     > [!Note]
     > これらの資格情報を使用するには、 **[その他]**  >  **[別のアカウントを使用する]** を選択することが必要な場合があります。

1. **[OK]** を選択します。

   > [!Note]
   > サインイン プロセス中に証明書の警告が表示される場合は、 **[はい]** または **[続行]** を選択します。

1. VM のデスクトップ ウィンドウが表示されたら、それを最小化してローカル デスクトップに戻ります。

## <a name="access-the-web-app-privately-from-the-vm"></a>VM から Web アプリにプライベートにアクセスする

このセクションでは、プライベート エンドポイントを使用して、プライベートで Web アプリに接続します。

1. プライベート エンドポイントのプライベート IP を取得するには、 **[検索]** ボックスに「**プライベート リンク**」と入力し、結果の一覧で **[Private Link]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![検索結果の一覧での [Private Link] リンクのスクリーンショット。][14]

1. プライベート リンク センターの左側のペインで **[プライベート エンドポイント]** を選択し、すべてのプライベート エンドポイントを表示します。

   > [!div class="mx-imgBorder"]
   > ![プライベート リンク センターでのプライベート エンドポイント一覧のスクリーンショット。][15]

1. Web アプリとサブネットにリンクしているプライベート エンドポイントを選択します。

   > [!div class="mx-imgBorder"]
   > ![プライベート エンドポイントのプロパティ ペインのスクリーンショット。][16]

1. プライベート エンドポイントのプライベート IP と、Web アプリの完全修飾ドメイン名 (FQDN) をコピーします。 前の例で、プライベート ID は *`webappdemope.azurewebsites.net 10.10.2.4`* です。

1. **myVM** のペインで、パブリック IP を使用して Web アプリにアクセスできないことを確認します。 これを行うには、ブラウザーを開き、Web アプリの名前を貼り付けます。 ページに "エラー 403 - アクセス不可" のメッセージが表示されます。

   > [!div class="mx-imgBorder"]
   > !["エラー 403 - アクセス不可" エラー ページのスクリーンショット。][17]

   DNS の場合、次のいずれかを行います。
 
   - Azure DNS プライベート ゾーン サービスを使用します。  

     a. *`privatelink.azurewebsites.net`* という名前の DNS プライベート ゾーンを作成し、それを仮想ネットワークにリンクします。  
     b. プライベート エンドポイントの IP アドレスを使用して、2 つの A レコード (つまり、アプリ名とサービス コントロール マネージャー (SCM) 名) を作成します。  
     > [!div class="mx-imgBorder"]
     > ![DNS プライベート ゾーン レコードのスクリーンショット。][21]  

   - VM の *hosts* ファイルを使用します。  

     a. hosts エントリを作成し、エクスプローラーを開いて、*hosts* ファイルを見つけます。  
     > [!div class="mx-imgBorder"]
     > ![エクスプローラーでの hosts ファイルを示すスクリーンショット。][18]  
     b. テキスト エディターで *hosts* ファイルを編集し、Web アプリのプライベート IP アドレスとパブリック名を含むエントリを追加します。  
     > [!div class="mx-imgBorder"]
     > ![hosts ファイルのテキストのスクリーンショット。][19]  
     c. ファイルを保存します。

1. ブラウザーで、Web アプリの URL を入力します。

   > [!div class="mx-imgBorder"]
   > ![Web アプリが表示されているブラウザーのスクリーンショット。][20]

これで、プライベート エンドポイントを通して Web アプリにアクセスしています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート エンドポイント、Web アプリ、VM を使い終えたら、リソース グループとそこに含まれるすべてのリソースを削除します。

1. Azure portal の **[検索]** ボックスに「**ready-rg**」と入力し、結果の一覧で **ready-rg** を選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に「**ready-rg**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク上の VM、Web アプリ、およびプライベート エンドポイントを作成しました。 インターネットから VM に接続し、Private Link を使用して Web アプリと安全に通信しました。 

プライベート エンドポイントの詳細については、「[Azure プライベート エンドポイントとは][privateendpoint]」を参照してください。

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
