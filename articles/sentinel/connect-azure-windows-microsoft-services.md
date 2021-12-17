---
title: Microsoft Sentinel を Azure、Windows、および Microsoft サービスに接続する
description: Microsoft Sentinel を Azure と Microsoft 365 クラウド サービス、および Windows Server イベント ログに接続する方法について学習します。
author: yelevin
manager: rkarlin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4843f3bf4f5ccaf22446bb98a5e44fa6179d9946
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724446"
---
# <a name="connect-microsoft-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Microsoft Azure Sentinel を Azure、Windows、Microsoft、および Amazon サービスに接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel では Azure 基盤を使用して、多くの Azure と Microsoft 365 サービス、アマゾン ウェブ サービス、およびさまざまな Windows Server サービスからのデータ インジェスト用の組み込みのサービス間サポートを提供します。 これらの接続を行ういくつかの異なる方法があります。この記事では、これらの接続を行う方法について説明します。

この記事では、次の種類のコネクタについて説明します。

- **API ベース** の接続
- **診断設定** の接続。そのうちの一部は Azure Policy によって管理されます
- **Log Analytics エージェント** ベースの接続

この記事では、コネクタのグループに共通する情報を示します。 各コネクタに固有の情報については、付随の[データ コネクタのリファレンス](data-connectors-reference.md) ページを参照してください。これには、データ ストレージのライセンス前提条件や Log Analytics テーブルなどがあります。

次の統合は、より固有なものでもあり、より一般的なものでもあります。これらは、それぞれの記事で個別に扱われます。

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Microsoft Defender for Cloud](connect-defender-for-cloud.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows セキュリティ イベント](connect-windows-security-events.md)
- [アマゾン ウェブ サービス (AWS) CloudTrail](connect-aws.md)

## <a name="api-based-connections"></a>API ベースの接続

### <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。
- Microsoft Sentinel ワークスペースのテナントに対するグローバル管理者またはセキュリティ管理者ロールが必要です。

### <a name="instructions"></a>手順

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーからご利用のサービスを選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. **[接続]** を選択して、サービスから Microsoft Sentinel へのイベントやアラートのストリーミングを開始します。

1. コネクタ ページに **[Create incidents - recommended!]\(インシデントの作成 - 推奨\)** というタイトルのセクションがある状態で、アラートからインシデントを自動的に作成する場合は **[有効]** を選択します。

[データ コネクタのリファレンス](data-connectors-reference.md) ページで、サービスのコネクタのセクションに表示されるテーブル名を使用することで、各サービスのデータを検索してクエリを実行できます。

## <a name="diagnostic-settings-based-connections"></a>診断設定ベースの接続

この種類の一部のコネクタの構成は、Azure Policy によって管理されます。 手順については、下の **[Azure Policy]** タブを選択してください。 この種類の他のコネクタの場合は、 **[スタンドアロン]** タブを選択します。

# <a name="standalone"></a>[スタンドアロン](#tab/SA)

### <a name="prerequisites"></a>前提条件

Microsoft Sentinel にデータを取り込むには:

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

### <a name="instructions"></a>手順

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーからリソースの種類を選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、リンクを選択してリソース構成ページを開きます。

    目的の種類のリソースの一覧が表示されたら、ログを取り込むリソースのリンクを選択します。

1. リソースのナビゲーション メニューから **[診断設定]** を選択します。

1. 一覧の下部にある **[+ 診断設定を追加する]** を選択します。

1. **[診断設定]** 画面で、 **[診断設定の名前]** フィールドに名前を入力します。

    **[Log Analytics への送信]** チェック ボックスにマークを付けます。 2 つの新しいフィールドが下に表示されます。 関連する **サブスクリプション** と **Log Analytics ワークスペース** (Microsoft Sentinel が存在する場所) を選択します。

