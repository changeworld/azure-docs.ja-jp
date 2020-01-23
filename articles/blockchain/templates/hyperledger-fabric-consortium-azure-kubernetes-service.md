---
title: Azure Kubernetes Service (AKS) 上の Hyperledger Fabric コンソーシアム
description: Azure Kubernetes Service に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成する方法
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6b9cbe1638034ed05bd4759926f6488693fe8ae3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780747"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上の Hyperledger Fabric コンソーシアム

Azure Kubernetes Service (AKS) 上の Hyperledger Fabric (HLF) テンプレートを使用して、Azure に Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成できます。

この記事を読むと、次のことができます。

- Hyperledger Fabric と、Hyperledger Fabric ブロックチェーン ネットワークの構成要素を形成するさまざまなコンポーネントについての、実用的な知識が得られます。
- 運用シナリオ用に Azure Kubernetes Service に Hyperledger Fabric コンソーシアムをデプロイして構成する方法がわかります。

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric コンソーシアムのアーキテクチャ

Azure 上に Hyperledger Fabric ネットワークを構築するには、ピア ノードで Ordering Service と組織をデプロイする必要があります。 テンプレートのデプロイの一部として、次のようなさまざまな基本コンポーネントが作成されます。

- **Orderer ノード**:台帳でトランザクション順序付けを行うノード。 他のノードと順序付けされたノードにより、Hyperledger Fabric ネットワークの Ordering Service が形成されます。

- **ピア ノード**:ネットワークの基本要素である台帳とスマート コントラクトを主にホストするノードです。

- **Fabric CA**: Fabric CA は、Hyperledger Fabric 用の証明機関 (CA) です。 Fabric CA を使って、証明機関をホストするサーバー プロセスを初期化して起動することができます。 ID と証明書を管理することができます。 テンプレートの一部としてデプロイされる各 AKS クラスターには、既定で Fabric CA ポッドが配置されます。

- **CouchDB または LevelDB**: ピア ノードのワールド状態データベースは、LevelDB または CouchDB のいずれかに格納できます。 LevelDB は、ピア ノードに埋め込まれる既定の状態データベースで、チェーンコード データは単純なキー値ペアとして格納され、キー、キー範囲、複合キーのクエリのみがサポートされます。 CouchDB はオプションの代替状態データベースであり、チェーンコード データの値が JSON としてモデル化されている場合、リッチなクエリがサポートされます。

デプロイ時にテンプレートによって、サブスクリプション内にさまざまな Azure リソースがスピンアップされます。 デプロイされるさまざまな Azure リソースは次のとおりです。

- **AKS クラスター**: ユーザーによって提供される入力パラメーターに従って構成される Azure Kubernetes クラスター。 AKS クラスターには、Hyperledger Fabric ネットワーク コンポーネントを実行するためのさまざまなポッドが構成されます。 作成されるポッドは次のとおりです。

  - **ファブリック ツール**: ファブリック ツールは、Hyperledger Fabric コンポーネントの構成を行います。
  - **Orderer/ピア ポッド**: HLF ネットワークのノード。
  - **Proxy**:クライアント アプリケーションが AKS クラスターとのインターフェイスを確立できる NGNIX プロキシ ポッド。
  - **Fabric CA**: Fabric CA を実行するポッド。
- **PostgreSQL**: PostgreSQL のインスタンスは、Fabric CA の ID を維持するためにデプロイされます。

- **Azure Key Vault**: キー コンテナー インスタンスは、ユーザーによって提供される Fabric CA 資格情報とルート証明書を保存するためにデプロイされ、テンプレートのデプロイの再試行時に使用されます。これは、テンプレートのしくみを処理するためのものです。
- **Azure マネージド ディスク**: Azure マネージド ディスクは、台帳とピア ノードのワールド状態データベースの永続的ストア用です。
- **[パブリック IP]** : クラスターとのインターフェイスを確立するためにデプロイされる AKS クラスターのパブリック IP エンドポイント。

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric ブロックチェーン ネットワークのセットアップ

開始するには、複数の仮想マシンと標準のストレージ アカウントをデプロイできる Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成できます。

次の手順に従って、Hyperledger Fabric ブロックチェーン ネットワークをセットアップします。

