---
title: Azure Functions を Azure 仮想ネットワークに統合する
description: 関数を Azure 仮想ネットワークに接続する方法を説明するステップ バイ ステップ チュートリアル
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002795"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>関数アプリを Azure 仮想ネットワークに統合する

このチュートリアルでは、Azure Functions を使用して Azure 仮想ネットワーク内のリソースに接続する方法について説明します。

このチュートリアルでは、インターネットからアクセスできない仮想ネットワーク内の VM 上に WordPress サイトをデプロイします。 その後、インターネットと仮想ネットワークの両方にアクセス可能な関数をデプロイします。 その関数を使用して、仮想ネットワーク内にデプロイした WordPress サイトからリソースにアクセスします。

システムのしくみ、トラブルシューティング、高度な構成の詳細については、「[アプリを Azure 仮想ネットワークに統合する](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)」を参照してください。 Premium プランの Azure Functions には Web アプリと同じホスティング機能があるため、その記事に記載されているすべての機能と制限事項が Functions にも適用されます。

## <a name="topology"></a>トポロジ

 ![仮想ネットワーク統合の UI][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>仮想ネットワーク内に VM を作成する

まず、仮想ネットワーク内で WordPress を実行する、構成済みの VM を作成します。 

VM 上の WordPress を選択したのは、それが仮想ネットワーク内にデプロイできる最も安価なリソースの 1 つであるためです。 このシナリオは、REST API、App Service Environment、他の Azure サービスなど、仮想ネットワーク内のあらゆるリソースにも使用できることに注意してください。

1. Azure Portal にアクセスします。
2. **[リソースの作成]** ブレードを開いて新しいリソースを追加します。
3. "[WordPress LEMP7 Max Performance on CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" を検索し、その作成ブレードを開きます。 
4. **[基本]** タブで、次の情報を使用して VM を構成します。
    1. チュートリアルの最後に簡単にリソースをクリーンアップするために、この VM 用の新しいリソース グループを作成します。 ここでは、例として、"Function-VNET-Tutorial" を使用します。
    1. 仮想マシンに一意の名前を付けます。 例として "VNET-Wordpress" を使用します。
    1. 最も近いリージョンを選択します。
    1. サイズとして B1s (1 vCPU、1 GB メモリ) を選択します。
    1. 管理者アカウントのパスワード認証を選択し、一意のユーザー名とパスワードを入力します。 このチュートリアルでは、トラブルシューティングが必要な場合を除き、VM にサインインする必要はありません。
    
        ![VM を作成するための [基本] タブ](./media/functions-create-vnet/create-vm-1.png)

1. **[ネットワーク]** タブに移動し、次の情報を入力します。
    1.  新しい仮想ネットワークを作成します。
    1.  プライベート アドレスの範囲と、そのアドレス範囲内のサブネットを入力します。 サブネットのサイズによって、App Service プランで使用できる VM の数が決定します。 IP アドレスの割り当てとサブネット化を初めて行う場合は、[基礎知識に関するドキュメント](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)を参照してください。 このシナリオにおいて IP アドレスの割り当てとサブネット化は重要であるため、オンラインでいくつかの記事を読み、何本かのビデオを視聴して、意味を理解することをお勧めします。 
    
        この例では、10.10.0.0/16 ネットワークと 10.10.1.0/24 のサブネットを使用するように選択しました。 オーバープロビジョニングを行って /16 サブネットを使用します。理由は、10.10.0.0/16 ネットワーク内でどのサブネットが利用可能かを計算するのが簡単だからです。
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. **[ネットワーク]** タブに戻り、パブリック IP を **[なし]** に設定します。 このステップにより、仮想ネットワークのみにアクセス可能な VM がデプロイされます。
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. VM を作成します。 このプロセスには約 5 分かかります。
8. VM が作成されたら、**[ネットワーク]** タブに移動し、プライベート IP アドレスをメモしておいてください。後で使用します。 その VM にパブリック IP はありません。

    ![14]

これで、完全に仮想ネットワーク内にデプロイされた WordPress サイトが表示されます。 このサイトにはパブリック インターネットからはアクセスできません。

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium プランの関数アプリを作成する

次の手順では、Premium プランで関数アプリを作成します。 Premium プランは、専用の App Service プランのすべてのベネフィットを備えたサーバーレス スケールを提供します。 従量課金プランで作成された関数アプリでは、仮想ネットワークの統合はサポートされません。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>関数アプリを仮想ネットワークに接続する

仮想ネットワーク内からファイルをホストしている WordPress サイトでは、仮想ネットワークに関数アプリを接続できるようになりました。

1.  ポータルで、前の手順の関数アプリに対して **[プラットフォーム機能]** を選択します。 次に、**[ネットワーク]** を選択します。

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  **[VNet 統合]** の下の **[ここをクリックして構成]** を選択します。

    ![ネットワーク機能を構成するための状態](./media/functions-create-vnet/Networking-1.png)

1. 仮想ネットワークの統合ページで、**[VNet の追加 (プレビュー)]** を選択します。

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  使用する関数と App Service プランの新しいサブネットを作成します。 サブネットのサイズによって、ご利用の App Service プランに追加できる VM の合計数が制限されることに注意してください。 仮想ネットワークでは、仮想ネットワーク内のサブネット間のトラフィックが自動的にルーティングされるため、使用する関数がご利用の VM とは別のサブネット内にあっても問題ありません。 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>仮想ネットワーク内のリソースにアクセスする関数を作成する

関数アプリから WordPress サイトを含む仮想ネットワークにアクセスできるようになりました。 そこで、関数を使用してそのファイルにアクセスし、そのファイルをユーザーに提供します。 この例では、WordPress サイトを API として使用し、プロキシを呼び出し元関数として使用します。これらは両方とも簡単に設定して視覚化できるためです。 

同じくらい簡単に、仮想ネットワーク内にデプロイされた他の任意の API を使用できます。 また、仮想ネットワーク内にデプロイされたその API への API 呼び出しを行うコードを含む別の関数を使用することもできます。 仮想ネットワーク内にデプロイされた SQL Server インスタンスが良い例です。

1. ポータルで、前の手順の関数アプリを開きます。
1. **[プロキシ]** > **[+]** を選択して、プロキシを作成します。

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. プロキシ名とルートを構成します。 この例では、"/plant" をルートとして使用します。
1. 前に確認した WordPress サイトの IP を入力し、**[バックエンド URL]** を `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg` に設定します
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

ここで、ブラウザーの新しいタブにバックエンド URL を貼り付けて直接アクセスしようとした場合、ページはタイムアウトになります。これは、WordPress サイトが仮想ネットワークのみに接続されており、インターネットには接続されていないためです。 プロキシの URL をブラウザーに貼り付けると、仮想ネットワーク内の (WordPress サイトから取得された) 植物の写真が表示されます。 

関数アプリは、インターネットと仮想ネットワークの両方に接続されています。 プロキシはパブリック インターネット経由で要求を受信し、その要求を仮想ネットワークに転送するシンプルな HTTP プロキシとして機能します。 その後、プロキシによって中継された応答がパブリック インターネット経由で返されます。 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>次の手順

Premium プランで実行されている関数は、PremiumV2 プラン上の Web Apps と同じ基盤となる App Service インフラストラクチャを共有します。 Web Apps のドキュメントはすべて、Premium プランの関数に適用されます。

* [関数のネットワーク オプションに関する詳細情報](./functions-networking-options.md)
* [関数のネットワークに関する FAQ](./functions-networking-faq.md)
* [Azure の仮想ネットワークに関する詳細情報](../virtual-network/virtual-networks-overview.md)
* [App Service Environment でさらなるネットワーク機能と制御を可能にする](../app-service/environment/intro.md)
* [ハイブリッド接続を使用して、ファイアウォールを変更せずに個々のオンプレミス リソースに接続する](../app-service/app-service-hybrid-connections.md)
* [Functions プロキシの詳細情報](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
