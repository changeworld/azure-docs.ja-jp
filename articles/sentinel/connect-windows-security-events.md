---
title: Azure Sentinel に Windows セキュリティ イベント データを接続する (タブ付きバージョン) | Microsoft Docs
description: Windows セキュリティ イベント コネクタを使用して、Windows システムから Azure Sentinel ワークスペースにあらゆるセキュリティ イベントをストリーミングする方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a95327518729b854fde1ca6fa22f5b78e65554f0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023423"
---
# <a name="connect-to-windows-servers-to-collect-security-events"></a>セキュリティ イベントを収集するために Windows サーバーに接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Windows セキュリティ イベント コネクタを使用すると、Azure Sentinel ワークスペースに接続されている任意の Windows サーバー (物理または仮想、オンプレミス、または任意のクラウド) からセキュリティ イベントをストリーミングできます。 Windows のセキュリティ イベントをダッシュボードに表示できるほか、それらをカスタム アラートの作成に使用したり、調査に役立てたりしながら、組織のネットワークについての分析情報を手に入れ、セキュリティ運用機能を拡張することができます。

## <a name="connector-options"></a>コネクタのオプション

Windows セキュリティ イベント コネクタは、次のバージョンをサポートしています。

|コネクタのバージョン  |説明  |
|---------|---------|
|**セキュリティ イベント**     |レガシ バージョン。 Log Analytics エージェントに基づいており、Microsoft Monitoring Agent (MMA) または OMS エージェントと呼ばれることもあります。 <br><br>1 秒あたり 1 万イベントに制限されます。 最適なパフォーマンスを得るには、1 秒あたり 8500 イベント以下を維持してください。       |
|**Windows セキュリティ イベント**     |新しい **バージョン (現在一般公開されています! )** は、Azure Monitor エージェント (AMA) に基づいています。   <br><br>特定のコンピューター グループに対するインジェスト前のログのフィルター処理や個別のデータ収集ルールなどの追加機能がサポートされています。      |
|     |         |


> [!NOTE]
> MMA for Linux では、複数のワークスペースにログを送信するマルチホームはサポートされていません。 マルチホームが必要な場合は、**Windows セキュリティ イベント** コネクタを使用することをお勧めします。

> [!TIP]
> 複数のエージェントが必要な場合は、ログの取り込み用に複数のエージェントを実行するように設定されている仮想マシン スケールを使用するか、複数のコンピューターを使用することをお勧めします。 その後、レガシ エージェントと Windows セキュリティ イベント コネクタの AMA バージョン両方をロード バランサーと共に使用して、マシンが過負荷にならないようにし、データの重複を防ぐようにします。
>

この記事では、両方のバージョンのコネクタに関する情報を示します。 下のタブから選択すると、選択したコネクタに関連する情報が表示されます。

# <a name="security-events-via-legacy-agent"></a>[レガシ エージェントを使用したセキュリティ イベント](#tab/LAA)

ストリーム配信するイベントは、次のセットから選ぶことができます。<a name="event-sets"></a>

- **すべてのイベント** - すべての Windows セキュリティ イベントおよび AppLocker イベント。
- **一般** - 監査の目的で使用する標準的なイベント セット。 このセットには、完全なユーザー監査証跡が含まれます。 たとえば、ユーザー サインインとユーザー サインアウトの両方のイベント (イベント ID 4624、4634) が含まれています。 また、セキュリティ グループの変更などの監査アクションや、ドメイン コントローラーの主要な Kerberos 操作、確立されているベスト プラクティスに沿った各種のイベントもあります。

    **一般** のイベント セットには、あまり一般的ではない種類のイベントも一部含まれている場合があります。  完全な監査証跡機能を保ちながらも、より扱いやすいレベルにまでイベントの量を減らすことが、**一般** セットの重要なポイントであるためです。

- **最小** - 潜在的な脅威を示す可能性がある小さいイベント セット。 このセットには、完全な監査証跡は含まれません。 侵害が成功したことを示す可能性があるイベントなど、発生率がきわめて低い重要イベントのみが対象となります。 たとえば、ユーザーの成功したログオンと失敗したログオン (イベント ID 4624、4625) が含まれますが、監査には重要であっても侵害の検出には重要ではない、比較的ボリュームの大きいサインアウト情報 (4634) は含まれません。 このセットのデータ量のほとんどは、サインイン イベントとプロセス作成イベント (イベント ID 4688) から成ります。

- **なし** - セキュリティ イベントまたは AppLocker イベントをストリーミングしない。 (この設定は、コネクタを無効にする目的で使用されます。)

