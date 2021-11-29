---
title: Microsoft Azure Sentinel を使用した Azure Key Vault ハニートークンのデプロイと監視
description: Azure Key Vault ハニートークンのキーとシークレットを仕掛け、Microsoft Azure Sentinel でそれらを監視します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 93b805a52b220d1b1fadf76782516b8aa7d8f5b5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713081"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-microsoft-sentinel-public-preview"></a>Microsoft Azure Sentinel を使用した Azure Key Vault ハニートークンのデプロイと監視 (パブリック プレビュー)

> [!IMPORTANT]
> Microsoft Azure Sentinel Deception (ハニートークン) ソリューションは現在プレビューの段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

この記事では、**Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションを使用して、"*ハニートークン*" と呼ばれるおとりの [Azure Key Vault](../key-vault/index.yml) キーとシークレットを既存のワークロードに仕掛ける方法について説明します。

このソリューションで提供される[分析ルール](detect-threats-built-in.md)、[ウォッチリスト](watchlists.md)、[ブック](monitor-your-data.md)を使用して、デプロイされたハニートークンへのアクセスを監視します。

システムでハニートークンを使用する場合、検出の原則は変わりません。 ハニートークンにアクセスする正当な理由がないため、すべてのアクティビティは、環境に慣れていないユーザーが存在することを示し、攻撃者である可能性があります。

## <a name="before-you-begin"></a>開始する前に

**Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションの使用を開始するには、次のことを確認してください。

- **必要なロール**: **Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションをインストールするには、テナント管理者である必要があります。 このソリューションをインストールすると、そのブックをキー コンテナー所有者と共有して、独自のハニートークンをデプロイできるようになります。

