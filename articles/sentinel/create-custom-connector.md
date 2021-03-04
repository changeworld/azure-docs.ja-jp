---
title: Azure Sentinel カスタム コネクタを作成するためのリソース | Microsoft Docs
description: Azure Sentinel 用のカスタム コネクタを作成するために使用できるリソースについて説明します。 Log Analytics エージェントと API、Logstash、Logic Apps、PowerShell、Azure Functions などの方法があります。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724355"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Azure Sentinel カスタム コネクタを作成するためのリソース

Azure Sentinel を使用すると、[Azure サービスと外部ソリューション用のさまざまな組み込みコネクタ](connect-data-sources.md)が提供され、一部のソースからの専用コネクタを使用しないデータの取り込みもサポートされます。

利用可能な既存のソリューションを使用してデータ ソースを Azure Sentinel に接続できない場合は、独自のデータ ソース コネクタを作成することを検討してください。

サポートされているすべてのコネクタの詳細な一覧については、「[Azure Sentinel: すべてのコネクタ (CEF、Syslog、Direct、Agent、Custom その他)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891)」ブログ記事を参照してください。

## <a name="compare-custom-connector-methods"></a>カスタム コネクタの方法を比較する

次の表は、この記事で説明されている各カスタム コネクタ作成方法に関する重要な詳細を比較したものです。 各方法の詳細については、表内のリンクを選択してください。

