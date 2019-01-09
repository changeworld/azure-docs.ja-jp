---
title: デバイスでのブロック blob の格納 -Azure IoT Edge | Microsoft Docs
description: Azure Blob Storage モジュールをご利用の IoT Edge デバイスにデプロイして、そのエッジにデータを格納します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e56d49208740686b51cdaef1bab778e2c08a9b58
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077922"
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

#### <a name="find-the-module"></a>モジュールを見つける

次の 2 つの方法のいずれかを選択して、BLOB ストレージ モジュールを見つけます。

1. Azure portal で「Azure Blob Storage on IoT Edge」を検索します。 その検索結果の項目を**選択**します。
2. Azure portal から Marketplace に移動し、[モノのインターネット (IoT)] をクリックします。 [IoT Edge モジュール] セクションで、[Azure Blob Storage on IoT Edge]\(IoT Edge の Azure Blob Storage\) を選択します。 その後 **[作成]** をクリックします。

#### <a name="steps-to-deploy"></a>デプロイ手順

**IoT Edge モジュールの対象デバイス**

1. IoT Hub がデプロイされている [サブスクリプション] を選択します。
2. 対象の [IoT Hub] を選択します。
3. このモジュールをデプロイする [IoT Edge Device Name]\(IoT Edge デバイス名\) を指定します。 [Find Device]\(デバイスの検索\) を使用してデバイスの場所を探すこともできます。
4. **Create** をクリックしてください。

**モジュールの設定**

1. [モジュールの追加] セクションの [デプロイ モジュール] で、そのモジュールが「AzureBlobStorageonIoTEdge」で始まる名前で一覧に既に表示されていることがわかります。 
2. [デプロイ モジュール] の一覧からその BLOB ストレージ モジュールを**選択**します。 [IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\) サイド パネルが開きます。
3. **[名前]**: ここでモジュールの名前を変更できます
4. **[イメージの URI]**: URI を **mcr.microsoft.com/azure-blob-storage:latest** に置き換えます
5. **[コンテナーの作成オプション]**: 以下の JSON を自分の値で編集し、ポータル ページの JSON と置き換えます。
   
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
   
    * `<your storage account name>` を更新します。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。
    * `<your storage account key>` を 64 バイトの Base64 キーに更新します。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。
    * `<storage directory bind>` を更新します。 コンテナーのオペレーティング システムによって異なります。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。  

       * Linux コンテナー: **\<ストレージのパス>:/blobroot**。 たとえば、/srv/containerdata:/blobroot です。 または、my-volume:/blobroot。 
       * Windows コンテナー: **\<ストレージのパス>:C:/BlobRoot**。 たとえば、C:/ContainerData:C:/BlobRoot です。 または、my-volume:C:/blobroot。
   
   > [!CAUTION]
   > **\<Storage directory bind>** の値 (Linux の場合は "/blobroot"、Windows の場合は "C:/BlobRoot") を変更しないでください。

    ![モジュール コンテナー作成オプション - ポータルの更新](./media/how-to-store-data-blob/edit-module.png)

6. [IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\) の値を**保存**します。
7. [モジュールの設定] セクションで **[次へ]** をクリックします。
8. [ルートの指定] セクションで **[次へ]** をクリックします。
9. 確認したら、[デプロイの確認] セクションで **[送信]** をクリックします。
10. IoT Hub でデバイスが BLOB ストレージ モジュールを実行していることを確認します。 

### <a name="visual-studio-code-templates"></a>Visual Studio Code のテンプレート

