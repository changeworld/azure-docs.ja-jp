---
title: Azure Functions を Azure 仮想ネットワークに統合する
description: 関数を Azure 仮想ネットワークに接続する方法を示すステップ バイ ステップ チュートリアル
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 042d41e9125e5d4fa0af04fd6edeba5f0b33123a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001586"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>関数アプリを Azure 仮想ネットワークに統合する
このステップ バイ ステップ チュートリアルでは、Azure Functions を使用して Azure VNET 内のリソースに接続する方法を示します。 

このチュートリアルでは、インターネットでアクセスできない非公開 VNET 内の VM 上に WordPress サイトをデプロイします。 その後、インターネットと VNET の両方にアクセス可能な関数をデプロイします。 その関数を使用して、VNET 内にデプロイした WordPress サイトのリソースにアクセスします。

システムのしくみ、トラブルシューティング、高度な構成の詳細については、「[アプリを Azure 仮想ネットワークに統合する](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)」のドキュメントを参照してください。 専用プランの Azure Functions には Web Apps と同じホスティング機能があるため、そのドキュメントに記載されているすべての機能と制限事項が Functions にも適用されます。

## <a name="topology"></a>トポロジ
 ![VNet 統合 UI][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>VNET 内に VM を作成する

最初に、VNET 内で WordPress を実行する事前構成済みの VM を作成します。 

VM 上の WordPress が選択された理由は、それが VNET 内にデプロイできる最も安価なリソースの 1 つであることです。 このシナリオは、REST API、他の Azure Service、App Service Environment など、VNET 内のあらゆるリソースにも使用できることに注意してください。

1.  Azure portal にアクセスします
2.  [リソースの作成] ブレードを開いて新しいリソースを追加します
3.  "[Wordpress LEMP7 Max Performance on CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" を検索し、その作成ブレードを開きます 
4.  作成ブレードで VM に次の情報を構成します:
    1.  チュートリアルの最後に簡単にリソースをクリーンアップするために、この VM 用の新しいリソース グループを作成します。 ここでは、リソース グループに "Function-VNET-Tutorial" という名前を付けています
    1.  仮想マシンに一意の名前を付けます。 ここでは "VNET-Wordpress" という名前にしています
    1.  最も近いリージョンを選択します
    1.  サイズとして B1s (1 vcpu、1 GB メモリ) を選択します
    1.  管理者アカウントのパスワード認証を選択し、一意のユーザー名とパスワードを入力します。 このチュートリアルでは、トラブルシューティングが必要な場合を除き、VM にサインインする必要はありません。
    
        <img src="./media/functions-create-vnet/create-VM-1.png" width="700">

1. [ネットワーク] タブに移動し、次の情報を入力します。
    1.  新しい仮想ネットワークを作成します
    1.  必要なプライベート アドレスの範囲と、そのアドレス範囲内のサブネットを入力します。 サブネットのサイズによって、App Service プランで使用できる VM の数が決定します。 IP アドレスの割り当てとサブネット化を初めて行う場合は、[基本事項に関するドキュメント](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)を参照してください。 このシナリオにおいて IP アドレスの割り当てとサブネット化は重要であるため、オンラインで何本かの記事を読み、何本かのビデオを視聴して、意味を理解することをお勧めします。 
        1. この例では、10.10.0.0/16 ネットワークと 10.10.1.0/24 のサブネットを使用するように選択しました。 オーバープロビジョニングを行って /16 サブネットを使用することを選択しました。理由は、10.10.0.0/16 ネットワーク内でどのサブネットが利用可能かを計算するのが簡単だからです。
        
        <img src="./media/functions-create-vnet/create-VM-2.png" width="700">

1. [ネットワーク] タブに戻り、パブリック IP を "なし" に設定します。 これにより、VNET のみにアクセス可能な VM がデプロイされます。
       
    <img src="./media/functions-create-vnet/create-VM-2.1.png" width="700">

7. VM を作成します。 これは 5 分ほどかかります。
8. VM が作成されたら、[ネットワーク] タブに移動し、プライベート IP アドレスをメモしておいてください。後で使用します。 その VM にパブリック IP はありません。

    ![14]

以上で、完全に仮想ネットワーク内にデプロイされた WordPress サイトが表示されます。 このサイトにはパブリック インターネットからはアクセスできません。

## <a name="create-a-dedicated-function-app"></a>専用の関数アプリを作成する

次の手順では、Standard 以上の App Service プラン内に関数アプリを作成します。 従量課金プランの Function Apps では VNET 統合がサポートされていないことに注意してください。

1. Azure portal にアクセスします
2. [リソースの作成] ブレードを開いて新しいリソースを追加します
3. [Serverless Function App]\(サーバーレス関数アプリ\) を選択します
4. 作成ブレードには通常入力するすべての情報を入力しますが、例外が 1 つあります。
    1. Standard 以上の App Service プラン レベルを選択します。

        <img src="./media/functions-create-vnet/Create-App-Service-Plan.PNG" width="300">
    
1. 完了した作成のブレードは、次のようになります。

    <img src="./media/functions-create-vnet/Create-Function-App.png" width="300">


## <a name="connect-your-function-app-to-your-vnet"></a>関数アプリを VNET に接続する

ここまでで、VNET 内の多数のファイルをホストしている、WordPress サイトができました。次に、関数アプリをその VNET に接続する必要があります。

1.  ポータルで、前の手順の関数アプリに対して **[プラットフォーム機能]** を選択し、**[ネットワーク]** を選択します
1.  VNet 統合の下の **[クリックして構成する]** を選択します

    <img src="./media/functions-create-vnet/Networking-1.png" width="450">

1. VNET 統合ページで、**[VNet の追加 (プレビュー)]** を選択します

    <img src="./media/functions-create-vnet/Networking-2.png" width="600"> 
    
1.  使用する関数と App Service プランの新しいサブネットを作成します。 サブネットのサイズによって、ご利用の App Service プランに追加できる VM の合計数が制限されることに注意してください。 VNET では、VNET 内のサブネット間のトラフィックが自動的にルーティングされるため、使用する関数がご利用の VM とは別のサブネット内にあっても問題ありません。 
    
    <img src="./media/functions-create-vnet/Networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>VNET 内のリソースにアクセスする関数を作成する

関数アプリから WordPress サイトを含む VNET にアクセスできるようになったので、関数を使用してそのファイルにアクセスし、そのファイルをユーザーに提供します。 この例では、WordPress サイトを API として使用し、関数プロキシを呼び出し元関数として使用します。これらは両方とも簡単に設定して視覚化できます。 同じくらい簡単に、VNET 内にデプロイされた他の任意の API、および VNET 内にデプロイされたその API への API 呼び出しを行うコードを含む別の関数を使用できます。 VNET 内にデプロイされた SQL サーバーは、その好例です。

1. ポータルで、前の手順の関数アプリを開きます
1. **[プロキシ]** > **[+**] を選択してプロキシを作成します

    <img src="./media/functions-create-vnet/New-Proxy.png" width="250">

1. プロキシ名とルートを構成します。 ここではルートとして /plant を選択します。
1. 前に確認した WordPress サイトの IP を入力し、バックエンド URL を `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg` に設定します
    
    <img src="./media/functions-create-vnet/Create-Proxy.png" width="900">

ここで、ブラウザーの新しいタブにバックエンド URL を貼り付けて直接アクセスしようとした場合、ページはタイムアウトになります。使用する WordPress サイトは VNET のみに接続されており、インターネットには接続されていないため、これは想定内です。 プロキシの URL をブラウザーに貼り付けると、VNET 内の WordPress サイトから取得された美しい植物の写真が表示されます。 

使用する関数アプリは、インターネットと VNET の両方に接続されています。 プロキシはパブリック インターネット経由で要求を受信し、その要求を仮想ネットワークに転送するシンプルな HTTP プロキシとして機能します。 その後、プロキシによって中継された応答がパブリック インターネット経由で返されます。 

<img src="./media/functions-create-vnet/Plant.png" width="900">

## <a name="next-steps"></a>次の手順

App Service プランで実行される Azure Functions は、Web Apps と同じサービス上で実行されるため、Web Apps に関するすべてのドキュメントは専用 Functions に適用されます。

1. [App Service または Functions との VNET 統合についての詳細情報](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Azure での VNET についての詳細情報](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [App Service Environment でのネットワーク機能と制御を可能にする](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [関数プロキシについての詳細情報](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/Create-Function-App.png
[3]: ./media/functions-create-vnet/Create-App-Service-Plan.PNG
[4]: ./media/functions-create-vnet/configure-VNET.png
[5]: ./media/functions-create-vnet/create-VM-1.png
[6]: ./media/functions-create-vnet/create-VM-2.png
[7]: ./media/functions-create-vnet/create-VM-2.1.png
[8]: ./media/functions-create-vnet/Networking-1.png
[9]: ./media/functions-create-vnet/Networking-2.png
[10]: ./media/functions-create-vnet/Networking-3.png
[11]: ./media/functions-create-vnet/New-Proxy.png
[12]: ./media/functions-create-vnet/Create-Proxy.png
[14]: ./media/functions-create-vnet/VM-Networking.png