|方法の説明  |機能 | サーバーレス    |複雑さ  |
|---------|---------|---------|---------|
|**[Log Analytics エージェント](#connect-with-the-log-analytics-agent)** <br>オンプレミスと IaaS のソースからファイルを収集する場合に最適です   | ファイル収集のみ  |   いいえ      |低         |
|**[Logstash](#connect-with-logstash)** <br>オンプレミスと IaaS のソース、プラグインを利用できるソース、および Logstash に既に慣れている組織に最適です  | 利用可能なプラグインとカスタム プラグインの機能により、高い柔軟性が提供されます。   |   いいえ、実行するには VM または VM クラスターが必要です           |   低、プラグインで多くのシナリオがサポートされます      |
|**[Logic Apps](#connect-with-logic-apps)** <br>高コストであり、大量のデータへの使用は避けます <br>少量のクラウド ソースに最適です  | コード不要のプログラミングのため柔軟性は制限され、アルゴリズムの実装はサポートされません。<br><br> 要件が既にサポートされている使用可能なアクションがない場合、カスタム アクションを作成すると複雑になる可能性があります。    |    はい         |   低、シンプルでコード不要の開発      |
|**[PowerShell](#connect-with-powershell)** <br>プロトタイプと定期的なファイル アップロードに最適です | ファイル収集が直接サポートされます。 <br><br>PowerShell を使用すると、より多くのソースを収集できますが、スクリプトをコーディングし、サービスとして構成する必要があります。      |いいえ               |  低       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>統合を実装する ISV、および固有の収集要件に最適です   | コードで使用できるすべての機能がサポートされます。  | 実装によって異なります           |     高    |
|**[Azure Functions](#connect-with-azure-functions)** 大量のクラウド ソースおよび固有の収集要件に最適です  | コードで使用できるすべての機能がサポートされます。  |  はい             |     高、プログラミングに関する知識が必要です    |
|     |         |                |

> [!TIP]
> Logic Apps と Azure Functions を同じコネクタに使用する場合の比較については、次を参照してください。
> 
> - [Fastly Web アプリケーション ファイアウォールのログを Azure Sentinel に取り込む](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Azure Sentinel GitHub コミュニティ):[Logic Apps コネクタ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data) | [Azure Functions コネクタ](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Log Analytics エージェントを使用して接続する

データ ソースによってファイルにイベントが配信される場合は、Azure Monitor Log Analytics エージェントを使用してカスタム コネクタを作成することをお勧めします。

- 詳細については、[Azure Monitor でのカスタム ログの収集](../azure-monitor/agents/data-sources-custom-logs.md)に関するページを参照してください。

- この方法の例については、「[Azure Monitor での Linux 用 Log Analytics エージェントを使用したカスタム JSON データ ソースの収集](../azure-monitor/agents/data-sources-json.md)」を参照してください。

## <a name="connect-with-logstash"></a>Logstash を使用して接続する

[Logstash](https://www.elastic.co/logstash) を使い慣れている場合は、Logstash と [Azure Sentinel 用 Logstash 出力プラグイン](connect-logstash.md)を使用して、カスタム コネクタを作成できます。

Azure Sentinel の Logstash 出力プラグインを使用すると、Logstash の任意の入力およびフィルター プラグインを使用し、Logstash パイプラインの出力として Azure Sentinel を構成することができます。 Logstash に用意されているプラグインの大きなライブラリを使用すると、Event Hubs、Apache Kafka、ファイル、データベース、クラウド サービスなど、さまざまなソースから入力できます。 フィルター プラグインを使用すると、イベントの解析、不要なイベントの除外、値の難読化などを行うことができます。

Logstash をカスタム コネクタとして使用する例については、次を参照してください。

- [Azure Sentinel を使用した AWS ログでの Capital One 侵害の TTP のハンティング](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (ブログ)
- [Radware Azure Sentinel 実装ガイド](https://support.radware.com/ci/okcsFattach/get/1025459_3)

役に立つ Logstash プラグインの例については、次を参照してください。

- [Cloudwatch 入力プラグイン](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs プラグイン](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage 入力プラグイン](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub 入力プラグイン](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash を使用すると、クラスターを使用するスケーリングされたデータ収集も有効になります。 詳細については、[負荷分散された Logstash VM の大規模な使用](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)に関するページを参照してください。
>

## <a name="connect-with-logic-apps"></a>Logic Apps を使用して接続する

Azure Sentinel 用のサーバーレスのカスタム コネクタを作成するには、[Azure ロジック アプリ](../logic-apps/index.yml)を使用します。

> [!NOTE]
> Logic Apps を使用してサーバーレス コネクタを作成すると便利な場合がありますが、コネクタに Logic Apps を使用すると、大量のデータではコストがかかる可能性があります。
>
> この方法は、量の少ないデータ ソースの場合、またはデータのアップロードをエンリッチする場合にのみ使用することをお勧めします。
>

1. **次のいずれかのトリガーを使用して、Logic Apps を開始します**。

    |トリガー  |[説明]  |
    |---------|---------|
    |**定期的なタスク**     |   たとえば、特定のファイル、データベース、または外部 API から定期的にデータを取得するように、ロジック アプリをスケジュールします。 <br>詳細については、[Azure Logic Apps での定期的なタスクとワークフローの作成、スケジュール、実行](../connectors/connectors-native-recurrence.md)に関するページを参照してください。      |
    |**オンデマンド トリガー**     | 手動によるデータの収集とテストのために、ロジック アプリをオンデマンドで実行します。 <br>詳細については、[HTTPS エンドポイントを使用したロジック アプリの呼び出し、トリガー、入れ子化](../logic-apps/logic-apps-http-endpoint.md)に関するページを参照してください。        |
    |**HTTP/S エンドポイント**     |  ストリーミングの場合、およびソース システムがデータ転送を開始できる場合に推奨されます。 <br>詳細については、[HTTP または HTTPS によるサービス エンドポイントの呼び出し](../connectors/connectors-native-http.md)に関するページを参照してください。       |
    |     |         |

1. **情報を読み取るいずれかの Logic Apps コネクタを使用して、イベントを取得します**。 次に例を示します。

    - [REST API に接続する](/connectors/custom-connectors/)
    - [SQL Server に接続する](/connectors/sql/)
    - [ファイル システムに接続する](/connectors/filesystem/)

    > [!TIP]
    > REST API、SQL Server、ファイル システムへのカスタム コネクタでは、オンプレミスのデータ ソースからのデータの取得もサポートされています。 詳細については、[オンプレミス データ ゲートウェイのインストール](/connectors/filesystem/)に関するドキュメントを参照してください。
    >

1. **取得する情報を準備します**。

    たとえば、[JSON 解析アクション](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)を使用して JSON コンテンツのプロパティにアクセスすると、ロジック アプリの入力を指定するときに、動的コンテンツ リストからそれらのプロパティを選択できるようになります。

    詳細については、「[Azure Logic Apps でデータの操作を実行する](../logic-apps/logic-apps-perform-data-operations.md)」を参照してください。

1. **Log Analytics にデータを書き込みます**。

    詳細については、[Azure Log Analytics データ コレクター](/connectors/azureloganalyticsdatacollector/)のドキュメントを参照してください。

Logic Apps を使用して Azure Sentinel 用のカスタム コネクタを作成する方法の例については、次を参照してください。

- [データ コレクター API によるデータ パイプラインの作成](/connectors/azureloganalyticsdatacollector/)
- [Webhook を使用した Palo Alto Prisma ロジック アプリ コネクタ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub コミュニティ)
- [スケジュールされたアクティブ化を使用して Microsoft Teams の呼び出しをセキュリティで保護する](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (ブログ)
- [Azure Sentinel への AlienVault OTX 脅威インジケーターの取り込み](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (ブログ)
- [Azure Sentinel への Proofpoint TAP ログの送信](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (ブログ)


## <a name="connect-with-powershell"></a>PowerShell で接続する

[Upload-AzMonitorLog PowerShell スクリプト](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/)を使用すると、コマンド ラインから PowerShell を使用してイベントまたはコンテキスト情報を Azure Sentinel にストリーミングできます。 このストリーミングにより、実質的に、データ ソースと Azure Sentinel の間のカスタム コネクタが作成されます。

たとえば、次のスクリプトを使用すると、CSV ファイルが Azure Sentinel にアップロードされます。

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[Upload-AzMonitorLog PowerShell スクリプト](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/)で使用されるパラメーターは次のとおりです。

|パラメーター  |[説明]  |
|---------|---------|
|**WorkspaceId**     |   データを格納する Azure Sentinel ワークスペースの ID。  [ワークスペースの ID とキーを確認します](#find-your-workspace-id-and-key)。  |
|**WorkspaceKey**     |   データを格納する Azure Sentinel ワークスペースのプライマリまたはセカンダリ キー。 [ワークスペースの ID とキーを確認します](#find-your-workspace-id-and-key)。  |
|**LogTypeName**     |    データを格納するカスタム ログ テーブルの名前。 サフィックス **_CL** が、テーブル名の末尾に自動的に追加されます。  |
|**AddComputerName**     |   このパラメーターが存在する場合、スクリプトにより、すべてのログ レコードの **Computer** という名前のフィールドに、現在のコンピューター名が追加されます。      |
|**TaggedAzureResourceId**     | このパラメーターが存在する場合、スクリプトにより、アップロードされたすべてのログ レコードと指定した Azure リソースが関連付けられます。 <br><br>この関連付けにより、アップロードされたログ レコードはリソース コンテキスト クエリに対して有効になり、リソース中心のロールベースのアクセス制御に準拠します。       |
|**AdditionalDataTaggingName**     |      このパラメーターが存在する場合、スクリプトにより、構成されている名前および **AdditionalDataTaggingValue** パラメーターに構成されている値を使用して、すべてのログ レコードに別のフィールドが追加されます。 <br><br>この場合、**AdditionalDataTaggingValue** を空にすることはできません。 |
|**AdditionalDataTaggingValue**     |   このパラメーターが存在する場合、スクリプトにより、構成されている値および **AdditionalDataTaggingName** パラメーターに構成されている名前を使用して、すべてのログ レコードに別のフィールドが追加されます。 <br><br>**AdditionalDataTaggingName** パラメーターが空であるのに、値が構成されている場合、既定のフィールド名は **DataTagging** になります。       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>ワークスペースの ID とキーを確認する

Azure Sentinel で **WorkspaceID** と **WorkspaceKey** パラメーターの詳細を確認します。

1. Azure Sentinel の左側で **[設定]** を選択し、 **[ワークスペースの設定]** タブを選択します。

1. **[Log Analytics の使用を開始する]**  >  **[1 データ ソースへの接続]** で、 **[Windows and Linux agents management]\(Windows および Linux エージェントの管理\)** を選択します。

1. **[Windows サーバー]** タブで、ワークスペース ID、プライマリ キー、セカンダリ キーを確認します。
## <a name="connect-with-the-log-analytics-api"></a>Log Analytics API を使用して接続する

Log Analytics Data Collector API を使用して RESTful エンドポイントを直接呼び出すことにより、Azure Sentinel にイベントをストリーミングできます。

RESTful エンドポイントを直接呼び出すには、より多くのプログラミングが必要ですが、柔軟性も高くなります。

詳細については、[Log Analytics Data Collector API](../azure-monitor/logs/data-collector-api.md) に関するページの、特に次の例を参照してください。

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Azure Functions を使用して接続する

Azure Functions と RESTful API および [PowerShell](../azure-functions/functions-reference-powershell.md) などのさまざまなコーディング言語を使用して、サーバーレスのカスタム コネクタを作成します。

この方法の例については、以下を参照してください。

- [Azure Function を使用して、VMware Carbon Black Cloud Endpoint Standard を Azure Sentinel に接続します](connect-vmware-carbon-black.md)
- [Azure 関数を使用して Okta シングル サインオンを Azure Sentinel に接続する](connect-okta-single-sign-on.md)
- [Azure Functions を使用して Proofpoint TAP を Azure Sentinel に接続する](connect-proofpoint-tap.md)
- [Azure Functions を使用して Qualys VM を Azure Sentinel に接続する](connect-qualys-vm.md)
- [XML や CSV など各種形式のデータを取り込む](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Azure Sentinel を使用した Zoom の監視](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (ブログ)
- [Office 365 Management API のデータを取得するための関数アプリを Azure Sentinel にデプロイする](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure Sentinel GitHub コミュニティ)

## <a name="parse-your-custom-connector-data"></a>カスタム コネクタのデータを解析する

カスタム コネクタの組み込み解析手法を使用して、関連する情報を抽出し、Azure Sentinel の関連するフィールドに設定することができます。

次に例を示します。

- **Logstash を使用した場合** は、[Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) フィルター プラグインを使用してデータを解析します。
- **Azure 関数を使用した場合** は、コードを使用してデータを解析します。 詳細については、「[パーサー](normalization.md#parsers)」を参照してください。

Azure Sentinel は、クエリ時の解析に対応しています。 クエリ時の解析を使用すると、データを元の形式でプッシュし、必要に応じてオンデマンドで解析することができます。

また、クエリ時の解析を使用すると、カスタム コネクタを作成するときにデータの正確な構造を事前に、または抽出する必要のある情報さえ、把握しておく必要がありません。 代わりに、調査中であっても、いつでもデータを解析できます。

> [!NOTE]
> パーサーの更新は、Azure Sentinel に既に取り込んだデータにも適用されます。
> 
## <a name="next-steps"></a>次のステップ

Azure Sentinel に取り込まれたデータを使用して、次のいずれかのプロセスで環境をセキュリティ保護します。

- [アラートの視覚化](quickstart-get-visibility.md)
- [ データの視覚化と監視](tutorial-monitor-your-data.md)
- [インシデントの調査](tutorial-investigate-cases.md)
- [脅威を検出する](tutorial-detect-threats-built-in.md)
- [脅威の防止の自動化](tutorial-respond-threats-playbook.md)
- [脅威の探索](hunting.md)