> [!NOTE]
> 単一ワークスペースのコンテキスト内でのセキュリティ イベントの収集は、Azure Security Center と Azure Sentinel のどちらか一方からのみ構成できます。両方からは不可能です。 既に Azure Security Center から Azure Defender アラートを取得しており、なおかつセキュリティ イベントを収集するように設定されているワークスペースに Azure Sentinel をオンボーディングする場合、次の 2 つの選択肢があります。
> - Azure Security Center でのセキュリティ イベントの収集をそのまま使用する。 それらのイベントは、Azure Security Center だけでなく Azure Defender でも照会、分析できるようになります。 ただし、Azure Sentinel でコネクタの接続状態を監視したり、その構成を変更したりすることはできません。 この点が問題となる場合は、2 つ目の選択肢を検討してください。
>
> - Azure Security Center での[セキュリティ イベントの収集を無効](../security-center/security-center-enable-data-collection.md)にした後、Azure Sentinel でセキュリティ イベント コネクタを追加する。 1 つ目の選択肢と同様、イベントの照会と分析は、Azure Sentinel と Azure Defender/ASC の両方で行えますが、コネクタの接続状態を監視したりその構成を変更したりする作業は、Azure Sentinel でしか行えません。

# <a name="windows-security-events-via-azure-monitor-agent-ama"></a>[Windows セキュリティAzure Monitor エージェント (AMA) 経由のイベント](#tab/AMA)

> [!NOTE]
>
> - Azure Monitor エージェント (AMA)  に基づく Windows セキュリティ イベント データ コネクタが一般公開されています。

[Azure Monitor エージェント](../azure-monitor/agents/azure-monitor-agent-overview.md)では、**データ収集ルール (DCR)** を使用して、各エージェントから収集するデータが定義されます。 データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースから独立し、仮想マシンからも独立しているため、一度定義すると、コンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)」をご覧ください。

古いコネクタを使用して取り込むことを選択できる、事前に選択されたイベントのセット (**すべてのイベント**、**最小**、**共通**) に加えて、**データ収集ルール** を使用して、取り込みたいイベントを選択するためのカスタム フィルターを作成することができます。 Azure Monitor エージェントは、これらのルールを使用して *ソースの* データをフィルター処理し、必要なイベントだけを取り込み、その他のすべてを残します。 これにより、データ インジェストのコストを削減できます。

このドキュメントでは、データ収集ルールを作成する方法について説明します。

> [!NOTE]
> - **他のエージェントとの共存**
> 
>   Azure Monitor エージェントは、既存のエージェントと共存させることができるため、評価または移行中にそれらのレガシ コネクタを使用し続けることができます。 ただし、重複データの収集では注意する必要があります。これにより、クエリ結果にずれが生じ、データ インジェストと保持に追加料金が発生する可能性があるためです。
> 
> - **Azure 以外のマシンからセキュリティ イベントを収集する**
> 
>   Azure の仮想マシンではないシステムからセキュリティ イベントを収集するには、Azure Monitor エージェントベースのコネクタを有効にする "*前*" に、システムに [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) をインストールし、有効にしておく必要があります。
>   
>   これには次のものが含まれます
>   
>    - 物理マシンにインストールされている Windows サーバー
>    - オンプレミスの仮想マシンにインストールされている Windows サーバー
>    - Azure 以外のクラウドの仮想マシンにインストールされている Windows サーバー

---

## <a name="set-up-the-windows-security-events-connector"></a>Windows セキュリティ イベントのコネクタを設定する

Azure Sentinel で Windows セキュリティ イベントを収集するには、デプロイするコネクタのバージョンに従って、適切なタブの指示に従います。

# <a name="security-events-via-legacy-agent"></a>[レガシ エージェントを使用したセキュリティ イベント](#tab/LAA)

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタの一覧で **[レガシ イベント 経由のセキュリティ イベント]** を選択し、詳細ペインの **[コネクタ ページを開く]** を選択します。 その後、 **[Instructions]\(手順\)** タブで画面の指示に従い、このセクションの残りの作業を行います。

1. コネクタ ページの **[前提条件]** セクションに記載された適切なアクセス許可があることを確認します。