1. 収集するログとメトリックの種類のチェック ボックスをオンにします。 [データ コネクタのリファレンス](data-connectors-reference.md) ページで、リソースのコネクタのセクションにある各リソースの種類に推奨される選択肢を確認してください。

1. ページの最上部で **[保存]** を選択します。

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>前提条件

Microsoft Sentinel にデータを取り込むには:

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Policy を使用してリソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが必要です。

### <a name="instructions"></a>Instructions

この種類のコネクタでは Azure Policy を使用して、スコープとして定義された単一の種類のリソースのコレクションに、単一の診断設定構成を適用します。 特定のリソースの種類から取り込まれたログの種類は、そのリソースのコネクタ ページの左側にある **[データ型]** で確認できます。

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーからリソースの種類を選び、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、表示されている任意の展開コントロールを展開し、 **[Azure Policy の割り当てウィザードの起動]** ボタンを選択します。

    ポリシーの割り当てウィザードが開き、事前に設定されたポリシー名で新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンを選択し、ご利用のサブスクリプション (および必要に応じてリソース グループ) を選びます。 説明を追加することもできます。

    1. **[パラメーター]** タブで、次の操作を行います。
       - **[Only show parameters that require input]\(入力が必要なパラメーターのみを表示する\)** チェック ボックスをオフにします。
       - **[効果]** と **[設定名]** のフィールドが表示されている場合は、そのままにしておきます。
       - **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Microsoft Sentinel ワークスペースを選択します。
       - 残りのドロップダウン フィールドは、使用可能な診断ログの種類を表します。 取り込み対象のすべてのログの種類を "True" とマークされたままにします。

    1. ポリシーは、今後追加されるリソースに適用されます。 ポリシーを既存のリソースにも適用するには、 **[修復]** タブを選択し、 **[修復タスクを作成する]** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

---

> [!NOTE]
>
> この種類のデータ コネクタを使用すると、過去 14 日間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑色) と表示されます。 データ インジェストがないまま 14 日が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

[データ コネクタのリファレンス](data-connectors-reference.md) ページで、リソースのコネクタのセクションに表示されるテーブル名を使用することで、各リソースの種類のデータを検索してクエリを実行できます。

## <a name="windows-agent-based-connections"></a>Windows エージェントベースの接続

# <a name="azure-monitor-agent"></a>[Azure Monitor エージェント](#tab/AMA)

> [!IMPORTANT]
>
> - Azure Monitor エージェント (AMA) に基づくコネクタの一部は現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[Azure Monitor エージェント](../azure-monitor/agents/azure-monitor-agent-overview.md)では、**データ収集ルール (DCR)** を使用して、各エージェントから収集するデータが定義されます。 データ収集ルールには、次の 2 つの明確な利点があります。

- **大規模に収集設定を管理** しながら、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースから独立し、仮想マシンからも独立しているため、一度定義すると、コンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)」をご覧ください。

- **カスタム フィルターを作成** して、取り込むイベントを正確に選択できます。 Azure Monitor エージェントは、これらのルールを使用して *ソースの* データをフィルター処理し、必要なイベントだけを取り込み、その他のすべてを残します。 これにより、データ インジェストのコストを削減できます。

データ収集ルールを作成する方法については、以下を参照してください。

### <a name="prerequisites"></a>前提条件

- Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure 仮想マシン以外からイベントを収集するには、Azure Monitor エージェントベースのコネクタを有効にする前に、システムに [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) をインストールし、有効にしておく必要があります。

  これには次のものが含まれます

  - 物理マシンにインストールされている Windows サーバー
  - オンプレミスの仮想マシンにインストールされている Windows サーバー
  - Azure 以外のクラウドの仮想マシンにインストールされている Windows サーバー

### <a name="instructions"></a>Instructions

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。 一覧からコネクタを選び、詳細ペインの **[コネクタ ページを開く]** を選びます。 その後、 **[Instructions]\(手順\)** タブで画面の指示に従い、このセクションの残りの作業を行います。

