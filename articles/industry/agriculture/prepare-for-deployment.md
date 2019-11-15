---
title: Azure FarmBeats のデプロイ
description: FarmBeats をデプロイする方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797244"
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

## <a name="prepare"></a>準備

Azure FarmBeats をデプロイするには、次のアクセス許可が必要です。

- テナント:読み取りアクセス
- サブスクリプション: 共同作成者から所有者
- リソース グループ: 所有者

## <a name="before-you-begin"></a>開始する前に

デプロイを開始する前に、以下があることを確認してください。

- Sentinel アカウント
- Azure Active Directory (アプリ登録)
- Azure FarmBeats

## <a name="create-a-sentinel-account"></a>Sentinel アカウントの作成    

Sentinel があるアカウントを使用すると、公式 Web サイトから自分のデバイスに sentinel 衛星画像をダウンロードできます。 無料アカウントを作成するには、次の手順に従います。

1. [https://www.powershellgallery.com/packages/Az.ApplicationMonitor](https://scihub.copernicus.eu/dhus/#/self-registration ) にアクセスします。 登録ページで、名、姓、ユーザー名、パスワード、およびメールを入力します。
2. **[Select Domain]\(ドメインの選択\)** ドロップダウン メニューで、 **[Land]\(陸\)** オプションを選択します。
3. **[Select Usage]\(用途の選択\)** ドロップダウン メニューで、 **[Education]\(教育\)** オプションを選択します。
4. **[Select Country]\(国の選択\)** ドロップダウン メニューで、お住まいの国を選択します。
5. **[Register]\(登録\)** を選択して、登録プロセスを完了します。

確認のために、登録したメール アドレスに確認メールが送信されます。 リンクを選択して確認します。 登録プロセスが完了しました。

## <a name="create-azure-ad-app-registration"></a>Azure AD アプリの登録を作成する

Azure FarmBeats での認証と承認については、次のような Azure Active Directory アプリケーションの登録が必要です。

- ケース 1:インストーラーによって自動的に作成できます (必要なテナント、サブスクリプション、およびリソース グループのアクセス許可を持っている場合)。
- ケース 2:Azure FarmBeats をデプロイする前に、作成して構成することができます (手動の手順が必要です)。

**ケース 1**:インストーラーでは、目的のサブスクリプション内で Azure Active Directory アプリケーションの登録を作成する権限を持っていることが前提となります。 登録するには、ポータルにサインインし、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** に移動します。

既にサブスクリプションを持っている場合は、次の手順に直接移動できます。

**ケース 2**:この方法は、サブスクリプション内で Azure AD アプリの登録を作成および構成するための十分な権限がない場合に推奨される手順です。 管理者に[カスタム スクリプト](https://aka.ms/FarmBeatsAADScript)の使用を依頼します。これは、IT 管理者が Azure portal 上で Azure AD アプリの登録を自動的に生成して構成するのに役立ちます。 PowerShell 環境を使用してこのカスタム スクリプトを実行するための出力として、IT 管理者は Azure Active Directory アプリケーション クライアント ID とパスワード シークレットをユーザーに伝える必要があります。 これらの値を書き留めておきます。

Azure AD アプリケーションの登録スクリプトを実行するには、次の手順を使用します。

1. 登録[スクリプト](https://aka.ms/FarmBeatsAADScript)を取得します。
2. Azure portal にサインインし、サブスクリプションと AD テナントを選択します。
3. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。

    ![プロジェクト FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)


4. 初回使用時には、ストレージ アカウントと Microsoft Azure ファイル共有を作成するためのサブスクリプションの選択を求められます。 **[Create storage]\(ストレージの作成\)** を選択します。
5. 初回使用時には、優先シェル エクスペリエンス (Bash または PowerShell) の選択を求められます。 [PowerShell] を選択します。
6. Cloud Shell で、次のコマンドを入力してスクリプトを実行します。

    ```powershell
    ./create_aad_script.ps1
    ```
7. Azure FarmBeats をデプロイするユーザーと共有するために、Azure AD アプリケーション ID とクライアント シークレットをメモしておきます。

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Marketplace 上に Azure FarmBeats オファーを作成する

Marketplace 内に Azure FarmBeats オファーを作成するには、次の手順を使用します。

1. **Azure portal** にサイン インし、右上隅でお使いのアカウントを選び、Microsoft Azure FarmBeats をデプロイする Azure AD テナントに切り替えます。
2. **[Search/Marketplace]\(検索/Marketplace\)** または **[リソースの作成]** を選択します。 「**FarmBeats**」と入力してオファーの詳細を表示します。 次の手順に進む前に、このページの aka.ms ハイパーリンクを使用して利用可能なガイドをダウンロードします。
3. **[作成]** を選択し、次の情報を入力します。

   - サブスクリプション名を入力します。
   - 既存のリソース グループ名 (空のリソース グループのみ) を入力するか、Azure FarmBeats をデプロイするための新しいリソース グループを作成します。 後の段階で input.json ファイルに入力するために、このリソース グループをメモしておきます。
   - Azure FarmBeats をインストールするリージョンを入力します。 FarmBeats は現在、次のリージョンにインストールできます。米国中部、西ヨーロッパ、米国東部 2、北ヨーロッパ、東南アジア、米国東部、オーストラリア東部、米国西部 2。
4. **[OK]** を選択すると、利用規約のページにリダイレクトされます。 Marketplace の標準使用条件を確認するか、ハイパーリンクを選択して利用規約を確認します。
5. **[閉じる]** を選択し、[同意する] チェックボックスをオンにして、 **[作成]** を選択します。
6. これで、Marketplace 上で Azure FarmBeats のエンドユーザー使用許諾契約書 (EULA) に正常に署名されました。 デプロイを続行するには、このガイドの次の手順に従います。

### <a name="prepare-inputjson-file"></a>Input.Json ファイルを準備する

このファイルは Azure Cloud Shell およびパラメーターへの入力ファイルであり、アップロード前にこのファイル内で指定された値はデプロイ時に要求されないため、時間を節約できます。  

> [!NOTE]
> このファイルによって Azure Cloud Shell に値が入力されます。  時間を節約するために、デプロイ時には、このファイルに追加するパラメーターの入力を求められません。 不足しているパラメーターの入力を求められます。

ファイルを準備する前に、パラメーターを確認してください。

|command | 説明|
|--- | ---|
|"sku"  | Azure FarmBeats のコンポーネントのいずれかまたは両方をダウンロードするかどうかを選択できます。 ダウンロードするコンポーネントを指定します。 データ ハブのみをインストールするには、"onlydatabhub" を使用します。 データハブとアクセラレータをインストールするには、"both" を使用します。|
|"subscriptionId"  | FarmBeats をインストールするためのサブスクリプションを指定します|
|"datahubResourceGroup"  | データ ハブ リソースのリソース グループ名。|
|"datahubLocation" | データ ハブ リソースを格納する場所。|
|"acceleratorWebsiteName"  |データ ハブの名前に対する一意の URL プレフィックス
Swagger Web サイト。 既定値はデータ ハブ リソース グループ名です。 既定値をそのまま使用するには Enter を押します。|
|"acceleratorResourceGroup"  | データ ハブ リソースのリソース グループ名。 |
|"acceleratorLocation"  | データ ハブ リソースを格納する場所
"acceleratorWebsiteName"  | アクセラレータ Web サイトの名前に対する一意の URL プレフィックス。 既定値は accelerator です。 既定値をそのまま使用するには Enter を押します。|
|"sentinelUsername" | https://scihub.copernicus.eu/dhus/#/self-registration にサインインするためのユーザー名。|
|"sentinelPassword"  | https://scihub.copernicus.eu/dhus/#/self-registration にサインインするためのパスワード。|
|"farmbeatsAppId"  | Team Azure FarmBeats によって共有される値。  |
|"farmbeatsPassword"  | Team Azure FarmBeats によって共有される値。|
|"notificationEmailAddress"  | データ ハブ内で構成するすべてのアラートの通知を受信するためのメール アドレス。|
|"upda"aadAppClientId""  |**[省略可能]** Azure AD アプリが既に存在する場合にのみ、Input.Json 内に含まれるパラメーター。  - True/False。 初回インストールの場合は False で、アップグレード シナリオの場合は True。|
|"aadAppClientId"  | **[省略可能]** Azure AD アプリが既に存在する場合にのみ、Input.Json 内に含まれるパラメーター。  |
|"aadAppClientSecret"   | **[省略可能]** Azure AD アプリが既に存在する場合にのみ、Input.Json 内に含まれるパラメーター。|


サンプルの JSON 入力:

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Cloud Shell ブラウザーベースのコマンド ライン内でのデプロイ

Marketplace ワークフローの一部として、1 つのリソース グループを作成し、エンドユーザー使用許諾契約書に署名しました。これは、実際のデプロイの一部としてもう一度確認できます。 デプロイは、Bash 環境を使用して Azure Cloud Shell (ブラウザーベースのコマンド ライン) を介して行われます。  

> [!NOTE]
> 非アクティブな Cloud Shell セッションは 20 分後に期限切れになります。 この時間内にデプロイを完了するようにしてください。

1. **Azure** portal にサインインし、目的のサブスクリプションと AD テナントを選択します。
2. **Azure** portal 上部のナビゲーションから **Cloud Shell** を起動します。

   ![プロジェクト FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

3. ストレージ アカウントと Microsoft Azure ファイル共有を作成するためのサブスクリプションを選択します。
4. **[Create storage]\(ストレージの作成\)** を選択します。
5. シェル ウィンドウ (Bash) の左側で環境ドロップダウンを選びます。

   ![プロジェクト FarmBeats](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>デプロイ シナリオ 1

インストーラーによって Azure AD が作成されます (AD テナントの読み取りアクセス許可があります)。  

1. [input.json](https://aka.ms/PPInputJsonTemplate) テンプレートをダウンロードします。 input.json ファイルに Azure アプリケーション クライアント ID とパスワードを含め、それを保存します。
2. ダウンロードしたファイルをメモ帳で開き、値を入力してファイルを設定します。

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > この手順を省略すると、実行時に入力を求めるメッセージが表示されます。

3. ファイルを保存し、(ローカル コンピューター上の) パスをメモしておきます。  
4. Azure Cloud Shell に移動し、認証が正常に完了したら、[アップロード] を選択し (下の図の強調表示されたアイコンを参照)、input.json ファイルを Cloud Shell ストレージにアップロードします。 インストーラーによって Azure AD アプリの登録が作成および構成されるため、json 内で Azure AD パラメーターを渡す必要はありません。

   ![プロジェクト FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

5. Cloud Shell に「Deployment Command」と入力するか貼り付けます。 input. Json ファイルのパスを必ず変更し、Enter を押します。  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   スクリプトによって、すべての依存関係が自動的にダウンロードされ、デプロイ機能が構築されます。 次に、Azure FarmBeats エンドユーザー使用許諾契約書 (EULA) に同意するかどうかを確認するメッセージが表示されます。

   - 同意する場合は「Y」と入力し、次の手順に進みます。
   - 条項に同意しない場合は、「N」と入力するとデプロイが終了します。

   その後、デプロイのアクセス トークンを入力するように求められます。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にログインします。

   > [!NOTE]
   > コードは 60 分後に有効期限が切れます。 有効期限が切れた場合は、デプロイ コマンドをもう一度入力して再起動できます。

6. 次のプロンプトで、sentinel アカウントのパスワードを入力します。
7. インストーラーにより検証され、デプロイが開始します。これには約 20 分かかります。
8. デプロイが成功すると、次の出力リンクが表示されます。
    - **データ ハブの URL**:Azure FarmBeats Api を試すための Swagger リンク。
    - **アクセラレータの URL**:Azure FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
    - **デプロイ機能ログ ファイル** - デプロイ中に作成されたログ ファイル。 トラブルシューティングの目的で使用できます。

    - 同意する場合は「Y」と入力し、次の手順に進みます。
    - 条項に同意しない場合は、「N」と入力するとデプロイが終了します。

   その後、デプロイのアクセス トークンを入力するように求められます。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にサイン インします。

   > [!NOTE]
    > これらをメモし、後で使用するためにデプロイ ログ ファイルのパスを手元に置いておきます。


### <a name="deployment-scenario-2"></a>デプロイ シナリオ 2

既存の Azure Active Directory アプリの登録は、デプロイに使用されます。

1. [input.json](https://aka.ms/PPInputJsonTemplate) をダウンロードし、Azure アプリケーション クライアント ID とパスワードを input.json に含め、それを保存します。

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

    ![プロジェクト FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud Shell に「*Deployment Command*」と入力するか貼り付けます。 input. Json ファイルのパスを必ず変更し、Enter を押します。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   残りの手順に従います。

5. スクリプトによって、すべての依存関係が自動的にダウンロードされ、デプロイ機能が構築されます。
6. Azure FarmBeats エンドユーザー使用許諾契約書 (EULA) を読んで同意するかどうかを確認するメッセージが表示されます。

   - 同意する場合は「Y」と入力し、次の手順に進みます。
   - 条項に同意しない場合は、「N」と入力するとデプロイが終了します。

7. デプロイのアクセス トークンを入力するように求められます。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にサイン インします。
8. これで、インストーラーによってリソースが検証され、その作成が開始されます。これには約 20 分かかります。 この期間中は、Cloud Shell 上でセッションをアクティブにしておきます。
9. デプロイが正常に行われると、次の出力リンクが表示されます。
   - **データ ハブの URL**:FarmBeats Api を試すための Swagger リンク。
   - **アクセラレータの URL**:FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
   - **デプロイ機能ログ ファイル**: デプロイ時にログを保存し、トラブルシューティングに使用できます。

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
3. オプションのサニティ テストを実行します。

    - 正常なデプロイへの出力として受信したアクセラレータ リンクを使用して、アクセラレータ ポータルに正常にサインインできるかどうかを確認します。
    - **[Create farm]\(ファームの作成\)** を選択します。
    - アイコン "?" の下で、 **[Get started]\(開始\)** ボタンを使用して、FarmBeats ガイドを開きます。

## <a name="upgrade"></a>アップグレード

アップグレードの手順は、初回インストールに似ています。 次の手順に従います。

1. Azure portal にサインインし、目的のサブスクリプションと AD テナントを選択します。
2. Cloud Shell は、Azure Portal の上部のナビゲーションから起動します。

   ![プロジェクト FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

3. ストレージ アカウントと Azure Files ファイル共有の作成に使用するサブスクリプションを選択します。
4. **[Create storage]\(ストレージの作成\)** を選択します。
5. シェル (Bash) の左側のドロップダウンから環境を選びます。
6. 必要な場合にのみ、input.json ファイルに変更を加え、Azure Cloud Shell にアップロードします。 たとえば、受信する通知のメール アドレスを更新できます。
7. (ローカル コンピューター上の) input.json ファイルのパスをメモしておきます。
8. Azure Cloud Shell に移動します。 認証が正常に完了したら、[アップロード] ボタンを選択し (下の図の強調表示されたアイコンを参照)、input.json ファイルを Cloud Shell ストレージにアップロードします。

   ![プロジェクト FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

9. Cloud Shell に「**Deployment Command**」と入力するか貼り付けます。 input. json ファイルのパスを必ず変更し、Enter を押します。

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    残りの手順に従います。

10. インストーラーによって、実行時に必要な入力が自動的に求められます。

    - デプロイ用のアクセス トークンを入力します。 生成されたコードをコピーし、Azure の資格情報を使用して https://microsoft.com/devicelogin にサイン インします。

     > [!NOTE]
     > コードは 60 分後に有効期限が切れます。 有効期限が切れた場合は、デプロイ コマンドをもう一度入力して再起動できます。

     - Sentinel パスワード

11. これで、インストーラーによってリソースが検証され、その作成が開始されます。これには約 20 分かかります。
12. デプロイが成功すると、次の出力リンクが表示されます。

    - **データ ハブの URL**:FarmBeats Api を試すための Swagger リンク。
    - **アクセラレータの URL**:FarmBeats Smart Farm Accelerator を探索するためのユーザー インターフェイス。
    - **デプロイ機能ログ ファイル**: デプロイ時にログを保存します。 トラブルシューティングの目的で使用できます。

    > [!NOTE]
    > 上記のリンクをメモし、後で使用するためにデプロイ ログ ファイルのパスを手元に置いておきます。

## <a name="uninstall"></a>アンインストール

現在、インストーラーを使用した FarmBeats の自動アンインストールはサポートされていません。 データ ハブまたはアクセラレータを削除するには、Azure portal 内で、これらのコンポーネントがインストールされているリソース グループを削除するか、リソースを手動で削除します。

たとえば、2 つの異なるリソース グループにデータ ハブとアクセラレータをデプロイした場合、これらのリソース グループは次のように削除します。

1. Azure portal にサインインします。
2. 右上隅でお使いのアカウントを選び、Microsoft FarmBeats をデプロイする Azure AD テナントに切り替えます。

   > [!NOTE]
   > アクセラレータを正常に動作させるには、データ ハブが必要です。 アクセラレータをアンインストールせずにデータ ハブをアンインストールすることはお勧めしません。

3. [リソース グループ] を選択し、削除するデータ ハブまたはアクセラレータ リソース グループの名前を入力します。
4. リソース グループ名を選択します。 もう一度名前を入力して再確認し、[削除] をクリックしてリソース グループとそのすべての基になるリソースを削除します。
5. または、各リソースを手動で削除することもできますが、この方法はお勧めしません。
7. データ ハブを削除/アンインストールするには、Azure 上のリソース グループに直接アクセスし、そこからリソース グループを削除します。

## <a name="next-steps"></a>次の手順

Azure FarmBeats をデプロイしました。 次は、[ファームの作成](manage-farms.md#create-farms)方法を確認します。