1. Azure Sentinel にストリーム配信するセキュリティ イベントの発生元となるマシンに、[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md) (旧称 Microsoft Monitoring Agent または MMA) をダウンロードしてインストールします。

    Azure Virtual Machines の場合:
    
    1. **[Install agent on Azure Windows Virtual Machine]\(Azure 仮想マシンにエージェントをインストールする\)** を選択し、下に表示されるリンクを選択します。
    1. 接続する各仮想マシンについて、その名前を右側に表示されるリストで選択し、**[接続]** を選択します。

    Azure 以外の Windows マシンの場合 (物理マシン、オンプレミスの仮想マシン、別のクラウドの仮想マシン):

    1. **[Azure 以外の Windows マシンにエージェントをインストールする]** を選択し、下に表示されるリンクを選択します。
    1. 右側の **[Windows コンピューター]** に表示される適切なダウンロード リンクを選択します。
    1. ダウンロードした実行可能ファイルを使用して、目的の Windows システムにエージェントをインストールし、前述のダウンロード リンクの下に表示される **[ワークスペース ID とキー]** を使用してその構成を行います。

    > [!NOTE]
    >
    > 必要なインターネット接続を備えていない Windows システムでも、Azure Sentinel にイベントをストリーム配信できるようにしたい場合は、プロキシとして機能する **OMS ゲートウェイ** を、右下のリンクから個々のマシンにダウンロードしてインストールしてください。  その場合でも、イベントを収集する個々の Windows システムに Log Analytics エージェントをインストールする必要があります。
    >
    > このシナリオの詳細については、[**Log Analytics ゲートウェイ** のドキュメント](../azure-monitor/agents/gateway.md)を参照してください。

    その他のインストール オプションとさらに詳しい情報については、[**Log Analytics エージェント** のドキュメント](../azure-monitor/agents/agent-windows.md)を参照してください。