Azure IoT Edge では、エッジ ソリューションの開発に役立つ、Visual Studio Code のテンプレートが提供されます。 これらの手順では、ご利用の開発マシンに [Visual Studio Code](https://code.visualstudio.com/) がインストールされ、[Azure IoT Edge の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)が構成されている必要があります。

次の手順を使って、BLOB ストレージ モジュールで新しい IoT Edge ソリューションを作成し、配置マニフェストを構成します。 

1. **[ビュー]** > **[コマンド パレット]** の順に選択します。 

2. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 

3. プロンプトに従って新しいソリューションを作成します。 

   1. **Select Folder (フォルダーの選択)** - 新しいソリューションを作成するフォルダーを参照します。  
   
   2. **Provide a solution name (ソリューション名を指定)** - ご自分のソリューションの名前を入力するか、既定値をそのまま使用します。
   
   3. **Select module template (モジュール テンプレートの選択)** - **既存のモジュール (完全なイメージの URL を入力)** を選びます。
   
   4. **Provide a module name (モジュール名を指定)** - ご自分のモジュールに対して認識できる名前を入力します (**azureBlobStorage** など)。
   
   5. **[Provide Docker image for the module]\(モジュールに Docker イメージを指定してください\)** - イメージの URI を指定します: **mcr.microsoft.com/azure-blob-storage**

VS Code は指定した情報を取り、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。 

ソリューション テンプレートによって、ご自分の BLOB ストレージ モジュール イメージを含む配置マニフェストのテンプレートが作成されますが、モジュールの作成オプションを構成する必要があります。 

1. 新しいソリューション ワークスペースで **deployment.template.json** を開いて、**modules** セクションを見つけます。 

2. このデプロイに必要ないときは、**tempSensor** モジュールを削除します。 

3. 次のコードをコピーして、ご自分の BLOB ストレージ モジュールの **createOptions** フィールドに貼り付けます。 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![モジュール createOptions - VS Code の更新](./media/how-to-store-data-blob/create-options.png)

4. 作成オプションの JSON で、ご利用のコンテナー オペレーティング システムに応じて `<storage directory bind>` を更新します。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。  

   * Linux コンテナー: **\<ストレージのパス>:/blobroot**。 たとえば、/srv/containerdata:/blobroot です。 または、my-volume:/blobroot。
   * Windows コンテナー: **\<ストレージのパス>:C:/BlobRoot**。 たとえば、C:/ContainerData:C:/BlobRoot です。 または、my-volume:C:/blobroot。
   
   > [!CAUTION]
   > **\<Storage directory bind>** の値 (Linux の場合は "/blobroot"、Windows の場合は "C:/BlobRoot") を変更しないでください。

5. **deployment.template.json** を保存します。

6. ご自分のソリューション ワークスペースで **.env** を開きます。 

7. だれでも利用できるため、BLOB ストレージ イメージにコンテナー レジストリの値を入力する必要はありません。 代わりに、新しい環境変数を 2 つ追加します。 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. `STORAGE_ACCOUNT_NAME` の値を指定します。アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 その後、`STORAGE_ACCOUNT_KEY` に 64 バイトの base64 キーを指定します。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。 

9. **.env** を保存します。 

10. **[deployment.template.json]** を右クリックして、**[Generate IoT Edge deployment manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。 

Visual Studio Code では、deployment.template.json と .env で指定した情報が取得され、それを使用して新しい配置マニフェスト ファイルが作成されます。 配置マニフェストは、ご利用のソリューション ワークスペース内の新しい **config** フォルダーに作成されます。 そのファイルが用意されたら、「[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)」または [Azure CLI 2.0 を使用した Azure IoT Edge モジュールのデプロイ](how-to-deploy-modules-cli.md)に関するページの手順に従うことができます。

## <a name="connect-to-your-blob-storage-module"></a>ご自分の BLOB ストレージ モジュールに接続する

ご自分のモジュールに対して構成したアカウント名とアカウントキーを使用して、ご利用の IoT Edge デバイス上の BLOB ストレージにアクセスできます。 

作成する任意のストレージ要求に対する BLOB エンドポイントとして、ご利用の IoT Edge デバイスを指定します。 構成した IoT Edge デバイス情報とアカウント名を使用して、[明示的なストレージ エンドポイントへの接続文字列を作成](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)できます。 

1. "Azure Blob Storage on IoT Edge" が実行されている同じエッジ デバイスにデプロイされているモジュールの場合、BLOB エンドポイントは `http://<Module Name>:11002/<account name>` になります。 
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

