---
title: Azure FarmBeats をインストールする
description: この記事では、Azure サブスクリプションに Azure FarmBeats をインストールする方法について説明します
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475745"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats をインストールする

この記事では、Azure サブスクリプションに Azure FarmBeats をインストールする方法について説明します。

Azure FarmBeats は、Azure Marketplace で入手できる企業間オファリングです。 複数のプロバイダーにまたがる農業データ セットを集約し、アクションにつながる分析情報を生成することができます。 これを Azure FarmBeats を使用して実行するには、合成したデータ セットに基づいて人工知能 (AI) または機械学習 (ML) モデルを構築します。 Azure FarmBeats の 2 つの主要なコンポーネントは次のとおりです。

- **データ ハブ**:さまざまなプロバイダーにまたがるさまざまな農業データ セットの集計、正規化、およびコンテキスト化を可能にする API レイヤー。

- **アクセラレータ**:データ ハブ上に構築されたサンプル Web アプリケーション。 これを使うと、モデルの開発と視覚化をすぐに始められます。 アクセラレータでは、Azure FarmBeats API を使って、取り込まれたセンサー データのグラフとしての視覚化とモデル出力のマップとしての視覚化の例を示します。

## <a name="before-you-start"></a>開始する前に
### <a name="components-installed"></a>インストールされるコンポーネント

Azure FarmBeats をインストールすると、次のリソースが Azure サブスクリプションにプロビジョニングされます。

| インストールされる Azure リソース  | Azure FarmBeats コンポーネント  |
|---------|---------|
| Application Insights   |      データ ハブとアクセラレータ      |
| App Service     |     データ ハブとアクセラレータ     |
| App Service プラン   | データ ハブとアクセラレータ  |
| API 接続    |  データ ハブ       |
| Azure Cache for Redis       | データ ハブ      |
| Azure Cosmos DB   |  データ ハブ       |
| Azure Data Factory V2       |     データ ハブとアクセラレータ      |
| Azure Batch アカウント    | データ ハブ   |
| Azure Key Vault |  データ ハブとアクセラレータ        |
| Azure Maps アカウント       |     アクセラレータ    |
| イベント ハブの名前空間    |     データ ハブ      |
| ロジック アプリ      |  データ ハブ       |
| ストレージ アカウント      |     データ ハブとアクセラレータ      |
| Time Series Insights     |    データ ハブ    |

### <a name="costs-incurred"></a>発生するコスト

