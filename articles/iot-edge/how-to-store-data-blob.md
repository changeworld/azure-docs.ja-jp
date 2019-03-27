---
title: デバイスでのブロック blob の格納 -Azure IoT Edge | Microsoft Docs
description: Azure Blob Storage モジュールをご利用の IoT Edge デバイスにデプロイして、そのエッジにデータを格納します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9faed53540d449f8658655ff7285b38aa20bee6c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901821"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する (プレビュー)

IoT Edge の Azure Blob Storage では、エッジで[ブロック BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) ストレージのソリューションが提供されます。 ご利用の IoT Edge デバイス上の BLOB ストレージ モジュールは Azure のブロック BLOB サービスのように動作しますが、そのブロック BLOB はご利用の IoT Edge デバイス上でローカルに格納されます。 同じ Azure Storage SDK メソッドまたは既に慣れているブロック BLOB API 呼び出しを使用して、ご自分の BLOB にアクセスできます。 

ビデオ、画像、財務データ、病院データなど、ローカルに格納する必要があるデータで、後でローカルで処理またはクラウドに転送できるシナリオは、このモジュールを使用するよい例です。

この記事では、ご利用の IoT Edge デバイス上で Blob service を実行する IoT Edge コンテナーに Azure Blob Storage をデプロイするための手順を示します。 

>[!NOTE]
>IoT Edge の Azure Blob Storage は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中です。 

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* IoT Edge モジュールの Azure Blob Storage では、次のデバイスの構成がサポートされます。

   | オペレーティング システム | アーキテクチャ |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (10 月の更新プログラム) | AMD64 |
   | Windows 10 IoT Enterprise (10 月の更新プログラム) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="deploy-blob-storage-to-your-device"></a>BLOB ストレージをご利用のデバイスにデプロイする

IoT Edge デバイスにモジュールをデプロイするにはいくつかの方法があり、これらすべてが IoT Edge モジュールの Azure Blob Storage で機能します。 2 つの最も簡単な方法では、Azure portal または Visual Studio Code テンプレートを使用します。 

### <a name="azure-portal"></a>Azure ポータル

Azure Marketplace には、IoT Edge 上の Azure Blob Storage など、IoT Edge デバイスに直接デプロイできる IoT Edge モジュールが用意されています。 次の手順に従って、Microsoft Azure portal でモジュールをデプロイします。