1. ストリーム配信するイベント セット (すべて、一般、最小) を選択します。 最小および共通のイベント セットに [含まれるイベント ID の一覧](#event-id-reference)を参照してください。

1. **[更新]** を選択します。

1. Windows セキュリティ イベントに Log Analytics 内の関連スキーマを使用するには、クエリ ウィンドウで「`SecurityEvent`」と入力します。

# <a name="windows-security-events-via-azure-monitor-agent-ama"></a>[Azure Monitor エージェント (AMA) 経由の Windows セキュリティ イベント](#tab/AMA)

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタの一覧で **[AMA 経由の Windows セキュリティ イベント]** を選択し、右下にある **[コネクタ ページを開く]** ボタンを選択します。 その後、 **[Instructions]\(手順\)** タブで画面の指示に従い、このセクションの残りの作業を行います。

1. コネクタ ページの **[前提条件]** セクションに記載された適切なアクセス許可があることを確認します。

    1. ワークスペースと、Windows セキュリティ イベントを取り込むすべてのデータソースに対する読み取りと書き込みのアクセス許可を持っている必要があります。

    1. Azure VM ではない Windows マシンからイベントを収集するには、マシン (物理または仮想) に Azure Arc がインストールされ、有効になっている必要があります。 [詳細については、こちらを参照してください](../azure-monitor/agents/azure-monitor-agent-install.md)。

1. **[構成]** の下の **[+ データ収集ルールの追加]** を選択します。 右に **[データ収集ルールの作成]** ウィザードが表示されます。

1. **[基本]** の下の **[規則名]** を入力し、データ収集ルール (DCR) を作成する **[サブスクリプション]** および **[リソース グループ]** を指定します。 監視対象のマシンとその関連付けが同じテナント内にある限り、同じリソース グループまたはサブスクリプションである必要は *ありません*。

1. **[リソース]** タブで **[+ リソースの追加]** を選択し、データ収集ルールが適用されるマシンを追加します。 **[スコープの選択]** ダイアログが開き、使用可能なサブスクリプションの一覧が表示されます。 サブスクリプションを展開してそのリソース グループを表示し、リソース グループを展開して使用可能なマシンを表示します。 一覧に、Azure 仮想マシンと Azure Arc 対応サーバーが表示されます。 サブスクリプションまたはリソース グループのチェック ボックスをオンにして、含まれるすべてのマシンを選択するか、個々のマシンを選択できます。 すべてのマシンを選択したら、 **[適用]** を選択します。 このプロセスの最後で、選択した未インストールのマシンに Azure Monitor エージェントがインストールされます。

1. **[収集]** タブで、収集する [イベントのセット](#event-id-reference)を選択するか、 **[カスタム]** を選択して他のログを指定するか、[XPath クエリ](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)を使用してイベントをフィルター処理します。 収集するイベントの特定の XML 条件を評価する式をボックスに入力し、 **[追加]** を選択します。 1 つのボックスには最大 20 個の式を、ルールには最大 100 個のボックスを入力できます。

    [データ収集ルール](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr)の詳細については、Azure Monitoring のドキュメントを参照してください。

    > [!NOTE]
    > - Windows セキュリティと AppLocker ログのみにクエリを実行するようにしてください。 他の Windows ログからのイベント、または他の環境のセキュリティ ログからのイベントは、Windows セキュリティ イベント スキーマに準拠しておらず、正しく解析されない場合があります。その場合、ワークスペースには取り込まれません。
    >
    > - Azure Monitor エージェントは、 **[XPath バージョン 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) のみ** の XPath クエリをサポートしています。

1. 必要なすべてのフィルター式を追加した後、 **[次へ: 確認および作成]** を選択します。

1. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。 

コネクタ ページの **[構成]** に、すべてのデータ収集ルール (API を使用して作成されたルールを含む) が表示されます。 そこから既存のルールを編集または削除することができます。

> [!TIP]
> XPath クエリの有効性をテストするには、 *-FilterXPath* パラメーターを指定した PowerShell コマンドレット **Get-WinEvent** を使用します。 次のスクリプトは、一例を示しています。
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - イベントが返されたら、クエリは有効です。
> - *[No events were found that match the specified selection criteria.]\(指定した選択条件に一致するイベントは見つかりませんでした。\)* というメッセージが表示された場合は、クエリはおそらく有効ですが、一致するイベントがローカル コンピューターにありません。
> - *[The specified query is invalid]\(指定したクエリは無効です\)* というメッセージが表示された場合は、クエリ構文が無効です。

### <a name="create-data-collection-rules-using-the-api"></a>API を使用してデータ収集ルールを作成する

また、API ([スキーマ](/rest/api/monitor/data-collection-rules)を参照) を使用してデータ収集ルールを作成することもできます。これは、多くのルールを作成する場合 (たとえば、MSSP の場合) の作業が容易になる可能性があります。 ルールを作成するためのテンプレートとして使用できる例を次に示します。

**要求 URL とヘッダー**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```
**要求 URL とヘッダー**

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



---

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、約 20 分かかる場合があります。 

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>異常な RDP ログイン検出用にセキュリティ イベント/Windows セキュリティ イベント コネクタを構成する

> [!IMPORTANT]
> 異常な RDP ログイン検出は現在、パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel はセキュリティ イベント データに機械学習 (ML) を適用して、異常なリモート デスクトップ プロトコル (RDP) ログイン アクティビティを識別できます。 シナリオには以下が含まれます。

- **通常とは異なる IP** - その IP アドレスが過去 30 日間にほとんどまたはまったく確認されていない

- **通常とは異なる地理的な場所** - IP アドレス、市区町村、国、および ASN が過去 30 日間にほとんどまたはまったく確認されていない

- **新しいユーザー** - 新しいユーザーが、過去 30 日間のデータに基づいて予期されないか確認されていない (またはその両方の) IP アドレスおよび地理的な場所からログインしている。

**構成の手順**

1. **レガシ イベント経由のセキュリティ イベント** または **AMA から Windows セキュリティ イベント** データ コネクタを使用して、RDP ログイン データ (イベント ID 4624) を収集している必要があります。 イベント セットを Azure Sentinel にストリーミングするには、"None" 以外の[イベント セット](#event-id-reference)を選択するか、このイベント ID を含むデータ収集ルールを作成します。

1. Azure Sentinel ポータルで、 **[分析]** を選択した後、 **[規則のテンプレート]** タブを選択します。 **[(Preview) Anomalous RDP Login Detection]\((プレビュー) 異常な RDP ログイン検出\)** 規則を選択し、 **[状態]** スライダーを **[有効]** に移動します。

    > [!NOTE]
    > 機械学習アルゴリズムでは、ユーザー動作のベースライン プロファイルを作成するために 30 日間分のデータが必要であるため、インシデントを検出する前に、30 日間の Windows セキュリティ イベント データの収集を許可する必要があります。

## <a name="event-id-reference"></a>イベント ID 参照

次の一覧は、各セットのすべてのセキュリティ イベント ID と AppLocker イベント ID を示します。

| イベント セット | 収集されるイベント ID |
| --- | --- |
| **最小** | 1102、4624、4625、4657、4663、4688、4700、4702、4719、4720、4722、4723、4724、4727、4728、4732、4735、4737、4739、4740、4754、4755、4756、4767、4799、4825、4946、4948、4956、5024、5033、8001、8002、8003、8004、8005、8006、8007、8222 |
| **共通** | 1、299、300、324、340、403、404、410、411、412、413、431、500、501、1100、1102、1107、1108、4608、4610、4611、4614、4622、4624、4625、4634、4647、4648、4649、4657、4661、4662、4663、4665、4666、4667、4688、4670、4672、4673、4674、4675、4689、4697、4700、4702、4704、4705、4716、4717、4718、4719、4720、4722、4723、4724、4725、4726、4727、4728、4729、4733、4732、4735、4737、4738、4739、4740、4742、4744、4745、4746、4750、4751、4752、4754、4755、4756、4757、4760、4761、4762、4764、4767、4768、4771、4774、4778、4779、4781、4793、4797、4798、4799、4800、4801、4802、4803、4825、4826、4870、4886、4887、4888、4893、4898、4902、4904、4905、4907、4931、4932、4933、4946、4948、4956、4985、5024、5033、5059、5136、5137、5140、5145、5632、6144、6145、6272、6273、6278、6416、6423、6424、8001、8002、8003、8004、8005、8006、8007、8222、26401、30004 |
|

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Windows セキュリティ イベントを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [組み込み](detect-threats-built-in.md)または[カスタム](detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。
