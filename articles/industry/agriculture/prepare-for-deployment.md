---
title: Azure FarmBeats のデプロイ
description: FarmBeats をデプロイする方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: acc64486ac8bd15a9abab1f2010ea56b752a1e86
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927668"
---
# <a name="deploy-farmbeats"></a>FarmBeats のデプロイ

この記事では、Azure FarmBeats の設定方法について説明します。

Azure FarmBeats は、データ駆動形農業向けの業界固有の拡張可能なソリューションであり、シームレスなプロビジョニングと、Azure クラウド、利用統計情報の収集、および集計へのセンサー デバイスの接続を可能にします。 Azure FarmBeats には、カメラ、ドローン、土壌センサー、クラウドからのデバイスの管理などのさまざまなセンサーがあります。これには、IoT 対応 (モノのインターネット) デバイス用の Azure 内のインフラストラクチャとサービスから、視覚化、アラート、および分析情報を提供するための拡張可能な Web およびモバイル アプリまでが含まれます。

> [!NOTE]
> Azure FarmBeats は、パブリック クラウド環境でのみサポートされています。 クラウド環境の詳細については、[Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/) に関するページを参照してください。

Azure FarmBeats には、次の 2 つのコンポーネントがあります。

- **データ ハブ** - データ ハブは、既存または新規のデータ パイプラインからデータを構築、保存、処理し、分析情報を引き出せるようにする Azure FarmBeats のプラットフォーム レイヤーです。 このプラットフォーム レイヤーは、農業データ パイプラインおよびモデルを実行し、構築するのに役立ちます。

- **アクセラレータ** - アクセラレータは、事前に作成された農業モデルを使用して Azure FarmBeats の機能を示す組み込みアプリケーションを備えた Azure FarmBeats のソリューション レイヤーです。 このソリューション レイヤーを使用すると、ファーム境界を作成し、ファーム境界のコンテキスト内で農業データから分析情報を引き出すことができます。

Azure FarmBeats の迅速なデプロイの所要時間は 1 時間未満です。 データ ハブとアクセラレータのコストは、使用量によって異なります。

## <a name="deployed-resources"></a>デプロイされるリソース

Azure FarmBeats のデプロイでは、サブスクリプション内に以下に示すリソースが作成されます。

|No.  |リソース名  |Azure FarmBeats コンポーネント  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  データ ハブ       |
|2  |    Application Insights      |     データ ハブ/アクセラレータ     |
|3  |Azure Cache for Redis   |データ ハブ   |
|4  |       Azure KeyVault    |  データ ハブ/アクセラレータ        |
|5  |    Time Series Insights       |     データ ハブ      |
|6 |      EventHub 名前空間    |  データ ハブ       |
|7  |    Azure Data Factory V2       |     データ ハブ/アクセラレータ      |
|8  |Batch アカウント    |データ ハブ   |
|9  |       ストレージ アカウント     |  データ ハブ/アクセラレータ        |
|10  |    ロジック アプリ        |     データ ハブ      |
|11  |    API 接続        |     データ ハブ      |
|12|      App Service      |  データ ハブ/アクセラレータ       |
|13 |    App Service プラン        |     データ ハブ/アクセラレータ      |
|14 |Azure Maps アカウント     |アクセラレータ    |
|15 |       Time Series Insights      |  データ ハブ     |

Azure FarmBeats は、Azure Marketplace からダウンロードできます。 Azure portal から直接アクセスできます。  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Marketplace 上に Azure FarmBeats オファーを作成する

Marketplace 内に Azure FarmBeats オファーを作成するには、次の手順を使用します。

1. Azure portal にサインインし、右上隅でお使いのアカウントを選び、Microsoft Azure FarmBeats をデプロイする Azure AD テナントに切り替えます。
2. Azure FarmBeats は、Azure Marketplace で入手できます。 Marketplace のページで、[今すぐ入手する] を選択します。
3. [作成] を選択し、次の情報を入力します。
    - サブスクリプション名。
    - 既存のリソース グループ名 (空のリソース グループのみ)。または、Azure FarmBeats をデプロイするための新しいリソース グループを作成します。 このリソース グループは、以降のセクションのために書き留めておきます。