- [Orderer/ピア組織をデプロイする](#deploy-the-ordererpeer-organization)
- [コンソーシアムを構築する](#build-the-consortium)
- [ネイティブ HLF 操作を実行する](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Orderer/ピア組織をデプロイする

HLF ネットワーク コンポーネントのデプロイを始めるには、[Azure portal](https://portal.azure.com) に移動します。 **[リソースの作成] > [ブロックチェーン]** を選択し、**Azure Kubernetes Service 上の Hyperledger Fabric** を探します。

1. **[作成]** を選択してテンプレートのデプロイを始めます。 **[Create Hyperledger Fabric on Azure Kubernetes Service]\(Hyperledger Fabric on Azure Kubernetes Service の作成\)** が表示されます。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. **[基本]** ページでプロジェクトの詳細を入力します。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 次の詳細を入力します。
    - **サブスクリプション**:HLF ネットワーク コンポーネントをデプロイするサブスクリプションの名前を選択します。
    - **[リソース グループ]** :新しいリソース グループを作成するか、既存の空のリソース グループを選択します。リソース グループには、テンプレートの一部としてデプロイされているすべてのリソースが保持されます。
    - **[リージョン]** :HLF コンポーネント用の Azure Kubernetes クラスターをデプロイする Azure リージョンを選択します。 テンプレートは、AKS が利用可能なすべてのリージョンで使用できます。お使いのサブスクリプションが仮想マシン (VM) のクォータ制限に達していないリージョンを選択してください。
    - **[Resource prefix]\(リソース プレフィックス\)** : デプロイされるリソースの名前のプレフィックス。 リソース プレフィックスの長さは 6 文字未満にする必要があり、文字の組み合わせには数字と文字の両方を含める必要があります。
4. **[Fabric Settings]\(Fabric 設定\)** タブを選択して、デプロイされる HLF ネットワークのコンポーネントを定義します。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 次の詳細を入力します。
    - **組織名**:さまざまなデータ プレーン操作に必要な Fabric 組織の名前。
    - **[Fabric network component]\(Fabric ネットワーク コンポーネント\)** : セットアップするブロックチェーン ネットワーク コンポーネントに基づいて、Ordering Service またはピア ノードを選択します。
    - **[Number of nodes]\(ノード数\)** - 次の2種類のノードがあります。
        - Ordering Service - ネットワークにフォールト トレランスを提供するノードの数を選択します。 サポートされている orderer ノードの数は、3、5、7 のみです。
        - ピア ノード - 要件に基づいて、1 - 10 ノードを選択できます。
    - **[Peer node world state database]\(ピア ノード ワールド状態データベース\)** : LevelDB または CoucbDB を選択します。 このフィールドは、[Fabric network component]\(Fabric ネットワーク コンポーネント\) ドロップダウンでピア ノードを選択すると表示されます。
    - **[Fabric username]\(Fabric ユーザー名\)** : Fabric CA 認証に使用するユーザー名を入力します。
    - **[Fabric CA password]\(Fabric CA パスワード\)** : Fabric CA 認証のパスワードを入力します。
    - **[Confirm password]\(パスワードの確認\)** : Fabric CA のパスワードを確認します。
    - **[Certificates]\(証明書\)** : 独自のルート証明書を使用して Fabric CA を初期化する場合は、[Upload root certificate for Fabric CA]\(Fabric CA のルート証明書をアップロードする\) オプションを選択します。既定では、Fabric CA によって自己署名証明書が作成されます。
    - **[Root Certificate]\(ルート証明書\)** : Fabric CA の初期化に使用する必要があるルート証明書 (公開キー) をアップロードします。 .pem 形式の証明書がサポートされています。証明書は UTC タイム ゾーンで有効である必要があります。
    - **[Root Certificate private key]\(ルート証明書の秘密キー\)** : ルート証明書の秘密キーをアップロードします。 公開キーと秘密キーが組み合わされた .pem 証明書がある場合は、ここでもアップロードします。


6. **[AKS cluster Settings]\(AKS クラスターの設定\)** タブを選択し、Fabric ネットワーク コンポーネントがセットアップされる基になるインフラストラクチャである Azure Kubernetes クラスターの構成を定義します。

    ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. 次の詳細を入力します。
    - **[Kubernetes クラスター名]** : 作成される AKS クラスターの名前。 このフィールドは、指定したリソース プレフィックスに基づいて既に設定されています。必要に応じて変更できます。
    - **[Kubernetes バージョン]** : クラスターにデプロイする Kubernetes のバージョン。 **[基本]** タブで選択したリージョンに基づいて、使用できるサポートされるバージョンが変わることがあります。
    - **[DNS プレフィックス]** : AKS クラスターのドメイン ネーム システム (DNS) 名のプレフィックス。 クラスターを作成した後でコンテナーを管理するときに、DNS を使用して Kubernetes API に接続します。
    - **[ノード サイズ]** : Kubernetes ノードのサイズ。Azure で利用可能な VM Stock Keeping Unit (SKU) の一覧から選択できます。 最適なパフォーマンスのため、Standard DS3 v2 をお勧めします。
    - **ノード数**:クラスターにデプロイする Kubernetes ノードの数。 このノードの数は、Fabric の設定で指定した HLF ノードの数以上にすることをお勧めします。
    - **[サービス プリンシパルのクライアント ID]** : 既存のサービス プリンシパルのクライアント ID を入力するか、新しく作成します。AKS 認証に必要です。 [サービス プリンシパルを作成する](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)手順もご覧ください。
    - **[サービス プリンシパルのクライアント シークレット]** : サービス プリンシパル クライアント ID で指定したサービス プリンシパルのクライアント シークレットを入力します。
    - **[Confirm client secret]\(クライアント シークレットの確認\)** :サービス プリンシパルのクライアント シークレットで指定したクライアント シークレットを確認します。
    - **[コンテナーの監視を有効にする]** : AKS の監視を有効にする場合に選択します。これにより、指定した Log Analytics ワークスペースに AKS ログがプッシュされるようになります。
    - **Log Analytics ワークスペース**:監視を有効にした場合、Log Analytics ワークスペースには、作成された既定のワークスペースが設定されます。

8. 上記の詳細をすべて指定した後、 **[確認と作成]** タブを選択します。確認と作成では、指定した値の検証がトリガーされます。
9. 検証に合格したら、 **[作成]** を選択できます。
通常、デプロイには 10 から 12 分かかりますが、指定した AKS ノードのサイズと数によって異なる場合があります。
10. デプロイが正常に完了すると、右上隅に Azure の通知が表示されます。
11. **[リソース グループに移動]** を選択し、テンプレートのデプロイの一環としてすべてのリソースが作成されたことを確認します。 すべてのリソース名は、 **[基本]** 設定で指定したプレフィックスで始まっています。

## <a name="build-the-consortium"></a>コンソーシアムを構築する

Ordering Service とピア ノードのデプロイ後にブロックチェーン コンソーシアムを構築するには、次の手順を順番に行う必要があります。 **ネットワーク構築**スクリプト (byn.sh) は、コンソーシアムの設定、チャネルの作成、チェーンコードのインストールに役立ちます。

> [!NOTE]
> 提供されているネットワーク構築 (byn) スクリプトは、デモと DevTest のシナリオにのみ使用されます。 運用グレードのセットアップでは、ネイティブ HLF API を使用することをお勧めします。

byn スクリプトを実行するすべてのコマンドは、Azure Bash コマンド ライン インターフェイス (CLI) を使用して実行できます。 Azure Shell の Web バージョンにログインするには、 ![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) オプションを使用します (Azure portal の右上隅にあります)。 コマンド プロンプトで「bash」と入力して Enter キーを押しBash CLI に切り替えます。

詳しくは、[Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) に関するページをご覧ください。

![Hyperledger Fabric on Azure Kubernetes Service テンプレート](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


byn.sh および fabric-admin.yaml ファイルをダウンロードします。

```bash-interactive
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/fabric-admin.yaml -o fabric-admin.yaml
```
**Azure CLI Bash シェルで次の環境変数を設定する**:

チャネル情報と orderer 組織の情報を設定します

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=<ordererDNSZone>
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
ピア組織の情報を設定します

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

ピア組織と orderer 組織の間でさまざまな公開証明書を共有するため、Azure ファイル共有を 1 つ作成します。

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry 2020-01-01 --https-only --permissions lruw --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**チャネル管理コマンド**

orderer 組織の AKS クラスターに移動し、次のコマンドを発行して新しいチャネルを作成します

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**コンソーシアム管理コマンド**

次のコマンドを指定された順序で実行し、チャネルとコンソーシアムにピア組織を追加します。

1. ピア組織の AKS クラスターに移動し、そのメンバー サービス プロバイダー (MSP) を Azure File Storage にアップロードします。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. orderer 組織の AKS クラスターに移動し、チャネルとコンソーシアムにピア組織を追加します。

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. ピア組織に戻り、次のコマンドを発行してチャネルにピア ノードを参加させます。

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

同様に、チャネルにより多くのピア組織を追加するには、必要なピア組織に従ってピア AKS 環境変数を更新し、ステップ 1 から 3 を実行します。

**チェーンコード管理コマンド**

チェーンコード関連の操作を行うには、次のコマンドを実行します。 これらのコマンドでは、すべての操作がデモ チェーンコードに対して実行されます。 このデモ チェーンコードには、"a" と "b" の 2 つの変数があります。 チェーンコードのインスタンス化では、"a" は 1000 で初期化され、"b" は 2000 で初期化されます。 チェーンコードを呼び出すたびに、10 個のユニットが "a" から "b" に転送されます。 チェーンコードのクエリ操作では、"a" 変数のワールド状態が表示されます。

ピア組織の AKS クラスターで、次のコマンドを実行します。

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**チェーンコード操作コマンド**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>ネイティブ HLF 操作を実行する

お客様が AKS 上の HLF ネットワークで Hyperledger のネイティブ コマンドの実行を始められるようにします。 Fabric NodeJS SDK を使用して HLF 操作を実行するサンプル アプリケーションが用意されています。 新しいユーザー ID を作成し、独自のチェーンコードをインストールするためのコマンドが用意されています。

### <a name="before-you-begin"></a>開始する前に

次のコマンドを実行して、アプリケーションの初期セットアップを行います。

- アプリケーション ファイルをダウンロードする
- 接続プロファイルと管理プロファイルを生成する
- 管理者ユーザー ID をインポートする

初期セットアップが完了したら、SDK を使用して以下の操作を行うことができます。

- ユーザー ID の生成
- チェーンコード操作

上記のコマンドは、Azure Cloud Shell から実行できます。

### <a name="download-application-files"></a>アプリケーション ファイルをダウンロードする

アプリケーションを実行するための最初のセットアップでは、フォルダー内のすべてのアプリケーション ファイルをダウンロードします。

**アプリ フォルダーを作成してフォルダーに入る**:

```bash
mkdir app
cd app
```
次のコマンドを実行して、必要なすべてのファイルとパッケージをダウンロードします。

```bash-interactive
curl https://raw.githubusercontent.com/ravastra/ARM-template-for-Hyperledger-Fabric-based-on-AKS/master/application/setup.sh | bash
```
このコマンドでは、すべてのパッケージの読み込みに時間がかかります。 コマンドが正常に実行されると、現在のディレクトリに `node_modules` フォルダーが表示されます。 必要なすべてのパッケージが `node_modules` フォルダーに読み込まれます。

### <a name="generate-connection-profile-and-admin-profile"></a>接続プロファイルと管理プロファイルを生成する

`app` フォルダー内に `profile` ディレクトリを作成します

```bash
cd app
mkdir ./profile
```
Azure Cloud Shell で次の環境変数を設定します

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

次のコマンドを実行して、組織の接続プロファイルと管理者プロファイルを生成します

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

プロファイル フォルダーに、組織の接続プロファイルと管理者 `profile` が、それぞれ `<orgname>-ccp.json` および `<orgname>-admin.json` という名前で作成されます。

同様に、orderer 組織とピア組織の接続プロファイルと管理者プロファイルを生成します。


### <a name="import-admin-user-identity"></a>管理者ユーザー ID をインポートする

最後のステップは、組織の管理者ユーザー ID をウォレットにインポートすることです。

```bash
npm run importAdmin -- -o <orgName>

```
上記のコマンドでは、importAdmin.js が実行されて、管理者ユーザー ID がウォレットにインポートされます。 このスクリプトでは、管理者プロファイル `<orgname>-admin.json` から管理者 ID が読み取られて、HLF 操作を実行するためにウォレットにインポートされます。

スクリプトでは、ファイル システム ウォレットを使用して ID が格納されます。 接続プロファイルの ".wallet" フィールドで指定されているパスに従って、ウォレットが作成されます。 既定では、".wallet" フィールドは `<orgname>` で初期化されます。これは、ID を格納するために、`<orgname>` という名前のフォルダーが現在のディレクトリに作成されることを意味します。 他のパスにウォレットを作成する場合は、管理者ユーザー登録および他の HLF 操作を実行する前に、接続プロファイルの ".wallet" フィールドを変更します。

同様に、各組織の管理者ユーザー ID をインポートします。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください。

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>ユーザー ID の生成

以下のコマンドを指定された順序で実行して、HLF 組織用の新しいユーザー ID を生成します。

> [!NOTE]
> ユーザー ID の生成手順を始める前に、アプリケーションの初期セットアップが完了していることを確認します。

Azure Cloud Shell で次の環境変数を設定します

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

新しいユーザーを登録します

新しいユーザーを登録するには、registerUser.js を実行する次のコマンドを実行します。 生成されたユーザー ID はウォレットに保存されます。

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> 新規ユーザー登録コマンドを発行するには、管理者ユーザー ID が使用されます。 そのため、このコマンドを実行する前に、ウォレットに管理者ユーザー ID を作成しておく必要があります。 そうしないと、このコマンドは失敗します。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>チェーンコード操作


> [!NOTE]
> チェーンコードの操作を始める前に、アプリケーションの初期セットアップが完了していることを確認します。

Azure Cloud Shell で次のチェーンコード固有の環境変数を設定します

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

次のチェーンコード操作を実行できます。

- チェーンコードをインストールする
- チェーンコードをインスタンス化する
- チェーンコードを呼び出す
- チェーンコードのクエリを実行する

### <a name="install-chaincode"></a>チェーンコードをインストールする

ピア組織にチェーンコードをインストールするには、次のコマンドを実行します。

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
`ORGNAME` 環境変数で設定されている組織のすべてのピア ノードに、チェーンコードがインストールされます。 チャネルに 2 つ以上のピア組織があり、それらのすべてにチェーンコードをインストールする場合は、ピア組織ごとにコマンドを個別に実行します。

次の手順のようにします。

- `ORGNAME` を `<peerOrg1Name>` に設定し、`installCC` コマンドを発行します。
- `ORGNAME` を `<peerOrg2Name>` に設定し、`installCC` コマンドを発行します。

  各ピア組織に対して実行します。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>チェーンコードをインスタンス化する

ピアのチェーンコードをインスタンス化するには、次のコマンドを実行します。

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
`<instantiateFunc>` と `<instantiateFuncArgs>` で、インスタンス化関数名とコンマ区切りの引数リストを渡します。 たとえば、[fabrcar チェーンコード](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)では、チェーンコードをインスタンス化するために、`<instantiateFunc>` を `"Init"` に、`<instantiateFuncArgs>` を空の文字列 `""` に設定しています。

> [!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。
> トランザクションが orderer に正常に送信されると、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 そのため、チャネル内のすべてのピア組織のすべてのピア ノードで、チェーンコードがインスタンス化されます。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>チェーンコードを呼び出す

チェーンコード関数を呼び出すには、次のコマンドを実行します。

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
`<invokeFunction>` と `<invokeFuncArgs>` で、呼び出し関数名とコンマ区切りの引数リストを渡します。 fabcar チェーンコードの例では、initLedger 関数を呼び出すために、`<invokeFunction>` を `"initLedger"` に設定し、`<invokeFuncArgs>` を `""` に設定しています。

> [!NOTE]
> チャネル内の任意の 1 つのピア組織から、コマンドを 1 回実行します。
> トランザクションが orderer に正常に送信されると、orderer により、このトランザクションがチャネル内のすべてのピア組織に配布されます。 そのため、チャネル内のすべてのピア組織のすべてのピア ノードで、ワールド状態が更新されます。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>チェーンコードのクエリを実行する

チェーンコードのクエリを行うには、次のコマンドを実行します。

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

`<queryFunction>` と `<queryFuncArgs>` で、クエリ関数名とコンマ区切りの引数リストを渡します。 ここでも、`fabcar` チェーンコードを参照すると、ワールド状態のすべての自動車のクエリを実行するために、`<queryFunction>` を `"queryAllCars"` に設定し、`<queryArgs>' to ` を "" に設定しています。

コマンドに渡す引数の詳細については、コマンドのヘルプを参照してください

```bash
npm run queryCC -- -h

```
