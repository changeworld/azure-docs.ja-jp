---
title: Edge を使用してデータを変換する | Microsoft Docs
description: Edge でコンピューティング ロールを構成し、Azure への送信前にそれをデータの変換に使用する方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ba77fc4596d9bb245b3cea2538804b1816e9ad14
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466972"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>チュートリアル: Edge を使用してデータを変換する (プレビュー)

このチュートリアルでは、Edge でコンピューティング ロールを構成する方法を説明します。 コンピューティング ロールが構成されたら、Edge で、Azure への送信前にデータを変換することができます。

この手順の所要時間は約 30 ～ 45 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * IoT Hub リソースを作成する
> * コンピューティング ロールを設定する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。
 
## <a name="prerequisites"></a>前提条件

Edge でコンピューティングを設定する前に、次のことを確認してください。

* [Edge の接続とアクティブ化](data-box-edge-deploy-connect-setup-activate.md)に関するページで説明されているように、Edge デバイスがアクティブ化されている。


## <a name="create-an-iot-hub-resource"></a>IoT Hub リソースを作成する

Edge でコンピューティング ロールを設定する前に、IoT Hub リソースを作成する必要があります。

詳細な手順については、「[IoT Hub の作成](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)」を参照してください。 Edge リソースの作成に使用したのと同じサブスクリプションとリソース グループを使用します。