4. Azure FarmBeats をインストールするリージョン。 現在、FarmBeats では次のリージョンがサポートされています:米国中部、西ヨーロッパ、米国東部 2、北ヨーロッパ、東南アジア、米国東部、オーストラリア東部、米国西部 2。
5. **[OK]** を選択します。
利用規約ページが表示されます。 Marketplace の標準使用条件を確認するか、ハイパーリンクを選択して利用規約を確認します。
6. **[閉じる]** を選択し、[同意する] チェックボックスをオンにして、 **[作成]** を選択します。
7. これで、Marketplace 上で Azure FarmBeats のエンドユーザー使用許諾契約書 (EULA) に正常に署名されました。  
7. デプロイを続行するには、このガイドの次の手順に従います。

## <a name="prepare"></a>準備

Azure FarmBeats をデプロイするには、次のアクセス許可が必要です。

- テナント:読み取りアクセス
- サブスクリプション: 共同作成者または所有者
- リソース グループ: 所有者

## <a name="before-you-begin"></a>開始する前に

デプロイを開始する前に、以下があることを確認してください。

- Sentinel アカウント
- Azure Active Directory (AD) アプリ登録

## <a name="create-a-sentinel-account"></a>Sentinel アカウントの作成    

Sentinel があるアカウントを使用すると、公式 Web サイトから自分のデバイスに sentinel 衛星画像をダウンロードできます。 無料アカウントを作成するには、次の手順に従います。