1. [Azure portal](https://portal.azure.com) で「Azure Blob Storage on IoT Edge」を検索します。 また、Marketplace から検索結果を**選択**します。

   ![Marketplace 検索からモジュールを作成する](./media/how-to-store-data-blob/marketplace-module.png)

2. モジュールを受け取る IoT Edge デバイスを選択します。 **[IoT Edge モジュールのターゲット デバイス]** ページで、次の情報を入力します。

   1. 使用している IoT ハブを含む**サブスクリプション**を選択します。

   2. 対象の **[IoT Hub]** を選択します。

   3. **IoT Edge デバイス名**がわかっている場合、テキスト ボックスに入力します。 または、**[デバイスの検索]** を選択して、お客様の IoT ハブにある IoT Edge デバイスの一覧から選択します。 
   
   4. **作成**を選択します。

   これで、Azure Marketplace から IoT Edge モジュールを選択し、モジュールを受け取る IoT Edge デバイスを選択する手順は完了です。次は、モジュールのデプロイ方法を正確に定義するために役立つ 3 つの手順のウィザードに進みます。

3. [モジュールの設定] ウィザードの **[モジュールの追加]** 手順で、**AzureBlobStorageonIoTEdge** モジュールが既に **[デプロイ モジュール]** の下に一覧表示されていることに注意してください。 

2. デプロイ モジュールの一覧から BLOB ストレージ モジュールを選択し、そのモジュールの詳細を開きます。 

   ![モジュールの詳細を開くモジュール名を選択する](./media/how-to-store-data-blob/open-module-details.png)

3. **[IoT Edge のカスタム モジュール]** ページで、次の手順で IoT Edge モジュール上の Azure Blob Storage を更新します。

   1. モジュールの**名前**を小文字に変更します。 必要に応じて、モジュールの名前を変更したり、`azureblobstorageoniotedge` を使用します。 

      >[!IMPORTANT]
      >Azure IoT Edge は、モジュールを呼び出すときに大文字と小文字を区別し、Storage SDK は既定で小文字になります。 IoT Edge モジュール上の Azure Blob Storage への接続が中断されないようにするには、小文字の名前を付けます。 

   2. 既定の **[コンテナーの作成オプション]** は、コンテナーで必要なポート バインドですが、ストレージ アカウント情報と、デバイス上のストレージ ディレクトリのバインドも追加する必要があります。 ポータル内の JSON を以下の JSON で上書きします。
    
      ```json
      {
          "Env":[
              "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
              "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
          ],
          "HostConfig":{
              "Binds":[
                  "<storage directory bind>"
              ],
              "PortBindings":{
                  "11002/tcp":[{"HostPort":"11002"}]
              }
          }
      }
      ```   
   3. コピーした JSON を次の情報で更新します。 

      * `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。
      * `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。
      * コンテナーのオペレーティング システムに応じて `<storage directory bind>` を置き換えます。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。 ストレージ ディレクトリのバインドは、提供したデバイス上の位置をモジュール内の設定された位置にマップします。 

         * Linux コンテナー: **\<ストレージのパス>:/blobroot**。 たとえば、/srv/containerdata:/blobroot です。 または、my-volume:/blobroot。 
         * Windows コンテナー: **\<ストレージのパス>:C:/BlobRoot**。 たとえば、C:/ContainerData:C:/BlobRoot です。 または、my-volume:C:/blobroot。
   
      > [!IMPORTANT]
      > モジュールの特定の位置を指す、ストレージ ディレクトリのバインド値の後半を変更しないでください。 ストレージ ディレクトリ バインドは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

      ![モジュール コンテナー作成オプション - ポータルの更新](./media/how-to-store-data-blob/edit-module.png)

   4. **[保存]** を選択します。

4. **[次へ]** を選択して、ウィザードの次の手順に進みます。
5. ウィザードの **[ルートの指定]** ステップで、**[次へ]** を選択します。
6. ウィザードの **[デプロイの確認]** ステップで、**[送信]** を選びます。
7. デプロイを送信した後、IoT ハブの **IoT Edge** のページに戻ります。 デプロイで対象にした Azure IoT Edge デバイスを選択して、その詳細を開きます。 
8. デバイスの詳細で、BLOB ストレージ モジュールが **[デプロイで指定]** および **[デバイス別に報告]** の両方として一覧表示されていることを確認します。 モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。 

### <a name="visual-studio-code-templates"></a>Visual Studio Code のテンプレート

Azure IoT Edge では、エッジ ソリューションの開発に役立つ、Visual Studio Code のテンプレートが提供されます。 これらの手順では、ご利用の開発マシンに [Visual Studio Code](https://code.visualstudio.com/) がインストールされ、[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) が構成されている必要があります。

次の手順を使って、BLOB ストレージ モジュールで新しい IoT Edge ソリューションを作成し、配置マニフェストを構成します。 

1. **[ビュー]** > **[コマンド パレット]** の順に選択します。 

2. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **既存のモジュール (完全なイメージの URL を入力)** を選択します。 |
   | Provide a module name (モジュール名の指定) | **azureblobstorage** のようにすべて小文字でモジュールの名前を入力します。<br><br>IoT Edge モジュール上の Azure Blob Storage に小文字の名前を使用することが重要です。 IoT Edge は、モジュールを参照するときに大文字と小文字を区別し、Storage SDK は既定で小文字になります。 |
   | Provide Docker image for the module (モジュールの Docker イメージの指定) | イメージの URI: **mcr.microsoft.com/azure-blob-storage:latest** を指定します。 |

   VS Code は指定した情報を取り、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。 ソリューション テンプレートによって、ご自分の BLOB ストレージ モジュール イメージを含む配置マニフェストのテンプレートが作成されますが、モジュールの作成オプションを構成する必要があります。 

3. 新しいソリューション ワークスペースで **deployment.template.json** を開いて、**modules** セクションを見つけます。 次の構成の変更を行います。

   1. このデプロイに必要ないときは、**tempSensor** モジュールを削除します。 

   2. 次のコードをコピーして、ご自分の BLOB ストレージ モジュールの **createOptions** フィールドに貼り付けます。 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![モジュール createOptions - VS Code の更新](./media/how-to-store-data-blob/create-options.png)

4. 作成オプションの JSON で、ご利用のコンテナー オペレーティング システムに応じて `<storage directory bind>` を更新します。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。 ストレージ ディレクトリのバインドは、提供したデバイス上の位置をモジュール内の設定された位置にマップします。  

   * Linux コンテナー: **\<ストレージのパス>:/blobroot**。 たとえば、/srv/containerdata:/blobroot です。 または、my-volume:/blobroot。
   * Windows コンテナー: **\<ストレージのパス>:C:/BlobRoot**。 たとえば、C:/ContainerData:C:/BlobRoot です。 または、my-volume:C:/blobroot。
   
   > [!IMPORTANT]
   > モジュールの特定の位置を指す、ストレージ ディレクトリのバインド値の後半を変更しないでください。 ストレージ ディレクトリ バインドは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

5. **deployment.template.json** ファイルを保存します。

6. ソリューション ワークスペース内の **.env** ファイルを開きます。 

7. .env ファイルはコンテナー レジストリの資格情報を受信するように設定されていますが、公開されているため、BLOB ストレージ イメージには必要ありません。 代わりに、次の 2 つの新しい環境変数でファイルを置き換えます。 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. `STORAGE_ACCOUNT_NAME` の値を指定します。アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 `STORAGE_ACCOUNT_KEY` に 64 バイトの Base64 キーを指定します。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。 

   指定する値をスペースや引用符で囲まないでください。 

9. **.env** ファイルを保存します。 

10. **[deployment.template.json]** を右クリックして、**[Generate IoT Edge deployment manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。 

11. Visual Studio Code では、deployment.template.json と .env で指定した情報が取得され、それを使用して新しい配置マニフェスト ファイルが作成されます。 配置マニフェストは、ご利用のソリューション ワークスペース内の新しい **config** フォルダーに作成されます。 そのファイルが用意されたら、「[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)」または [Azure CLI 2.0 を使用した Azure IoT Edge モジュールのデプロイ](how-to-deploy-modules-cli.md)に関するページの手順に従うことができます。

## <a name="connect-to-your-blob-storage-module"></a>ご自分の BLOB ストレージ モジュールに接続する

ご自分のモジュールに対して構成したアカウント名とアカウントキーを使用して、ご利用の IoT Edge デバイス上の BLOB ストレージにアクセスできます。 

作成する任意のストレージ要求に対する BLOB エンドポイントとして、ご利用の IoT Edge デバイスを指定します。 構成した IoT Edge デバイス情報とアカウント名を使用して、[明示的なストレージ エンドポイントへの接続文字列を作成](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)できます。 

1. "Azure Blob Storage on IoT Edge" が実行されている同じエッジ デバイスにデプロイされているモジュールの場合、BLOB エンドポイントは `http://<module name>:11002/<account name>` になります。 
2. "Azure Blob Storage on IoT Edge" が実行されているエッジ デバイスとは別のエッジ デバイスにデプロイされているモジュールの場合、設定によって BLOB エンドポイントは `http://<device IP >:11002/<account name>`、`http://<IoT Edge device hostname>:11002/<account name>`、または `http://<FQDN>:11002/<account name>` になります。

## <a name="logs"></a>ログ

ログはコンテナー内の以下の場所に見つかります。 
* Linux の場合: /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>複数のインスタンスのデプロイ

IoT Edge 上の Azure Blob Storage の複数のインスタンスをデプロイする場合は、モジュールでバインドする HostPort を変更する必要があるだけです。 BLOB ストレージ モジュールでは常に、コンテナーでポート 11002 を公開しますが、ホスト上でバインドされるポートを宣言できます。 

モジュールの作成オプションを編集して、HostPort 値を変更します。

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

追加の BLOB ストレージ モジュールに接続すると、更新されたホスト ポートをポイントするエンドポイントを変更します。 

### <a name="try-it-out"></a>試してみる

Azure Blob Storage ドキュメントには、複数の言語でサンプル コードを提供するクイック スタートが含まれます。 ご利用の BLOB ストレージ モジュールをポイントするように BLOB エンドポイントを変更して、これらのサンプルを実行し、IoT Edge の Azure Blob Storage をテストできます。

次のクイック スタートでは IoT Edge からもサポートされる言語を使用するため、BLOB ストレージ モジュールと共に IoT Edge モジュールとして言語をデプロイできます。

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>サポートされるストレージ操作

IoT Edge 上の BLOB ストレージ モジュールでは同じ Azure Storage SDK が使用され、ブロック BLOB エンドポイント用の 2018-03-28 バージョンの Azure Storage API と一貫性があります。 今後のリリースは、顧客のニーズに依存します。 

すべての Azure Blob Storage の操作が、IoT Edge の Azure Blob Storage でサポートされるわけではありません。 次のセクションでは、サポートされる操作とサポートされない操作の詳細を示しています。 

### <a name="account"></a>Account

サポート対象: 
* コンテナーの一覧表示

サポート外: 
* Blob service プロパティの取得と設定
* BLOB 要求のプレフライト
* Blob service の統計情報の取得
* アカウント情報の取得

### <a name="containers"></a>Containers

サポート対象: 
* コンテナーの作成と削除
* コンテナーのプロパティとメタデータの取得
* BLOB を一覧表示する

サポート外: 
* コンテナー ACL の取得と設定
* コンテナーのリース
* コンテナー メタデータの設定

### <a name="blobs"></a>BLOB

サポート対象: 
* BLOB の配置、取得、削除
* BLOB プロパティの取得と設定
* BLOB メタデータの取得と設定

サポート外: 
* BLOB のリリース
* BLOB のスナップショット
* BLOB のコピーとコピーの中止
* BLOB の削除の取り消し
* BLOB レベルの設定

### <a name="block-blobs"></a>ブロック blob

サポート対象: 
* ブロックの配置:- ブロックのサイズは 4 MB 以下にする必要があります。
* ブロック一覧の配置と取得

サポート外:
* URL からブロックの配置

## <a name="next-steps"></a>次の手順

[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) の詳細情報