![IoT Hub リソースを作成する](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Edge コンピューティング ロールが設定されていないときには、以下のことに注意してください。

- IoT Hub リソースに、まったく IoT デバイスや IoT Edge デバイスがありません。
- Edge ローカル共有を作成することはできません。 共有を追加するときに、Edge コンピューティングのためにローカル共有を作成するオプションが有効になっていません。


## <a name="set-up-compute-role"></a>コンピューティング ロールを設定する

Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます。1 つは IoT デバイスで、もう 1 つは IoT Edge デバイスです。 IoT Hub リソースでは、これらのデバイスの両方を表示できます。

デバイスでコンピューティング ロールを設定するには、次の手順を実行します。

1. Edge リソースに移動し、**[概要]** に移動して **[Set up compute role]\(コンピューティング ロールの設定)** をクリックします。 

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    **[モジュール]** に移動して **[コンピューティングの構成]** をクリックすることもできます。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. ドロップダウン リストから、前の手順で作成した **IoT Hub リソース**を選択します。 現時点で IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。 **Create** をクリックしてください。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. コンピューティング ロールを作成する所要時間は数分です。 このリリースでのバグのため、コンピューティング ロールが作成された場合でも画面が最新の情報に更新されません。 **[モジュール]** に移動すると、Edge コンピューティングが構成されていることを確認できます。  

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. もう一度 **[概要]** に移動すると、今度は画面が更新されて、コンピューティング ロールが構成されたことが示されます。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Edge コンピューティング ロールを作成するときに使用した IoT Hub に移動します。 **[IoT devices](IoT デバイス)** に移動します。 今回は IoT デバイスが有効になっていることを確認できます。 

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. **[IoT Edge]** に移動すると、IoT Edge デバイスも有効になっていることが分ります。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. 目的の IoT Edge デバイスを選択してクリックします。 この IoT Edge デバイスで Edge エージェントが実行されています。 

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

ただし、この Edge デバイスにはカスタム モジュールがありません。 ここで、このデバイスにカスタム モジュールを追加できます。 カスタム モジュールを作成する方法については、[Data Box Edge 用の C# モジュールを開発する方法](data-box-edge-create-iot-edge-module.md)に関するページを参照してください。


## <a name="add-a-custom-module"></a>カスタム モジュールを追加する

このセクションでは、[Data Box Edge 用の C# モジュールを開発する方法](data-box-edge-create-iot-edge-module.md)に関するページで作成した IoT Edge デバイスにカスタム モジュールを追加します。 

この手順では、使用されるカスタム モジュールが Edge デバイス上のローカル共有からファイルを受け取り、デバイスのクラウド共有にそれらを移動する例を使用します。 クラウドはファイルを共有した後、クラウド共有に関連付けられている Azure ストレージ アカウントにファイルをプッシュします。 

1. 最初の手順では、Edge デバイスにローカル共有を追加します。 Edge リソースで、**[共有]** に移動します。 **[+ Add share]\(+ 共有の追加)** をクリックします。 共有名を指定し、共有の種類を選択します。 ローカル共有を作成するため、チェックボックスをオンにして **[Configure as Edge local share] \(Edge ローカル共有として構成する)** オプションを選択します。 **既存のユーザー**を選択するか、**新規作成**します。 **Create** をクリックしてください。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    ローカルの NFS 共有を作成した場合は、次の rsync コマンド オプションを使用してファイルを共有にコピーします。

    `rsync --inplace <source file path> < destination file path>`

     rsync コマンドの詳細については、[Rsync のドキュメント](https://www.computerhope.com/unix/rsync.htm)に移動してください。 

 
2. ローカル共有が作成され、作成に成功したことの通知を受信したら (共有の一覧が先に更新される場合もありますが、共有の作成が完了するまで待つ必要があります)、共有の一覧に移動します。 

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. 新しく作成されたローカル共有を選択してクリックし、共有のプロパティを表示します。 この共有に対応する **Edge モジュールのローカル マウント ポイント**をコピーし、保存します。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    お使いの Edge デバイスに作成された既存のクラウド共有に移動します。 ここでも、このクラウド共有用の Edge コンピューティング モジュールのローカル マウント ポイントをコピーして保存します。 これらのローカル マウント ポイントは、モジュールをデプロイするときに使用されます。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. カスタム モジュールを IoT Edge デバイスに追加するには、IoT Hub リソースに移動してから、**[IoT Edge device]\(IoT Edge デバイス)** に移動します。 デバイスを選択してクリックします。 **[デバイスの詳細]** の上部にあるコマンド バーの **[Set modules]\(モジュールの設定\)** をクリックします。 

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. **[モジュールの追加]** で以下の手順を実行します。

    1. カスタム モジュールの **[Container registry settings] \(コンテナー レジストリの設定)** で、**名前**、**アドレス**、**ユーザー名**、および**パスワード**を指定します。 名前、アドレス、および一覧に示された資格情報は、一致する URL を使用してモジュールを取得するために使用されます。 このモジュールをデプロイするには、**[Deployment modules]\(デプロイ モジュール)** で **[IoT Edge module]\(IoT Edge モジュール)** を選択します。 この IoT Edge モジュールは、Edge デバイスに関連付けられている IoT Edge デバイスにデプロイできる docker コンテナーです。

        ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. IoT Edge カスタム モジュールの設定を指定します。 モジュールの**名前**および対応するコンテナー イメージの**イメージ URI** を入力します。 
    
        ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. **[コンテナーの作成オプション]** で、クラウドとローカル共有のために、以前の手順でコピーした Edge モジュールのローカル マウント ポイントを指定します (新規作成するのではなくこれらのパスを使用することが重要です)。 [カスタム コードでモジュールを更新](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)するときにモジュールに指定した対応する **InputFolderPath** と **OutputFolderPath** にローカル マウントポイントがマップされます。 
    
        以下のサンプルをコピーして、**[コンテナーの作成オプション]** に貼り付けてください。 
        
        ```
        {
         "HostConfig": {
          "Binds": [
           "/home/hcsshares/mysmblocalshare:/home/LocalShare",
           "/home/hcsshares/mysmbshare1:/home/CloudShare"
           ]
         }
        }
        ```

        また、もしあれば、ここでモジュールの環境変数も指定します。

        ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. 必要に応じて **Edge ランタイムの詳細設定を構成**し、**[次へ]** をクリックします。

        ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  **[Specify routes]\(ルートの指定\)** で、モジュール間のルートを設定します。 この場合、クラウド共有にデータをプッシュするローカル共有の名前を指定します。 **[次へ]** をクリックします。

    ルート ("route") は、"FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")" というルート文字列で置き換えることができます。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  **[Review deployment]\(デプロイの確認)** で、すべての設定を確認し、問題がなければデプロイのためにモジュールを**送信**します。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
これで、**[モジュール]** の **[IoT Edge Custom module]\(IoT Edge カスタム モジュール)** に示されているように、モジュールのデプロイが開始されます。

![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>データ変換を検証して転送する

最後の手順では、モジュールが接続され、予期した通り実行されていることを確認します。 以下の手順を実行し、モジュールが実行されていることを確認します。

1. モジュールのランタイムの状態は、IoT Hub リソース内の IoT Edge デバイスに対して実行中である必要があります。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. モジュールを選択してクリックし、**[Module Identity Twin]\(モジュール ID ツイン)** を確認します。 Edge デバイスとモジュールのクライアント ステータスは、**[接続中]** と表示される必要があります。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  実行中になったモジュールは、Edge リソース内の Edge モジュールの一覧にも表示されます。 追加したモジュールの**ランタイムの状態**は、**実行中**です。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  ファイル エクスプローラーで、作成したローカル共有とクラウド共有の両方に接続します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  データをローカル共有に追加します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  そのデータはクラウド共有に移動されます。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  データは次に、クラウド共有からストレージ アカウントにプッシュされます。 Storage Explorer に移動してデータを表示します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
これで検証プロセスは終了です。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Edge に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * IoT Hub リソースを作成する
> * コンピューティング ロールを設定する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

次のチュートリアルに進み、Edge を管理する方法を学習してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Edge を管理する](http://aka.ms/dbg-docs)