1. コネクタ ページの **[前提条件]** セクションに記載された適切なアクセス許可があることを確認します。

1. **[構成]** の下の **[+ データ収集ルールの追加]** を選択します。 右に **[データ収集ルールの作成]** ウィザードが表示されます。

1. **[基本]** の下の **[規則名]** を入力し、データ収集ルール (DCR) を作成する **[サブスクリプション]** および **[リソース グループ]** を指定します。 監視対象のマシンとその関連付けが同じテナント内にある限り、同じリソース グループまたはサブスクリプションである必要は *ありません*。

1. **[リソース]** タブで **[+ リソースの追加]** を選択し、データ収集ルールが適用されるマシンを追加します。 **[スコープの選択]** ダイアログが開き、使用可能なサブスクリプションの一覧が表示されます。 サブスクリプションを展開してそのリソース グループを表示し、リソース グループを展開して使用可能なマシンを表示します。 一覧に、Azure 仮想マシンと Azure Arc 対応サーバーが表示されます。 サブスクリプションまたはリソース グループのチェック ボックスをオンにして、含まれるすべてのマシンを選択するか、個々のマシンを選択できます。 すべてのマシンを選択したら、 **[適用]** を選択します。 このプロセスの最後で、選択した未インストールのマシンに Azure Monitor エージェントがインストールされます。

1. **[収集]** タブで、収集するイベントを選びます。**[すべてのイベント]** または **[カスタム]** を選んで他のログを指定するか、[XPath クエリ](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)を使用してイベントをフィルター処理します。 収集するイベントの特定の XML 条件を評価する式をボックスに入力し、 **[追加]** を選択します。 1 つのボックスには最大 20 個の式を、ルールには最大 100 個のボックスを入力できます。

    [データ収集ルール](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr)の詳細については、Azure Monitoring のドキュメントを参照してください。

    > [!NOTE]
    >
    > - Windows セキュリティ イベント コネクタには、収集できる他の 2 つの [**事前構築済みイベント セット**](windows-security-event-id-reference.md) (**Common** と **Minimal**) が用意されています。
    >
    > - Azure Monitor エージェントは、 **[XPath バージョン 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) のみ** の XPath クエリをサポートしています。

1. 必要なすべてのフィルター式を追加した後、 **[次へ: 確認および作成]** を選択します。

1. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。 

コネクタ ページの **[構成]** に、すべてのデータ収集ルール (API を使用して作成されたルールを含む) が表示されます。 そこから既存のルールを編集または削除することができます。

> [!TIP]
> XPath クエリの有効性をテストするには、 *-FilterXPath* パラメーターを指定した PowerShell コマンドレット **Get-WinEvent** を使用します。 次のスクリプトは、一例を示しています。
>
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - イベントが返されたら、クエリは有効です。
> - [No events were found that match the specified selection criteria]\(指定した選択条件に一致するイベントは見つかりませんでした\) というメッセージが表示された場合は、クエリはおそらく有効ですが、一致するイベントがローカル コンピューターにありません。
> - [The specified query is invalid]\(指定したクエリは無効です\) というメッセージが表示された場合は、クエリ構文が無効です。

### <a name="create-data-collection-rules-using-the-api"></a>API を使用してデータ収集ルールを作成する

