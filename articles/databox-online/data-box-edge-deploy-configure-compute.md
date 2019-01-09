---
title: Edge を使用してデータを変換する | Microsoft Docs
description: Edge でコンピューティング ロールを構成し、Azure への送信前にそれをデータの変換に使用する方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c0901f22e4941fdfaa21138153a06e97c2d6095f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630378"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>チュートリアル:Azure Data Box Edge (プレビュー) を使用してデータを変換する

このチュートリアルでは、お客様の Azure Data Box Edge デバイスでコンピューティング ロールを構成する方法について説明します。 コンピューティング ロールを構成すると、Data Box Edge は Azure への送信前にデータを変換できます。

この手順の所要時間はおおよそ 30 分から 45 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure IoT Hub リソースを作成する
> * コンピューティング ロールを設定する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。
 
## <a name="prerequisites"></a>前提条件

お客様の Data Box Edge デバイスでコンピューティング ロールを設定する前に、次のことを確認してください。

* [Azure Data Box Edge の接続、設定、アクティブ化](data-box-edge-deploy-connect-setup-activate.md)に関するページで説明されているとおり、お客様の Data Box Edge デバイスをアクティブ化したこと。


## <a name="create-an-iot-hub-resource"></a>IoT Hub リソースを作成する

Data Box Edge でコンピューティング ロールを設定する前に、IoT Hub リソースを作成する必要があります。

詳細な手順については、「[IoT Hub の作成](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub)」を参照してください。 お客様の Data Box Edge リソースの作成に使用したのと同じサブスクリプションとリソース グループを使用します。