[https://www.powershellgallery.com/packages/Az.ApplicationMonitor](https://scihub.copernicus.eu/dhus/#/self-registration ) にアクセスします。 登録ページで、名、姓、ユーザー名、パスワード、およびメールを入力します。
確認のために、登録したメール アドレスに確認メールが送信されます。 リンクを選択して確認します。 登録プロセスが完了しました。

## <a name="create-azure-ad-app-registration"></a>Azure AD アプリの登録を作成する

Azure FarmBeats での認証と承認については、次のような Azure Active Directory アプリケーションの登録が必要です。

- ケース 1:インストーラーによって自動的に作成できます (必要なテナント、サブスクリプション、およびリソース グループのアクセス許可を持っている場合)。
- ケース 2:Azure FarmBeats をデプロイする前に、作成して構成することができます (手動の手順が必要です)。

**ケース 1**:AAD アプリ登録を作成するためのアクセス権がある場合は、この手順をスキップして、インストーラーにアプリ登録を作成させることができます。 次のセクションに進んでください:[input.json ファイルを準備する](#prepare-input-json-file)

既にサブスクリプションを持っている場合は、次の手順に直接移動できます。

**ケース 2**:この方法は、サブスクリプション内で Azure AD アプリの登録を作成および構成するための十分な権限がない場合に推奨される手順です。 管理者に[カスタム スクリプト](https://aka.ms/FarmBeatsAADScript)の使用を依頼します。これは、IT 管理者が Azure portal 上で Azure AD アプリの登録を自動的に生成して構成するのに役立ちます。 PowerShell 環境を使用してこのカスタム スクリプトを実行するための出力として、IT 管理者は Azure Active Directory アプリケーション クライアント ID とパスワード シークレットをユーザーに伝える必要があります。 これらの値を書き留めておきます。

Azure AD アプリケーションの登録スクリプトを実行するには、次の手順を使用します。

1. [スクリプト](https://aka.ms/FarmBeatsAADScript)をダウンロードします。
2. Azure portal にサインインし、サブスクリプションと AD テナントを選択します。
3. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。

    ![プロジェクト FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)


4. 初回使用時には、ストレージ アカウントと Microsoft Azure ファイル共有を作成するためのサブスクリプションの選択を求められます。 **[Create storage]\(ストレージの作成\)** を選択します。
5. 初回使用時には、優先シェル エクスペリエンス (Bash または PowerShell) の選択を求められます。 [PowerShell] を選択します。
6. (手順 1 の) スクリプトを Cloud Shell にアップロードし、アップロードしたファイルの場所をメモします。

    > [!NOTE]
    > 既定では、ホーム ディレクトリにアップロードされます。

    次のスクリプトを実行します。

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Azure FarmBeats をデプロイするユーザーと共有するために、Azure AD アプリケーション ID とクライアント シークレットをメモしておきます。

### <a name="prepare-input-json-file"></a>入力 Json ファイルを準備する

インストールの一部として、次のような input.json ファイルを作成します。

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

このファイルは、Azure Cloud Shell に対する入力ファイルで、その中の値がインストール中にパラメーターとして使用されます。 json 内のすべてのパラメーターは、適切な値に置き換えるか、削除する必要があります。削除した場合は、インストール時にインストーラーによってプロンプトが表示されます


ファイルを準備する前に、パラメーターを確認してください。

|command | 説明|
|--- | ---|
|sku  | Azure FarmBeats のコンポーネントのいずれかまたは両方をダウンロードするかどうかを選択できます。 ダウンロードするコンポーネントを指定します。 データ ハブのみをインストールするには、"onlydatabhub" を使用します。 データ ハブとアクセラレータをインストールするには、"both" を使用します|
|subscriptionId | FarmBeats をインストールするためのサブスクリプションを指定します|
|datahubResourceGroup| データ ハブ リソースのリソース グループ名|
|location |リソースを作成する場所|
|acceleratorWebsiteName |データ ハブの名前に対する一意の URL プレフィックス|
|acceleratorResourceGroup  | アクセラレータ Web サイトの名前に対する一意の URL プレフィックス。|
|datahubWebsiteName  | データ ハブ Web サイトの名前に対する一意の URL プレフィックス。 |
|sentinelUsername | https://scihub.copernicus.eu/dhus/#/self-registration にサインインするためのユーザー名。|
|notificationEmailAddress  | データ ハブ内で構成するすべてのアラートの通知を受信するためのメール アドレス。|
|updateIfExists|[省略可能] 既存の FarmBeats インスタンスをアップグレードする場合にのみ、Input.Json 内に含まれるパラメーター。 アップグレードの場合、その他の詳細 (例: リソース グループ名や場所など) は同じである必要があります。|
|aadAppClientId | **[省略可能]** Azure AD アプリが既に存在する場合にのみ、Input.Json 内に含まれるパラメーター。  |
|aadAppClientSecret  | **[省略可能]** Azure AD アプリが既に存在する場合にのみ、Input.Json 内に含まれるパラメーター。|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Cloud Shell ブラウザーベースのコマンド ライン内でのデプロイ

上述の Marketplace ワークフローの一部として、1 つのリソース グループを作成し、エンドユーザー使用許諾契約書に署名したはずです。これは、実際のデプロイの一部としてもう一度確認できます。 デプロイは、Bash 環境を使用して Azure Cloud Shell (ブラウザーベースのコマンド ライン) を経由して実行できます。 Cloud Shell 経由でデプロイするには、次のセクションに進んでください。

> [!NOTE]
> 非アクティブな Cloud Shell セッションは 20 分後に期限切れになります。 この時間内にデプロイを完了するようにしてください。

1. Azure portal にサインインし、目的のサブスクリプションと AD テナントを選択します。
2. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。
3. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure ファイル共有を作成するためのサブスクリプションの選択を求められます。
4. **[ストレージの作成]** を選択します。  

環境を (PowerShell ではなく) Bash として選択します。

## <a name="deployment-scenario-1"></a>デプロイ シナリオ 1

インストーラーによって Azure AD アプリ登録が作成されます (上記のケース 1)

1. 次のテンプレートをコピーして、「input.json」という名前を付けます。  
サンプルの JSON 入力:

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

2. ファイルを保存し、(ローカル コンピューター上の) パスをメモしておきます。
3. Azure Cloud Shell に移動し、認証が正常に完了したら、[アップロード] を選択し (下の図の強調表示されたアイコンを参照)、input.json ファイルを Cloud Shell ストレージにアップロードします。  

    ![プロジェクト (Farm Beats)](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud Shell でホーム ディレクトリに移動します。 これは、既定では /home/<username> です
5. 次のコマンドを Cloud Shell に入力するか貼り付けます。 input. Json ファイルのパスを必ず変更し、Enter を押します。

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     インストーラーによって、すべての依存関係が自動的にダウンロードされ、デプロイ機能がビルドされます。 Azure FarmBeats エンドユーザー使用許諾契約書 (EULA) に同意するかどうかを確認するメッセージが表示されます。

     - 同意する場合は「Y」と入力し、次の手順に進みます。
     - 条項に同意しない場合は、「N」と入力するとデプロイが終了します。

6. その後、デプロイのアクセス トークンを入力するように求められます。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にログインします。

    > [!NOTE]
    > トークンは 60 分後に有効期限が切れます。 有効期限が切れた場合は、デプロイ コマンドをもう一度入力して再起動できます。

7. プロンプトが表示されたら、Sentinel アカウントのパスワードを入力します。
8. インストーラーにより検証され、デプロイが開始します。これには約 20 分かかります。
9. デプロイが成功すると、次の出力リンクが表示されます。

 - **データ ハブの URL**:Azure FarmBeats Api を試すための Swagger リンク。
 - **アクセラレータの URL**:Azure FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
 - **デプロイ機能ログ ファイル** - デプロイ中に作成されたログ ファイル。 必要な場合に、トラブルシューティングの目的で使用できます。

## <a name="deployment-scenario-2"></a>デプロイ シナリオ 2

既存の Azure Active Directory アプリ登録がデプロイに使用されます (上記のケース 2)

1. 以下の JSON ファイル (ここに Azure アプリケーション クライアント ID とパスワードを含めます) を input.json にコピーし、保存します。

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

残りの手順に従います。

2. (ローカル コンピューター上の) input.json ファイルへのパスをメモしておきます。
3. もう一度 Azure Cloud Shell に移動し、認証が正常に完了したら、[アップロード] ボタンを選択し (下の図の強調表示されたアイコンを参照)、input.json ファイルを Cloud Shell ストレージにアップロードします。

    ![プロジェクト (Farm Beats)](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud Shell でホーム ディレクトリに移動します。 これは、既定では /home/<username> です
5. 次のコマンドを Cloud Shell に入力するか貼り付けます。 input. Json ファイルのパスを必ず変更し、Enter を押します。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

画面の指示に従います。

6. スクリプトによって、すべての依存関係が自動的にダウンロードされ、デプロイ機能がビルドされます。
7. Azure FarmBeats エンドユーザー使用許諾契約書 (EULA) を読んで同意するかどうかを確認するメッセージが表示されます。

    - 同意する場合は「Y」と入力し、次の手順に進みます。
    - 条項に同意しない場合は、「N」と入力するとデプロイが終了します。

8. デプロイのアクセス トークンを入力するように求められます。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にサイン インします。
9. これで、インストーラーによってリソースが検証され、その作成が開始されます。これには約 20 分かかります。 この期間中は、Cloud Shell 上でセッションをアクティブにしておきます。
10. デプロイが正常に行われると、次の出力リンクが表示されます。

 - **データ ハブの URL**:FarmBeats Api を試すための Swagger リンク。
 - **アクセラレータの URL**:FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
 - **デプロイ機能のログ ファイル**:デプロイ中に作成されたログ ファイル。 必要な場合に、トラブルシューティングの目的で使用できます。

問題が発生した場合は、「[トラブルシューティング](troubleshoot-project-farmbeats.md)」を確認してください。


## <a name="validate-deployment"></a>デプロイの検証

### <a name="data-hub"></a>データ ハブ

データ ハブのインストールが完了すると、Swagger インターフェイスを使用して Azure FarmBeats API にアクセスするための URL が次の形式で返されます: https://\<yourdatahub-website-name>.azurewebsites.net

1. Swagger を介してサインインするには、URL をコピーしてブラウザーに貼り付けます。
2. Azure portal の資格情報でサインインします。
3. サニティ テスト (オプション)

     - データ ハブ リンクを使用して Swagger ポータルに正常にサインインできます。これは、正常なデプロイへの出力として受信したものです。
     - 拡張型 Get API - [試してみる/実行] を選択します
     - 403 "未承認ユーザーです" などの例外ではなく、サーバー応答コード 200 を受信する必要があります。

### <a name="accelerator"></a>アクセラレータ

アクセラレータのインストールが完了すると、FarmBeats ユーザー インターフェイスにアクセスするための URL が次の形式で返されます: https://\<accelerator-website-name>.azurewebsites.net

1. アクセラレータからサインインするには、URL をコピーしてブラウザーに貼り付けます。
2. Azure portal の資格情報でサインインします。

## <a name="upgrade"></a>アップグレード

アップグレードの手順は、初回インストールに似ています。 次の手順に従います。

1. Azure portal にサインインし、目的のサブスクリプションと AD テナントを選択します。
2. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。

   ![プロジェクト (Farm Beats)](./media/prepare-for-deployment/navigation-bar-1.png)

3. シェルの左側のドロップダウンから環境として "Bash" を選びます。
4. 必要な場合にのみ、input.json ファイルに変更を加え、Azure Cloud Shell にアップロードします。 たとえば、受信する通知のメール アドレスを更新できます。
5. input.json ファイルを Azure Cloud Shell にアップロードします。
6. 次の 2 つのコマンドを Cloud Shell に入力するか貼り付けます。 パスを必ず input.json ファイルへのパスに変更し、Enter キーを押してください。

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
画面の指示に従います。

7. インストーラーによって、実行時に必要な入力が自動的に求められます。
8. デプロイ用のアクセス トークンを入力します。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にサイン インします。
9. Sentinel パスワード
10. これで、インストーラーによってリソースが検証され、その作成が開始されます。これには約 20 分かかります。
11. デプロイが成功すると、次の出力リンクが表示されます。
 - **データ ハブの URL**:FarmBeats Api を試すための Swagger リンク。
 - **アクセラレータの URL**:FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
 - **デプロイ機能のログ ファイル**: デプロイ中のログが保存されます。 トラブルシューティングの目的で使用できます。

> [!NOTE]
> 後で使用するために、上記の値をメモしておきます。


## <a name="uninstall"></a>アンインストール

現在、インストーラーを使用した FarmBeats の自動アンインストールはサポートされていません。 データ ハブまたはアクセラレータを削除するには、Azure portal 内で、これらのコンポーネントがインストールされているリソース グループを削除するか、リソースを手動で削除します。

たとえば、2 つの異なるリソース グループにデータ ハブとアクセラレータをデプロイした場合、これらのリソース グループは次のように削除します。

1. Azure portal にサインインします。
2. 右上隅でお使いのアカウントを選び、Microsoft FarmBeats をデプロイする Azure AD テナントに切り替えます。

   > [!NOTE]
   > アクセラレータを正常に動作させるには、データ ハブが必要です。 アクセラレータをアンインストールせずにデータ ハブをアンインストールすることはお勧めしません。

3. [リソース グループ] を選択し、削除するデータ ハブまたはアクセラレータ リソース グループの名前を入力します。
4. リソース グループ名を選択します。 もう一度名前を入力して再確認し、[削除] を選択してリソース グループとそのすべての基になるリソースを削除します。
5. または、各リソースを手動で削除することもできますが、この方法はお勧めしません。
7. データ ハブを削除/アンインストールするには、Azure 上のリソース グループに直接アクセスし、そこからリソース グループを削除します。

## <a name="next-steps"></a>次の手順

Azure FarmBeats をデプロイしました。 次は、[ファームの作成](manage-farms.md#create-farms)方法を確認します。