- **必要なデータ コネクタ**: [Azure Key Vault](data-connectors-reference.md#azure-key-vault) と [Azure アクティビティ](data-connectors-reference.md#azure-activity) データ コネクタがワークスペースにデプロイされ、それらが接続されていることを確認します。

  データ ルーティングが成功し、**KeyVault** および **AzureActivity** データが Microsoft Azure Sentinel に送信されていることを確認します。 詳細については、次を参照してください。

  - [Microsoft Azure Sentinel を Azure、Windows、Microsoft、および Amazon サービスに接続する](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
  - [Microsoft Azure Sentinel データ コネクタを見つける](data-connectors-reference.md)

## <a name="install-the-solution"></a>ソリューションをインストールする

[他のソリューション](sentinel-solutions-deploy.md)と同じように **Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションをインストールします。 **Azure Sentinel Deception** ソリューション ページで、 **[開始]** を選択して作業を開始します。

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="ソリューションの作成ページのスクリーンショット。":::

**Deception ソリューションをインストールするには:**

次の手順では、**Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションに必要な特定のアクションについて説明します。

1. **[基本]** タブで、Microsoft Azure Sentinel ワークスペースが配置されているのと同じリソース グループを選択します。

1. **[前提条件]** タブの **[関数アプリ名]** フィールドに、キー コンテナーにハニートークンを作成する Azure 関数アプリのわかりやすい名前を入力します。

    関数アプリ名は一意である必要があります。長さは 2-22 文字、英数字のみです。

    定義した名前の下にコマンドが表示されます。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="更新された curl コマンドを示す [前提条件] タブのスクリーンショット。":::

1. <a name="secret"></a> **[ここをクリックして Cloud Shell を開きます]** を選択して [Cloud Shell] タブを開きます。メッセージが表示されたらサインインし、その後表示されたコマンドを実行します。

    実行したスクリプトによって Azure AD (AAD) 関数アプリが作成され、ハニートークンがデプロイされます。    次に例を示します。

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

    スクリプトの出力に AAD アプリの ID とシークレットが含まれています。 次に例を示します。

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. Microsoft Azure Sentinelに戻り、 **[前提条件]** タブの下部にある AAD アプリの ID とシークレットをそれぞれ該当するフィールドに入力します。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="関数アプリのクライアント ID とシークレットの値が追加されたスクリーンショット。":::

1. 手順 4 の **[ここをクリックして関数アプリの設定を続行する]** を選択します。 Azure AD アプリケーションの設定で、新しいブラウザー タブが開きます。

    メッセージが表示されたらサインインし、その後 **[`<your directory name>` に管理者の同意を与えます]** を選択して続行します。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="[自分のディレクトリに管理者の同意を与えます] ボタンのスクリーンショット。":::

    詳細については、「[アプリの登録で管理者の同意を付与する](../active-directory/manage-apps/grant-admin-consent.md)」を参照してください。

1. もう一度 Microsoft Azure Sentinel に戻り、 **[ブック]** 、 **[分析]** 、 **[ウォッチリスト]** 、 **[プレイブック]** の各タブで、作成されるセキュリティ コンテンツを確認し、必要に応じて名前を変更します。

    > [!NOTE]
    > この記事の他の手順では、**HoneyTokensIncidents** および **SOCHTManagement** ブックを参照しています。 これらのブックの名前を変更する場合は、各自の参照用に新しいブック名をメモし、必要に応じて既定の名前の代わりに使用してください。

1. **[Azure Functions]** タブで、次の値を定義します。

    **Key Vault の構成**: 以下のフィールドで、AAD アプリのシークレットを格納するキー コンテナーの値を定義します。 これらのフィールドでは、ハニートークンをデプロイするキー コンテナーを "*定義しません*"。

    |フィールド  |説明  |
    |---------|---------|
    | **サービス プラン**     |   関数アプリで **[Premium]** または **[従量課金]** プランのどちらを使用するかを選択します。 詳細については、「[Azure Functions の従量課金プラン ホスティング](../azure-functions/consumption-plan.md)」および「[Azure Functions の Premium プラン](../azure-functions/functions-premium-plan.md)」を参照してください。      |
    | **新しいキー コンテナーを作成しますか**     |    アプリのシークレット用に新しいキー コンテナーを作成する場合は **[新規]** を選択し、既存のキー コンテナーを使用する場合は **[既存]** を選択します。   |
    | **キー コンテナー名**     | 新しいキー コンテナーの作成を選択した場合にのみ表示されます。 <br><br>アプリのシークレットを格納するために使用するキー コンテナーの名前を入力します。 この名前はグローバルに一意である必要があります。     |
    | **キー コンテナーのリソース グループ**     |新しいキー コンテナーの作成を選択した場合にのみ表示されます。 <br><br> アプリケーション キーのキー コンテナーを格納するリソース グループの名前を選択します。      |
    | **既存のキー コンテナー** | 既存のキー コンテナーの使用を選択した場合にのみ表示されます。 使用するキー コンテナーを選択します。 |
    | **キー コンテナーのシークレット名**     |  AAD アプリのシークレットを保存するシークレットの名前を入力します。 この AAP アプリは[ステップ 3](#secret) で作成しました。 |

    **ハニートークンの構成**: 以下のフィールドで、ハニートークンで使用するキーとシークレットに使用される設定を定義します。 組織の名前付け要件と調和する名前付け規則を使用します。これにより、攻撃者はその違いを知ることができなくなります。

    |フィールド  |説明  |
    |---------|---------|
    |**キーのキーワード**     |  おとりのハニートークン名で使用する値のコンマ区切りリストを入力します。  たとえば、「 `key,prod,dev` 」のように入力します。  値は英数字のみにする必要があります。   |
    |**シークレット**     |   おとりのハニートークン シークレットで使用する値のコンマ区切りリストを入力します。  たとえば、「 `secret,secretProd,secretDev` 」のように入力します。 値は英数字のみにする必要があります。    |
    |**追加のハニートークンの確率**     |  `0` から `1` までの値 (`0.6` など) を入力します。 この値によって、キー コンテナーに複数のハニートークンが追加される確率を定義します。       |
    |     |         |

1. **[次へ: 確認と作成]** を選択して、ソリューションのインストールを完了します。

    ソリューションがインストールされると、以下の項目が表示されます。

    - **SOCHTManagement** ブックへのリンク。 この名前は、この手順で前述した **[ブック]** タブで変更した可能性があります。

    - カスタム ARM テンプレートの URL。 この ARM テンプレートを使用すると、**SOCHTManagement** ブックを配布する (Microsoft Defender for Cloud のカスタム推奨事項に結び付けられた) Azure Policy イニシアティブを組織内のキー コンテナー所有者にデプロイできます。

1. **[デプロイ後の手順]** タブでは、デプロイ出力に表示される情報を使用して、ハニートークンをキー コンテナーにデプロイすることを推奨する Microsoft Defender for Cloud のカスタム推奨事項を組織内のすべてのキー コンテナー所有者に配布できることが示されます。

    インストール出力に表示されているカスタム [ARM テンプレートの URL](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy) を使用して、リンクされたテンプレートの **[カスタム デプロイ]** ページを開きます。

    詳細については、[SOCHTManagement ブックの配布](#distribute-the-sochtmanagement-workbook)に関する記事をご覧ください。

## <a name="deploy-your-honeytokens"></a>ハニートークンをデプロイする

**Microsoft Azure Sentinel Deception (ハニートークン)** ソリューションをインストールしたら、**SOCHTManagement** ブックの手順を使用してキー コンテナーへのハニートークンのデプロイを開始できます。

**SOCHTManagement** ブックを組織内のキー コンテナー所有者と共有して、キー コンテナーに独自のハニートークンを作成できるようにすることをお勧めします。 このブックの名前は、[ソリューションをインストール](#install-the-solution)するときに変更した可能性があります。 共有する場合は、読み取りアクセス許可のみを付与するようにしてください。

**キー コンテナーにハニートークンをデプロイする**:

1. Microsoft Azure Sentinel で、 **[ブック] > [マイ ブック]** にアクセスし、**SOCHTManagement** ブックを開きます。 この名前は、ソリューションをデプロイするときに変更した可能性があります。

1. **[保存されたブックの表示]**  >  **[信頼済みとして追加]** を選択します。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="SOCHTManagement ブックの [信頼済みとして追加] ボタンのスクリーンショット。":::

    ハニートークンをデプロイできるようにするため、キー コンテナーにインフラストラクチャがデプロイされます。

1. ブックの **[キー コンテナー]** タブで、サブスクリプションを展開して、ハニートークンをデプロイできるキー コンテナーと、ハニートークンが既にデプロイされているキー コンテナーを表示します。

    **[SOC によって監視されています]** 列で、緑色のチェックマーク :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false"::: はキー コンテナーにハニートークンが既に存在することを示します。 赤い x マーク :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false"::: は、キー コンテナーにハニートークンがまだ存在しないことを示します。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="デプロイされたハニートークンを示す SOCHTManagement ブックのスクリーンショット。":::

1. ブック ページを下にスクロールし、 **[アクションを実行する]** セクションの指示とリンクを使用してハニートークンをすべてのキー コンテナーに大規模にデプロイするか、または一度に 1 つずつ手動でデプロイします。

    # <a name="deploy-at-scale"></a>[大規模にデプロイする](#tab/deploy-at-scale)

    **ハニートークンを大規模にデプロイするには**:

    1. **[ユーザーを有効にする]** リンクを選択して、指定されたユーザー ID にハニートークンを作成する権限を付与するキー コンテナー アクセス ポリシーをデプロイする ARM テンプレートをデプロイします。

        メッセージが表示されたらサインインし、ARM テンプレートのデプロイに関する **[プロジェクトの詳細]** および **[インスタンスの詳細]** 領域の値を入力します。 **[テナント ID]** と **[ユーザー オブジェクト ID]** は、ユーザーの Azure Active Directory ホーム ページで確認します。

        完了したら、ARM テンプレートをデプロイするために **[確認と作成]** を選択します。 次に例を示します。

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="[カスタム デプロイ] ページのスクリーンショット。":::

        設定が検証され、検証に成功すると、 **[検証に成功しました]** という確認メッセージが表示されます。

        ページの下部にある **[作成]** を選択して ARM テンプレートをデプロイし、デプロイの成功を確認するページが表示されるまで待ちます。

    1. Microsoft Azure Sentinel に戻り、**SOCHTManagement** ブック > **[アクションを実行する]**  >  **[大規模にデプロイする]** 領域で、 **[クリックしてデプロイする]** リンクを選択して、選択したサブスクリプションでアクセスできるすべてのキー コンテナーにハニートークンを追加します。

        完了すると、新しいタブの表にハニートークンのデプロイ結果が表示されます。

    1. 必ず **[ユーザーを無効にする]** リンクを選択して、前に作成したアクセス ポリシーを削除します。 メッセージが表示されたらもう一度サインインし、カスタム ARM デプロイの値を入力してから、ARM テンプレートをデプロイします。 この手順では、キーとシークレットを作成するユーザー権限を削除するキー コンテナー アクセス ポリシーをデプロイします。

    # <a name="deploy-a-single-honeytoken"></a>[1 つのハニートークンをデプロイする](#tab/deploy-a-single-honeytoken)

    **1 つのハニートークンを手動でデプロイするには**:

    1. ページの上部にある表で、ハニートークンをデプロイするキー コンテナーを選択します。 ページの下部に **[特定のキー コンテナーにデプロイする:]** セクションが表示されます。

    1. 下にスクロールして、 **[ハニートークンの種類]** ドロップダウンで、キーまたはシークレットのどちらを作成するかを選択します。 **[ロール名]** フィールドで、ロールのわかりやすい名前を入力します。 次に例を示します。

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="[特定のキー コンテナーにデプロイする] 領域のスクリーンショット。":::

    1. **[操作]** 表で、 **[ハニートークンをデプロイする]** セクションを展開し、各タスク名を選択して必要な手順を実行します。 メッセージが表示されたらサインインします。

        - **[クリックしてキー コンテナーが監査されていることを検証します]** を選択します。 Azure Key Vault で、Log Analytics に監査イベントを送信するようにキー コンテナーの診断設定が設定されていることを確認します。
        - **[キー コンテナーのポリシーにユーザーが含まれていない場合は、ユーザーを有効にします]** を選択します。 Azure Key Vault で、ユーザーが必要な場所にハニートークンをデプロイするためのアクセス権を持っていることを確認します。 **[保存]** を選択してすべての変更を保存します。
        - **[クリックしてキー コンテナーにハニートークンを追加します]** を選択して、Azure Key Vault を開きます。 新しいシークレットと同じように、新しいハニートークンを構成されたキー コンテナーに追加します。
        - **[クリックして SOC に監視を追加します]** を選択します。 成功すると、新しいタブに [`Honey-token was successfully added to monitored list`] という確認メッセージが表示されます。

        詳しくは、[Azure Key Vault のドキュメント](../key-vault/secrets/about-secrets.md)をご覧ください。

    > [!NOTE]
    > **[必要に応じて、キー コンテナーのポリシーでユーザーを無効に戻します]** リンクを選択して、ハニートークンを作成する権限を付与するために作成されたアクセス ポリシーを削除します。
    >

    # <a name="remove-a-honeytoken"></a>[ハニートークンを削除する](#tab/remove-a-honeytoken)

    **特定のハニートークンを削除するには**:

    1. ページの上部にある表で、ハニートークンを削除するキー コンテナーを選択します。 ページの下部に **[特定のキー コンテナーにデプロイする:]** セクションが表示されます。

    1. **[操作]** 表で、 **[ハニートークンを削除する]** セクションを展開し、各タスク名を選択して必要な手順を実行します。 メッセージが表示されたらサインインします。

        - **[クリックしてキー コンテナーからハニー トークンを削除します]** を選択して、Azure Key Vault のハニートークンを削除できるページに移動します。
        - **[メールを送信して SOC を更新します]** を選択します。 既定のメール クライアントで、SOC に対して選択したキー コンテナーのハニートークンの監視を削除することを推奨するメールが開きます。

    > [!TIP]
    > 削除するハニートークンの情報を SOC に明確に伝えることをお勧めします。
    >

    ---

データが設定され、アクセス許可が更新されるまでに、数分かかる場合があります。 ページを更新して、キー コンテナーのデプロイの更新内容を表示します。

## <a name="test-the-solution-functionality"></a>ソリューションの機能をテストする

**ハニートークンへのアクセスが試行された場合にアラートを受け取ることをテストするには**:

1. Microsoft Azure Sentinel の **[ウォッチリスト]** ページで、 **[マイ ウォッチリスト]** タブを選択してから、**HoneyTokens** ウォッチリストを選択します。

    **[Log Analytics で表示]** を選択して、検出された現在のハニートークン値の一覧を表示します。 **[ログ]** ページで、ウォッチリストの項目がクエリのために自動的に抽出されます。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Log Analytics のハニートークン ウォッチリスト値のスクリーンショット。" lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::

    詳細については、「[Microsoft Azure Sentinel ウォッチリストを使用する](watchlists.md)」を参照してください。

1. Log Analytics の一覧から、テストするハニートークン値を選択します。

    次に、Azure Key Vault に移動して、公開キーをダウンロードするか、選択したハニートークンのシークレットを表示します。

    たとえば、ハニートークンを選択してから、 **[公開キーのダウンロード]** を選択します。 このアクションにより、Microsoft Azure Sentinel にアラートをトリガーする `KeyGet` または `SecretGet` ログが作成されます。

    詳細については、[Key Vault](../key-vault/index.yml) のドキュメントを参照してください。

1. Microsoft Azure Sentinel に戻り、 **[インシデント]** ページに移動します。 場合によっては 5 分ほど待つ必要がありますが、「**HoneyTokens: KeyVault HoneyTokens key accessed**」のような名前の新しいインシデントが表示されます。

    インシデントを選択すると、実行されたキー操作、ハニートークン キーにアクセスしたユーザー、侵害されたキー コンテナーの名前などの詳細情報が表示されます。

    > [!TIP]
    > ハニートークン キーとシークレットを使用したアクセスまたは操作によってインシデントが生成され、Microsoft Azure Sentinel でそのインシデントを調査できます。 実際にハニートークン キーとシークレットを使用する理由は何もないため、ワークスペース内の同様のアクティビティは悪意のあるものである可能性があり、調査する必要があります。

1. **HoneyTokensIncident** ブックでハニートークン アクティビティを表示します。 Microsoft Azure Sentinel の **[ブック]** ページで、**HoneyTokensIncident** ブックを検索して開きます。

    このブックには、ハニートークンに関連するすべてのインシデント、関連するエンティティ、侵害されたキー コンテナー、実行されたキー操作、アクセスされたハニートークンが表示されます。

    特定のインシデントと操作を選択して、関連するすべてのアクティビティをさらに調査します。

## <a name="distribute-the-sochtmanagement-workbook"></a>SOCHTManagement ブックを配布する

組織内で最適な検出機能を確保するため、できるだけ多くのキー コンテナーにハニートークンをデプロイすることをお勧めします。

ただし、多くの SOC チームはキー コンテナーにアクセスできません。 このギャップを埋めるには、テナント内のすべてのキー コンテナー所有者に **SOCHTManagement** ブックを配布して、SOC チームが独自のハニートークンをデプロイできるようにします。 このブックの名前は、[ソリューションをインストール](#install-the-solution)するときに変更した可能性があります。

ブックへの直接リンクは、いつでも共有できます。 別の方法として、この手順では、ARM テンプレートを使用して、**SOCHTManagement** ブックを配布する (Microsoft Defender for Cloud のカスタム推奨事項に結び付けられた) Azure Policy イニシアティブを組織内のキー コンテナー所有者にデプロイする方法について説明します。

> [!NOTE]
> ブックを配布するときは、必ず読み取りアクセス権のみを付与してください。
>

**Azure Policy イニシアティブを使用して SOCHTManagement ブックを配布するには**

1. 次の表から、ARM テンプレートをデプロイする方法に応じて **[Azure にデプロイ]** ボタンを選択し、 **[カスタム デプロイ]** ページに ARM テンプレートを表示します。  GitHub のリンクを使用して、ARM テンプレートに含まれる内容の詳細の表示、または環境に合わせた ARM テンプレートのカスタマイズを行います。

    **[Azure にデプロイ]** ボタンでは、[ソリューションのインストール](#install-the-solution)後に **[出力]** タブに表示されるのと同じ URL が使用されます。

    | デプロイ オプション | 説明 | Azure にデプロイ | GitHub のリンク |
    |-------------------|-------------|-------------|-----------------|
    | 管理グループ | エンタープライズ全体のデプロイに推奨| [![DTA-Button-MG]][DTA-MG]  |[GitHub の例][GitHub-MG] |
    | サブスクリプション | 1 つのサブスクリプションでのテストに推奨 | [![DTA-Button-Sub]][DTA-Sub]  | [GitHub の例][GitHub-Sub] |

    メッセージが表示されたらサインインします。

1. ARM テンプレートの **[Deception ソリューションのポリシーのデプロイ]**  >  **[基本]** タブで、管理グループの値と地域を選択します。 その後、 **[次へ: デプロイのターゲット >]** をクリックして続行します。

1. **[デプロイのターゲット]** タブで、管理グループをもう一度選択してから **[次へ: 管理ブック >]** を選択します。

1. **[管理ブック]** タブで、**SOCHTManagement** ブックへのリンクを貼り付けます。

    ブックのリンクは、Microsoft Azure Sentinel の **SOCHTManagement** ブックで確認できますが、ソリューションのデプロイの **[出力]** タブにも表示されていました。

    たとえば、ブック内のリンクを確認するには、 **[ブック]**  >  **[マイ ブック]**  >  **[SOCHTManagement]** の順に選択してから、ツールバーの **[リンクのコピー]** を選択します。

1. ブックのリンクを入力したら、 **[次へ: 確認と作成 >]** を選択して続行します。 検証が成功したことを示す確認メッセージが表示されるのを待ってから、 **[作成]** を選択します。

1. デプロイが完了すると、新しい **HoneyTokens** イニシアティブと、**KeyVault HoneyTokens** および **KVReviewTag** という 2 つの新しいポリシーがデプロイに含まれていることがわかります。 次に例を示します。

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="正常にデプロイされた ARM テンプレート ポリシーのスクリーンショット。" lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. Azure **Policy** で、必要なスコープを指定して新しい **KVReviewTag** ポリシーを割り当てます。 この割り当てにより、選択したスコープ内のすべてのキー コンテナーに **KVReview** タグと **ReviewNeeded** の値が追加されます。

    1. Azure Policy で、左側の **[作成]** の下にある **[定義]** を選択します。 **KVReviewTag** ポリシーの行を見つけ、右側の [オプション] メニューを選択します。

    1. **[アクティビティ ログの診断設定を Log Analytics ワークスペースにデプロイします]** ページで、環境の診断設定をデプロイするために必要な値を入力します。

        **[修復]** タブで、必ず **[修復タスクを作成する]** オプションを選択して、既存のキー コンテナーにタグを適用します。

    詳細については、[Azure Policy のドキュメント](../governance/policy/assign-policy-portal.md)を参照してください。

1. **Microsoft Defender for Cloud** で、選択したスコープ内のすべてのキー コンテナーに監査の推奨事項を追加します。

    1. **[規制コンプライアンス]** > [コンプライアンス ポリシーの管理] を選択してから、スコープを選択します。

    1. 選択したスコープの詳細ページで、下にスクロールし、 **[カスタム イニシアティブ]** セクションで **[カスタム イニシアティブの追加]** を選択します。

    1. **HoneyTokens** イニシアティブの行で、 **[追加]** を選択します。

**SOCHTManagement** ブックへのリンクを含む監査の推奨事項が、選択したスコープ内のすべてのキー コンテナーに追加されます。 このブックの名前は、[ソリューションをインストール](#install-the-solution)するときに変更した可能性があります。

詳細については、[Microsoft Defender for Cloud のドキュメント](/azure/security-center/security-center-recommendations)を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [Microsoft Azure Sentinel ソリューションについて](sentinel-solutions.md)
- [Microsoft Azure Sentinel ソリューションを検出してデプロイする](sentinel-solutions-deploy.md)
- [Microsoft Azure Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)
- [難しい設定なしで脅威を検出する](detect-threats-built-in.md)
- [一般的に使用される Microsoft Azure Sentinel ブック](top-workbooks.md)

<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (以下の外部画像を参照)
[//]: # (*******************************)

[DTA-Button-MG]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "管理グループのスコープに ASC ポリシーをデプロイします。"
[DTA-Button-Sub]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "サブスクリプションのスコープに ASC ポリシーをデプロイします。"

[//]: # (**************************)
[//]: # (下の外部リンクラベル)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