また、API ([スキーマ](/rest/api/monitor/data-collection-rules)を参照) を使用してデータ収集ルールを作成することもできます。これは、多くのルールを作成する場合 (たとえば、MSSP の場合) の作業が容易になる可能性があります。 ルールを作成するためのテンプレートとして使用できる ([AMA コネクタ経由の Windows セキュリティ イベント](data-connectors-reference.md#windows-security-events-via-ama)用) の例を次に示します。

**要求 URL とヘッダー**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```

**要求本文**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```

Azure Monitor のドキュメントのこちらの[データ収集ルールの完全な説明](../azure-monitor/agents/data-collection-rule-overview.md)を参照してください。

# <a name="log-analytics-agent-legacy"></a>[Log Analytics エージェント (レガシ)](#tab/LAA)

### <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する読み取りと書き込みのアクセス許可と、ログの収集元となるマシンを含むワークスペースが必要です。
- Microsoft Sentinel ロールに加えて、それらのワークスペースの SecurityInsights (Microsoft Sentinel) ソリューションに対する **Log Analytics 共同作成者** ロールが必要です。

### <a name="instructions"></a>手順

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. ご利用のサービス ( **[DNS]** または **[Windows ファイアウォール]** ) を選んでから、 **[コネクタ ページを開く]** を選択します。

1. ログを生成するデバイスにエージェントをインストールし、オンボードします。

    | マシンの種類  | Instructions  |
    | --------- | --------- |
    | **Azure Windows VM の場合** | 1. **[エージェントのインストール先を選択してください]** で、 **[Azure Windows 仮想マシンにエージェントをインストールする]** を展開します。 <br><br>2. **[Download & install agent for Azure Windows Virtual machines >]\(Azure Windows 仮想マシン用のエージェントをダウンロードしてインストールする >\)** リンクを選択します。 <br><br>3. **[仮想マシン]** ブレードで、エージェントのインストール先となる仮想マシンを選んでから、 **[接続]** を選択します。 接続する各 VM に対してこの手順を繰り返します。 |
    | **その他の Windows マシンの場合** | 1. **[エージェントのインストール先を選択してください]** で、 **[Azure 以外の Windows マシンにエージェントをインストールする]** を展開します <br><br>2. **[Download & install agent for non-Azure Windows machines >]\(Azure 以外の Windows マシン用のエージェントをダウンロードしてインストールする >\)** リンクを選択します。  <br><br>3. **[エージェントの管理]** ブレードの **[Windows サーバー]** タブで、32 ビットまたは 64 ビット システム用の **[Windows エージェントのダウンロード]** リンクを適宜選択します。  <br><br>4. ダウンロードした実行可能ファイルを使用して、目的の Windows システムにエージェントをインストールし、前の手順のダウンロード リンクの下に表示される **[ワークスペース ID とキー]** を使用してその構成を行います。 |
    | | |

> [!NOTE]
>
> 必要なインターネット接続を備えていない Windows システムでも Microsoft Sentinel にイベントをストリーム配信できるようにする場合は、プロキシとして機能する **Log Analytics ゲートウェイ** を別のマシンにインストールします。それには、 **[エージェントの管理]** ページの **[Log Analytics ゲートウェイのダウンロード]** リンクを使用します。  その場合でも、イベントを収集する個々の Windows システムに Log Analytics エージェントをインストールする必要があります。
>
> このシナリオの詳細については、[**Log Analytics ゲートウェイ** のドキュメント](../azure-monitor/agents/gateway.md)を参照してください。

その他のインストール オプションとさらに詳しい情報については、[**Log Analytics エージェント** のドキュメント](../azure-monitor/agents/agent-windows.md)を参照してください。


#### <a name="determine-the-logs-to-send"></a>送信するログを決定する

Windows DNS サーバーと Windowsファイアウォール コネクタに対して、**[ソリューションのインストール]** ボタンを選びます。 レガシ セキュリティ イベント コネクタについては、送信する [**イベント セット**](windows-security-event-id-reference.md)を選択し、**[更新]** を選びます。

[データ コネクタ リファレンス](data-connectors-reference.md) ページの各セクションのテーブル名を使用して、これらのサービスのデータを検索してクエリを実行できます。

---

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure、Microsoft、および Windows サービスと、アマゾン ウェブ サービスを Microsoft Sentinel に接続する方法について学習しました。 
- 一般的な [Microsoft Sentinel データ コネクタ](connect-data-sources.md)について学習する。
- [Microsoft Sentinel データ コネクタを見つける](data-connectors-reference.md)。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