![IoT Hub リソースを作成する](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Edge コンピューティング ロールが設定されていない場合、以下の注意事項が適用されます。

- IoT Hub リソースに、Azure IoT デバイスまたは Azure IoT Edge デバイスが一切ありません。
- Edge ローカル共有を作成することはできません。 共有を追加するときに、Edge コンピューティングのためにローカル共有を作成するオプションが有効になっていません。


## <a name="set-up-compute-role"></a>コンピューティング ロールを設定する

Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。

デバイスでコンピューティング ロールを設定するには、以下を実行します。

1. Data Box Edge リソースに移動し、**[概要]**、**[Set up compute role]\(コンピューティング ロールの設定\)** の順に選択します。 

    ![左側のウィンドウの [概要] リンク](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    必要に応じて、**[モジュール]** に移動して **[コンピューティングの構成]** を選択できます。

    ![[モジュール] リンクと [コンピューティングの構成] リンク](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. ドロップダウン リストで、お客様が前の手順で作成した **IoT Hub リソース**を選択します。  
    現時点では、お客様の IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。 
    
1. **Create** をクリックしてください。

    ![[作成] ボタン](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    コンピューティング ロールを作成する所要時間は数分です。 このリリースでのバグのため、コンピューティング ロールが作成された場合でも画面が最新の情報に更新されません。 Edge コンピューティング ロールが構成されたことを確認するには、**[モジュール]** に移動します。  

    ![[Edge コンピューティングの構成] のデバイス一覧](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. もう一度 **[概要]** に移動します。  
    画面が更新されて、コンピューティング ロールが構成されたことが示されます。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. お客様が Edge コンピューティング ロールを作成したときに使用した IoT ハブで、**[IoT デバイス]** に移動します。  
    IoT デバイスが有効になっています。 

    ![[IoT デバイス] ページ](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. 左側のウィンドウで、**[IoT Edge]** を選択します。  
    IoT Edge デバイスも有効です。

    ![コンピューティング ロールを設定する](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. 目的の IoT Edge デバイスを選択してクリックします。  
    この IoT Edge デバイスで Edge エージェントが実行されています。 

    ![[デバイスの詳細] ページ](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    この Edge デバイスにカスタム モジュールはありませんが、ここでカスタム モジュールを追加できます。 カスタム モジュールを作成する方法については、[Data Box Edge デバイス用の C# モジュールの開発](data-box-edge-create-iot-edge-module.md)に関するページを参照してください。


## <a name="add-a-custom-module"></a>カスタム モジュールを追加する

このセクションでは、[Data Box Edge 用の C# モジュールの開発](data-box-edge-create-iot-edge-module.md)に関するページでお客様が作成したカスタム モジュールを IoT Edge デバイスに追加します。 

次の手順で使用される例では、カスタム モジュールによって Edge デバイス上のローカル共有からファイルが受け取られ、デバイスのクラウド共有にそれらが移動されます。 その後、クラウド共有から、そのクラウド共有に関連付けられた Azure ストレージ アカウントにファイルがプッシュされます。 

1. 以下を実行して、Edge デバイスにローカル共有を追加します。

    a. Edge リソースで、**[共有]** に移動します。 
    
    b. **[共有の追加]** を選択してから、共有名を指定して共有の種類を選択します。 
    
    c. ローカル共有を作成するには、**[Edge ローカル共有として構成]** チェック ボックスをオンにします。 
    
    d. **[新規作成]** または **[既存のものを使用]** を選択して、**[作成]** を選択します。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    ローカルの NFS 共有を作成した場合は、次のリモート同期 (rsync) コマンド オプションを使用して、ファイルを共有にコピーします。

    `rsync --inplace <source file path> < destination file path>`

    rsync コマンドの詳細については、[rsync に関するドキュメント](https://www.computerhope.com/unix/rsync.htm)を参照してください。 

    ローカル共有が作成されると、作成の成功に関する通知が表示されます。 共有の一覧は更新されているかもしれませんが、共有の作成が完了するまで待つ必要があります。
    
1. 共有の一覧に移動します。 

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. 新しく作成されたローカル共有のプロパティを表示するには、それを選択します。 

1. **[Edge コンピューティング モジュールのローカル マウント ポイント]** ボックスで、この共有に対応する値をコピーします。  
    このローカル マウント ポイントはモジュールのデプロイ時に使用します。

    ![[Edge コンピューティング モジュールのローカル マウント ポイント] ボックス](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. お客様の Data Box Edge デバイスで作成された既存のクラウド共有の **[Edge コンピューティング モジュールのローカル マウント ポイント]** ボックスで、そのクラウド共有の Edge コンピューティング モジュールのローカル マウント ポイントをコピーします。  
    このローカル マウント ポイントはモジュールのデプロイ時に使用します。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. カスタム モジュールを IoT Edge デバイスに追加するには、お客様の IoT Hub リソースに移動してから、**[IoT Edge デバイス]** に移動します。 

1. デバイスを選択してから、**[デバイスの詳細]** で **[モジュールの設定]** を選択します。 

    ![[モジュールの設定] リンク](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. **[モジュールの追加]** で以下を実行します。

    a. カスタム モジュールのコンテナー レジストリの設定で、名前、アドレス、ユーザー名、パスワードを入力します。  
    名前、アドレス、および一覧に示された資格情報は、一致する URL を使用してモジュールを取得するために使用されます。 このモジュールをデプロイするには、**[Deployment modules]\(デプロイ モジュール)** で **[IoT Edge module]\(IoT Edge モジュール)** を選択します。 この IoT Edge モジュールは、お客様の Data Box Edge デバイスに関連付けられている IoT Edge デバイスにデプロイできる Docker コンテナーです。

    ![[モジュールの設定] ページ](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. お客様のモジュールの名前と対応するコンテナー イメージのイメージ URI を入力して、IoT Edge カスタム モジュールに関する設定を指定します。 
    
    ![[IoT Edge のカスタム モジュール] ページ](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. **[コンテナーの作成オプション]** ボックスで、クラウドとローカルの共有に関してお客様が前の手順でコピーした Edge モジュールのローカル マウント ポイントを入力します。
    > [!IMPORTANT]
    > コピーしたパスを使用します。新しいパスは作成しないでください。 ローカル マウント ポイントは、お客様が[カスタム コードでモジュールを更新](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)したときにモジュールに指定した、対応する **InputFolderPath** と **OutputFolderPath** にマップされます。 
    
    **[コンテナーの作成オプション]** ボックスで、次のサンプルを貼り付けることができます。 
    
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

    お客様のモジュールの環境変数もここで指定します。

    ![[コンテナーの作成オプション] ボックス](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. 必要に応じて、Edge ランタイムの詳細設定を構成し、**[次へ]** をクリックします。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  **[ルートの指定]** で、モジュール間のルートを設定します。  
    この例では、クラウド共有にデータをプッシュするローカル共有の名前を入力します。

    "*ルート*" は、次のルート文字列で置き換えることができます。`"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![[ルートの指定] セクション](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. **[次へ]** を選択します。 

1.  **[Review deployment]\(デプロイの確認\)** ですべての設定を確認してから **[送信]** を選択し、デプロイのためにモジュールを送信します。

    ![[モジュールの設定] ページ](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    この操作を行うと、次の図のようにモジュールのデプロイが開始されます。

    ![カスタム モジュールを追加する](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>データ変換を検証して転送する

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
モジュールが実行中であることを確認するには、以下を実行します。

1. モジュールを選択して、モジュール ID ツインを表示します。  
    Edge デバイスとモジュールのクライアントの状態は、*Connected* と表示される必要があります。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    モジュールは実行中になった後、お客様の Data Box Edge リソース内にある Edge モジュールの一覧にも表示されます。 追加したモジュールのランタイムの状態は、*[実行中]* です。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  エクスプローラーで、お客様が先ほど作成したローカル共有とクラウド共有の両方に接続します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  データをローカル共有に追加します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    そのデータはクラウド共有に移動されます。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    データは次に、クラウド共有からストレージ アカウントにプッシュされます。 データを表示するには、Storage Explorer に移動してデータを表示します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
検証プロセスが完了しました。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * IoT Hub リソースを作成する
> * コンピューティング ロールを設定する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

お客様の Data Box Edge デバイスを管理する方法を学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Edge を管理する](https://aka.ms/dbg-docs)