Azure FarmBeats のコストは、基になる Azure サービスのコストを集計したものです。 Azure サービスの料金情報は、[料金計算ツール](https://azure.microsoft.com/pricing/calculator)を使用して計算できます。 インストール全体の実際のコストは、使用量によって変わります。 2 つのコンポーネントの安定した状態のコストは次のとおりです。

* データ ハブ - 1 日あたり $10 未満
* アクセラレータ - 1 日あたり $2 未満

### <a name="regions-supported"></a>サポートされているリージョン

現時点で、Azure FarmBeats は次のリージョンのパブリック クラウド環境でサポートされています。
* オーストラリア東部
* 米国中部
* East US
* 米国東部 2
* 米国西部
* 米国西部 2
* 北ヨーロッパ
* 西ヨーロッパ
* 東南アジア

### <a name="time-taken"></a>所要時間

準備とインストールを含め、Azure FarmBeats の設定全体にかかる時間は 1 時間未満です。

## <a name="prerequisites"></a>前提条件    

Azure FarmBeats の実際のインストールを開始する前に、次の手順を完了する必要があります。

### <a name="create-sentinel-account"></a>Sentinel アカウントを作成する
Azure FarmBeats の設定を使用すると、ファームの欧州宇宙機関の [Sentinel-2](https://scihub.copernicus.eu/) 衛星ミッションから無料の衛星画像を取得できます。 この設定を構成するには、Sentinel アカウントが必要です。

次の手順に従って、Sentinel で無料アカウントを作成します。

1. 公式の[サインアップ](https://scihub.copernicus.eu/dhus/#/self-registration) ページにアクセスします。
2. 必要な詳細 (名、姓、ユーザー名、パスワード、およびメール ID) を入力し、フォームを完成させます。
3. 登録されているメール ID に確認リンクが送信されます。 メールに記載されているリンクを選択し、検証を完了します。

検証が完了すると、登録プロセスは完了です。 **[Sentinel Username]\(Sentinel ユーザー名\)** と **[Sentinel Password]\(Sentinel パスワード\)** をメモしておきます。

### <a name="decide-subscription-and-region"></a>サブスクリプションとリージョンを決定する

Azure FarmBeats をインストールする Azure サブスクリプション ID とリージョンが必要です。 「[サポートされているリージョン](#regions-supported)」セクションに一覧表示されているリージョンのいずれかを選択します。

**[Azure サブスクリプション ID]** と **[Azure リージョン]** をメモしておきます。

### <a name="verify-permissions"></a>アクセス許可を確認する

Azure FarmBeats をインストールする Azure テナントで、十分な特権とアクセス許可を持っているかどうかを確認する必要があります。

Azure portal でアクセス許可を確認するには、[ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/check-access)の手順に従います。

Azure FarmBeats をインストールするには、次のアクセス許可が必要です。
- テナント - 読み取りアクセス権
- サブスクリプション - 共同作成者または所有者
- リソース グループ - 所有者。

さらに、Azure FarmBeats には Azure Active Directory アプリケーション登録が必要です。 必要な Azure AD の設定を完了するには、次の 2 つの方法があります。

**ケース 1**:インストール先の Azure テナントに**書き込み**アクセス許可を持っています。 このケースは、インストール時に AAD アプリの登録を動的に作成するために必要なアクセス許可を持っていることを意味します。

[Marketplace のサインアップの完了](#complete-azure-marketplace-sign-up)に関するセクションにそのまま進むことができます。


**ケース 2**:Azure テナントに**書き込み**アクセス許可を持っていません。 このケースは、会社の Azure サブスクリプションに Azure FarmBeats をインストールしようとしたときに、あなたの**書き込み**アクセス権が、所有しているリソース グループのみに制限されている場合によく見られます。
このケースでは、次の手順に従って、Azure portal で Azure AD アプリの登録を自動的に生成して完了するように IT 管理者に依頼してください。

1. [AAD アプリ ジェネレーター スクリプト](https://aka.ms/FarmBeatsAADScript)をローカル コンピューターにダウンロードして展開します。
2. Azure portal にサインインし、サブスクリプションと Azure AD テナントを選択します。
3. Azure portal の上部にあるツール バーから Cloud Shell を起動します。

    ![プロジェクト FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. 好みのシェル エクスペリエンスとして PowerShell を選択します。 初めて使用するユーザーの場合、サブスクリプションを選択し、ストレージ アカウントを作成するように求められます。 指示に従って設定を完了します。
5. (手順 1 の) スクリプトを Cloud Shell にアップロードし、アップロードしたファイルの場所をメモします。

    > [!NOTE]
    > 既定では、ファイルはホーム ディレクトリにアップロードされます。

6. 'cd' コマンドを使用してホーム ディレクトリに移動し、次のスクリプトを実行します。

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. **データ ハブの Web サイト**名と **アクセラレータの Web サイト**名を入力します。 スクリプトの出力をメモし、Azure FarmBeats をインストールするユーザーと共有します。

IT 管理者から必要な詳細情報を受け取ったら、**AAD クライアント ID、AAD クライアント シークレット、データ ハブの Web サイト名、アクセラレータの Web サイト名**をメモしておきます。

   > [!NOTE]
   > ケース 2 の手順を実行している場合は、必ず AAD クライアント ID と AAD クライアント シークレットを[パラメーター ファイル](#prepare-parameters-file)に別のパラメーターとして追加してください。

これで、次のセクションに進むために必要なすべての情報が得られました。

### <a name="complete-azure-marketplace-sign-up"></a>Azure Marketplace のサインアップを完了する

クラウドシェル ベースのインストール プロセスを開始する前に、Azure マーケットプレースで Azure FarmBeats オファーへのサインアップを完了しておく必要があります。 サインアップを完了するには、次の手順を実行します。

1.  Azure portal にサインインします。 右上隅でお使いのアカウントを選択し、Azure FarmBeats をインストールする Azure AD テナントに切り替えます。

2.  ポータルで Azure Marketplace にアクセスし、Marketplace で **Azure FarmBeats** を検索します

3.  Azure FarmBeats の概要が表示された新しいウィンドウが開きます。 **[作成]** を選択します。

4.  新しいウィンドウが開きます。 Azure FarmBeats をインストールする適切なサブスクリプション、リソース グループ、および場所を選択して、サインアップ プロセスを完了します。

5.  入力した詳細が検証されたら、 **[OK]** を選択します。 利用規約ページが表示されます。 利用規約を確認し、 **[作成]** を選択してサインアップ プロセスを完了します。

この手順では、Azure Marketplace でのサインアップ プロセスを完了します。 これで、パラメーター ファイルの準備を始められるようになりました。

### <a name="prepare-parameters-file"></a>パラメーター ファイルを準備する
前提条件フェーズの最後の手順は、Cloud Shell のインストール時に入力として機能する JSON ファイルを作成することです。 JSON ファイル内のパラメーターは、適切な値に置き換える必要があります。

サンプルの JSON ファイルを以下に示します。 サンプルをダウンロードし、必要な詳細を更新します。

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

各パラメーターの詳細については、以下のパラメーター一覧を参照してください。

| パラメーター | 値|
|--- | ---|
|sku  | データ ハブとアクセラレータの両方をインストールするか、データ ハブのみをインストールするかの選択肢をユーザーに与えます。 データ ハブのみをインストールするには、"datahub" を使用します。 データ ハブとアクセラレータをインストールするには、"both" を使用します。|
|subscriptionId | Azure FarmBeats をインストールする Azure サブスクリプションを指定します|
|datahubResourceGroup| データ ハブ リソースのリソース グループ名を指定します。 Azure Marketplace で作成したリソース グループ名をここに入力します|
|location |Azure FarmBeats をインストールする場所または Azure リージョン|
|datahubWebsiteName  | データ ハブ Web アプリケーションの一意の URL プレフィックス |
|acceleratorResourceGroup  | アクセラレータ リソースのリソース グループ名を指定します|
|acceleratorWebsiteName |アクセラレータ Web アプリケーションの一意の URL プレフィックス|
|sentinelUsername | Sentinel の衛星画像を取得するユーザー名|
|notificationEmailAddress  | データ ハブ内で構成するすべてのアラートの通知を受信するためのメール アドレス。|
|updateIfExists| (省略可能) 既存の Azure FarmBeats インスタンスをアップグレードする場合にのみ、パラメーター ファイルに含めるパラメーター。 アップグレードの場合、リソース グループの名前や場所などのその他の詳細は同じである必要があります|
|aadAppClientId | (省略可能) 事前に作成された AAD アプリを使用している場合にのみ、パラメーター ファイルに含めるパラメーター。 詳細については、「[アクセス許可を確認する](#verify-permissions)」のケース 2 を参照してください。 |
|aadAppClientSecret  | (省略可能) 事前に作成された AAD アプリを使用している場合にのみ、パラメーター ファイルに含めるパラメーター。 詳細については、「[アクセス許可を確認する](#verify-permissions)」のケース 2 を参照してください。|

上記の表とサンプル JSON ファイルに基づいて、パラメーター JSON ファイルを作成し、ローカル コンピューターに保存します。

## <a name="install"></a>インストール

Azure FarmBeats リソースの実際のインストールは、Bash 環境を使用して Cloud Shell ブラウザーベースのコマンドライン インターフェイスで行われます。 Azure FarmBeats をインストールするには、次の手順を実行します。

1. Azure portal にサインインします。 Azure FarmBeats をインストールする Azure サブスクリプションとテナントを選択します。
2. Azure portal の右上隅にあるツールバーから **Cloud Shell** を起動します。
3. 優先するシェル エクスペリエンスとして Bash を選択します。 (下の図で強調表示されている) **[アップロード]** ボタンを選択し、準備されたパラメーターの JSON ファイルをアップロードします。

      ![プロジェクト FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. 次のコマンドを **[コピー]** し、 **\<username> を正しい値に置き換え**て、アップロードされたファイルの正しいパスをコマンドが指すようにします。

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. 変更したコマンドを実行して、インストール プロセスを開始します。 次のようなメッセージが表示されます。
 - **Azure FarmBeats のライセンス**条項に同意します。 使用条件に同意する場合は、「Y」と入力して次の手順に進みます。 使用条件に同意しない場合は、「N」と入力してインストールを終了します。

 - 次に、インストール用のアクセス トークンを入力するように求められます。 生成されたコードをコピーし、**Azure の資格情報**を使用して[デバイス ログイン ページ](https://microsoft.com/devicelogin)にログインします。

 - サインインが正常に完了すると、インストーラーによって、Sentinel アカウントのパスワードの入力が求められます。 **Sentinel アカウントのパスワード**を入力します。

6. パラメーター ファイルが検証され、Azure リソースのインストールが開始されます。 インストールが完了するまでに約 **25 分**かかります。    
> [!NOTE]
> 非アクティブな Cloud Shell セッションは **20 分**後に期限切れになります。 インストーラーによって Azure リソースがデプロイされている間は、Cloud Shell セッションのアクティブな状態を維持してください。 セッションがタイムアウトした場合は、インストール プロセスを再起動する必要があります。

インストールが完了すると、次の出力リンクを受け取ります。
* **データ ハブの URL**:データ ハブ API にアクセスするための Swagger リンク。
* **アクセラレータの URL**:Azure FarmBeats アクセラレータを探索するための Web アプリケーション。
* **インストーラーのログ ファイル**:インストールの詳細を含むログ ファイル。 このログ ファイルは、必要に応じて、インストールのトラブルシューティングに使用できます。

Azure FarmBeats のインストールが完了したことを確認するには、次のチェックを実行します。

**データ ハブ**
1. Azure の資格情報を使用して、(**https://\<yourdatahub-website-name>.azurewebsites.net/swagger** という形式で) 提供されたアクセラレータの URL にログインします。
2. さまざまな FarmBeats API オブジェクトを表示し、API に対して REST 操作を実行することができるようになります。

**アクセラレータ**
1. Azure の資格情報を使用して、(**https://\<youraccelerator-website-name>.azurewebsites.net/swagger** という形式で) 提供されたアクセラレータの URL にログインします。
2. ブラウザーでファームを作成するオプションを使用して、アクセラレータ ユーザー インターフェイスを表示できるようになります。

上記の操作を実行できる場合、Azure FarmBeats が正常にインストールされたことを示します。

## <a name="upgrade"></a>アップグレード
パブリック プレビュー バージョンでは、Azure FarmBeats の既存のインストールをアップグレードするには、Cloud Shell で Installation コマンドをもう一度実行する必要があります。その際に、パラメーター ファイルに "**true**" に設定した "**updateIfExists**" パラメーターを追加します。 update パラメーターについては、以下の JSON サンプルの最後の行を参照してください。

```json
{
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```
このコマンドを実行すると、既存の Azure FarmBeats インストールが最新バージョンに更新され、出力リンクが表示されます。

## <a name="uninstall"></a>アンインストール

Azure FarmBeats データ ハブ または アクセラレータ をアンインストールするには、次の手順を実行します。

1.  Azure portal にログインし、これらのコンポーネントがインストールされている**リソース グループを削除**します。

2.  Azure Active Directory に移動し、Azure FarmBeats のインストールにリンクされている **Azure AD アプリケーション削除**します。 これで、Azure サブスクリプションから Azure FarmBeats のインストールが削除されます。

## <a name="next-steps"></a>次のステップ
ここでは、Azure サブスクリプションに Azure FarmBeats をインストールする方法を学習しました。 次は、Azure FarmBeats インスタンスに[ユーザーを追加する](manage-users-in-azure-farmbeats.md#manage-users)方法を確認します。
