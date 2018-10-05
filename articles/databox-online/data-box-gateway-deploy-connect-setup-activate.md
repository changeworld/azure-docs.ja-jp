---
title: Azure portal 内で Azure Data Box Gateway を接続、構成、アクティブ化する | Microsoft Docs
description: Data Box Gateway をデプロイする 3 番目のチュートリアルでは、ご利用の仮想デバイスを接続、設定、およびアクティブ化する方法について説明します。
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/27/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 77ccc6ebbb48ab3d7ce22d6c593bd62aa8dec3ac
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419482"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>チュートリアル: Azure Data Box Gateway (プレビュー) を接続、設定、アクティブ化する 

## <a name="introduction"></a>はじめに

このチュートリアルでは、ローカル Web UI を使用して、Data Box Gateway デバイスに接続し、このデバイスを設定およびアクティブ化する方法について説明します。 

セットアップとアクティブ化のプロセスは、完了するまでに約 10 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想デバイスに接続する
> * 仮想デバイスを設定する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


> [!IMPORTANT]
> - Data Box Gateway はプレビュー段階にあります。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="prerequisites"></a>前提条件

Data Box Gateway を構成および設定する前に、以下のことを確認します。

* [Hyper-V での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-hyperv.md)または[VMware での Data Box Gateway のプロビジョニング](data-box-gateway-deploy-provision-vmware.md)に関するページの説明に従って仮想デバイスをプロビジョニングし、その仮想デバイスに接続された URL を取得していること。
* Data Box Gateway デバイスを管理するために作成した Data Box Gateway サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Data Box Gateway のデプロイ準備](data-box-gateway-deploy-prep.md)に関する記事をご覧ください。

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する 

1. ブラウザー ウィンドウを開き、ローカル Web UI に接続します。 次のコマンドを入力します: 
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   前のチュートリアルに記載されている接続 URL を使用します。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 **[この Web ページの閲覧を続ける]** をクリックして、この Web ページに進みます。 (これらの手順は、使用するブラウザーによって異なる場合があります。)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. ご利用の仮想デバイスの Web UI にサインインします。 既定のパスワードは *Password1*です。 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. デバイス管理者のパスワードを変更するように求められます。 8 文字から 16 文字の新しいパスワードを入力します。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を含める必要があります。

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

これで、ご利用のデバイスの**ダッシュボード**が表示されます。

## <a name="set-up-and-activate-the-virtual-device"></a>仮想デバイスを設定してアクティブ化する
 
1. 仮想デバイスを構成して Data Box Gateway サービスに登録するのに必要なさまざまな設定には、ダッシュボードからアクセスできます。 **ネットワーク設定**、**Web プロキシの設定**、**時刻の設定**はオプションです。 必須の設定は、**[デバイス名]** と **[クラウド設定]** のみです。
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. **[デバイス名]** ページで、使用するデバイスのフレンドリ名を構成します。 この名前は 1 文字から 15 文字の長さで指定でき、文字、数字、ハイフンを含めることができます。

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (必要に応じて) **[ネットワーク設定]** を構成します。 基になる仮想マシンで構成した数に応じて、1 つまたは複数のネットワーク インターフェイスが表示されます。 ネットワーク インターフェイスが 1 つ有効になっている仮想デバイスの **[ネットワーク設定]** ページは、次のように表示されます。
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    ネットワーク設定を構成する場合は、次の点に留意してください。

    - ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 そのため、IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
    - DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
    - 使用するネットワーク インターフェイスは、IPv4 として構成できます。
   
4. (省略可能) Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、ここでのみ構成できることに注意してください。
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   **[Web プロキシ]** ページで、以下のことを実行します。
   
   1. *http://&lt;host-IP アドレスまたは FDQN&gt;:ポート番号*の形式で、**[Web プロキシ URL]** を指定します。 HTTPS URL はサポートされていないことに注意してください。
   2. **[認証]** に **[基本]** または **[なし]** を指定します。
   3. 認証を使用する場合は、**ユーザー名**と**パスワード**も指定する必要があります。
   4. **[Apply]** をクリックします。 これにより、構成済みの Web プロキシ設定が検証され、適用されます。

5. (省略可能) デバイスの時刻設定を構成します (タイム ゾーン、プライマリおよびセカンダリ NTP サーバーなど)。 デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    1. ドロップダウン リストから、デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を選択します。 デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
    2. デバイスの **[プライマリ NTP サーバー]** を指定するか、time.windows.com の既定値をそのまま使用します。 データ センターからインターネットへの NTP トラフィックがネットワークで許可されていることを確認します。
    3. 必要に応じて、デバイスの **[セカンダリ NTP サーバー]** を指定します。
    4. **[Apply]** をクリックします。 これにより、構成済みの時刻設定が検証され、適用されます。

6. Azure portal の **[クラウド設定]** ページで、Data Box Gateway サービスに対してご自分のデバイスをアクティブ化します。
    
    1. Data Box Gateway 用の [[アクティブ化キーの取得]](data-box-gateway-deploy-prep.md#get-the-activation-key) 内で取得した**アクティブ化キー**を入力します。

    2. **[アクティブ化]** をクリックします。 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. デバイスが正常にアクティブ化されるまでに、少し待つ必要がある場合があります。 アクティブ化の後、ページが更新され、デバイスが正常にアクティブ化されたことを示します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Data Box Gateway に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 仮想デバイスに接続する
> * 仮想デバイスを設定する


次のチュートリアルに進み、ご利用の Data Box Gateway を使用してデータを転送する方法を学習してください。

> [!div class="nextstepaction"]
> [Data Box Gateway を使用してデータを転送する](./data-box-gateway-deploy-add-shares.md)します。
