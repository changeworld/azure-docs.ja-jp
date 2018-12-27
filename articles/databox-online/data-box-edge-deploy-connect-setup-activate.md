---
title: Azure portal 内で Azure Data Box Edge を接続、構成、アクティブ化する | Microsoft Docs
description: Data Box Edge をデプロイする 3 番目のチュートリアルでは、ご利用の物理デバイスを接続、設定、およびアクティブ化する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166578"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>チュートリアル: Azure Data Box Edge (プレビュー) を接続、設定、アクティブ化する 

このチュートリアルでは、ローカル Web UI を使用して、Data Box Edge デバイスに接続し、このデバイスを設定およびアクティブ化する方法について説明します。 

設定とアクティブ化のプロセスは、完了するまでに約 20 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 物理デバイスへの接続
> * 物理デバイスの設定とアクティブ化

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="prerequisites"></a>前提条件

Data Box Edge を構成および設定する前に、以下のことを確認します。

* [Data Box Edge のインストール](data-box-edge-deploy-install.md)で説明されているように、物理デバイスがインストールされていること。
* Data Box Edge デバイスを管理するために作成した Data Box Edge サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Data Box Edge のデプロイ準備](data-box-edge-deploy-prep.md)に関する記事をご覧ください。

## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する 

1. Edge デバイスに接続するために使用するコンピューターのイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。
2. デバイスでコンピューターをポート 1 に接続します。 
3. ブラウザー ウィンドウを開き、 https://192.168.100.10 からデバイスのローカル Web UI にアクセスします。 このアクションは、デバイスをオンにしてから数分かかる場合があります。 
4. Web サイトのセキュリティ証明書に問題があることを示すエラーまたは警告が表示されます。 **[この Web ページの閲覧を続ける]** をクリックして、この Web ページに進みます。 (これらの手順は、使用するブラウザーによって異なる場合があります。)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. ご利用のデバイスの Web UI にサインインします。 既定のパスワードは *Password1*です。 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. デバイス管理者のパスワードを変更するように求められます。 8 文字から 16 文字の新しいパスワードを入力します。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を含める必要があります。

これで、ご利用のデバイスの**ダッシュボード**が表示されます。

## <a name="set-up-and-activate-the-physical-device"></a>物理デバイスの設定とアクティブ化
 
1. 物理デバイスを構成して Data Box Edge サービスに登録するのに必要なさまざまな設定には、ダッシュボードからアクセスできます。 **デバイス名**、**ネットワーク設定**、**Web プロキシの設定**、**時刻の設定**はオプションです。 必須の設定は、**[クラウド設定]** のみです。
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. **[デバイス名]** ページで、使用するデバイスのフレンドリ名を構成します。 この名前は 1 文字から 15 文字の長さで指定でき、文字、数字、ハイフンを含めることができます。

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (必要に応じて) **[ネットワーク設定]** を構成します。 物理デバイスで 6 つのネットワーク インターフェイスが表示されます。 ポート 1 とポート 2 は、1 Gbps ネットワーク インターフェイスです。 ポート 3、ポート 4、ポート 5、およびポート 6 はすべて、25 Gbps ネットワーク インターフェイスです。 ポート 1 は管理専用ポートとして自動的に構成され、ポート 2 からポート 6 はすべてデータ ポートになります。 **[ネットワークの設定]** ページは次のとおりです。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    ネットワーク設定を構成する場合は、次の点に留意してください。

    - ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
    - DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
    - 使用するネットワーク インターフェイスは、IPv4 として構成できます。
   
4. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できます。
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   **[Web プロキシ]** ページで、以下のことを実行します。
   
   1. 次の形式で、**Web プロキシの URL** を指定します: `http://host-IP address or FDQN:Port number`。 HTTPS URL はサポートされていません。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、**ユーザー名**と**パスワード**も指定する必要があります。
   4. **[適用]** をクリックして、構成済みの Web プロキシ設定が検証され、適用されます。

5. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
    2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
    3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
    4. **[適用]** をクリックして、構成済みの時刻設定が検証され、適用されます。

6. Azure portal の **[クラウド設定]** ページで、Data Box Edge サービスに対してご自分のデバイスをアクティブ化します。
    
    1. Data Box Edge 用の [[アクティブ化キーの取得]](data-box-edge-deploy-prep.md#get-the-activation-key) 内で取得した**アクティブ化キー**を入力します。

    2. **[Apply]** をクリックします。 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. デバイスが正常にアクティブ化されると、接続モードのオプションが表示されます。 これらの設定は、部分的に切断された状態、または切断モードでデバイスを使用する必要がある場合に構成されます。 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

デバイスの設定が完了しました。 これで、デバイスで共有を追加できます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Edge に関する以下のトピックについて説明しました。

> [!div class="checklist"]
> * 物理デバイスへの接続
> * 物理デバイスの設定とアクティブ化


次のチュートリアルに進み、ご利用の Data Box Edge を使用してデータを転送する方法を学習してください。

> [!div class="nextstepaction"]
> [Data Box Edge を使用してデータを転送する](./data-box-edge-deploy-add-shares.md)