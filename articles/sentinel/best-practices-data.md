---
title: Microsoft Sentinel でのデータ収集のベスト プラクティス
description: データ ソースを Microsoft Sentinel に接続するときに使用するベスト プラクティスについて説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b64e7d9cedeb0e979f6b733163617a67c69d79fe
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714639"
---
#  <a name="data-collection-best-practices"></a>データ収集のベスト プラクティス

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このセクションでは、Microsoft Sentinel データ コネクタを使用してデータを収集するためのベスト プラクティスについて確認します。 詳細については、[データ ソースの接続](connect-data-sources.md)に関するページ、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md)、[Microsoft Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)を参照してください。

## <a name="prioritize-your-data-connectors"></a>データ コネクタの優先順位を設定する

どのデータ コネクタがお使いの環境に最適か不明な場合は、まずすべての[無料のデータ コネクタ](billing.md#free-data-sources)を有効にしてください。

無料のデータ コネクタにより、すぐに Microsoft Sentinel の価値が示されるようになり、その間に、引き続き他のデータ コネクタや予算についての計画を立てることができます。

[パートナー](data-connectors-reference.md)および[カスタム](create-custom-connector.md)のデータ コネクタについては、[Syslog](connect-syslog.md) および [CEF](connect-common-event-format.md) コネクタの設定から開始し、Linux ベースのデバイスとともに、優先度の高いものから始めます。

データ インジェストのコストが高くなるのが早すぎる場合は、[Azure Monitor エージェント](../azure-monitor/agents/azure-monitor-agent-overview.md)を使用して、転送されるログを停止するかフィルター処理します。

> [!TIP]
> カスタム データ コネクタを使用すると、エージェント、Logstash、API 経由など、組み込み機能では現在サポートされていないデータ ソースから Microsoft Sentinel にデータを取り込むことができます。 詳細については、「[Microsoft Sentinel カスタム コネクタを作成するためのリソース](create-custom-connector.md)」を参照してください。
>

## <a name="filter-your-logs-before-ingestion"></a>インジェストの前にログをフィルター処理する

データが Microsoft Sentinel に取り込まれる前に、収集されたログや、場合によってはログ コンテンツをフィルター処理することもできます。 たとえば、セキュリティ操作に関係のないログや重要ではないログを除外したり、ログ メッセージから不要な情報を削除したりすることができます。 メッセージの内容のフィルター処理は、関係のない情報が多く含まれている Syslog、CEF、または Windows ベースのログを処理するときに、コストを削減するのに役立つこともあります。

次のいずれかの方法を使用して、ログをフィルター処理します。

- **Azure Monitor エージェント**。 [Windows セキュリティ イベント](connect-windows-security-events.md)を取り込むために、Windows と Linux の両方でサポートされています。 指定されたイベントのみを収集するようにエージェントを構成することによって、収集されるログをフィルター処理します。

- **Logstash**。 ログ メッセージへの変更を含む、メッセージ内容のフィルター処理をサポートします。 詳細については、「[Logstash を使用して接続する](create-custom-connector.md#connect-with-logstash)」を参照してください。

> [!IMPORTANT]
> Logstash を使用してメッセージの内容をフィルター処理すると、ログがカスタム ログとして取り込まれ、[Free レベルのログ](billing.md#free-data-sources)が有料レベルのログになります。
>
> カスタム ログは自動的に追加されないため、[分析ルール](automate-incident-handling-with-automation-rules.md)、[脅威ハンティング](hunting.md)、および[ブック](get-visibility.md)に組み込む必要もあります。 また、カスタム ログは現在、[機械学習](bring-your-own-ml.md)機能に対してサポートされていません。
>

## <a name="alternative-data-ingestion-requirements"></a>代替のデータ インジェストの要件

さまざまな課題により、データ収集の標準構成が組織に適さない場合もあります。 次の表には、一般的な課題や要件、および考えられる解決策と考慮事項が記載されています。

> [!NOTE]
> 以下に示す多くの解決策にはカスタム データ コネクタが必要です。 詳細については、「[Microsoft Sentinel カスタム コネクタを作成するためのリソース](create-custom-connector.md)」を参照してください。
>

### <a name="on-premises-windows-log-collection"></a>オンプレミスの Windows ログ収集


|課題/要件  |考えられる解決策  |考慮事項  |
|---------|---------|---------|
|**ログのフィルター処理が必要**     | Logstash を使用する <br><br>Azure Functions の使用 <br><br> LogicApps を使用する <br><br> カスタム コード (.NET、Python) を使用する  |  フィルター処理はコスト削減につながる場合があり、必要なデータだけが取り込まれますが、[UEBA](identify-threats-with-entity-behavior-analytics.md)、[ エンティティ ページ ](identify-threats-with-entity-behavior-analytics.md#entity-pages)、[ 機械学習 ](bring-your-own-ml.md)、[Fusion](fusion.md) などの一部の Microsoft Sentinel 機能はサポートされていません。 <br><br>ログのフィルター処理を構成する場合は、脅威ハンティング クエリや分析ルールなどのリソースを更新する必要があります。     |
|**エージェントをインストールできない**     |[Azure Monitor エージェント](connect-windows-security-events.md#connector-options)でサポートされている Windows イベント転送を使用する       |   Windows イベント転送を使用すると、Windows イベント コレクターからの 1 秒あたりの負荷分散イベントが 10,000 イベントから 500 ～ 1000 イベントに減少します。|
|**サーバーをインターネットに接続できない**     | [Log Analytics ゲートウェイ](../azure-monitor/agents/gateway.md)を使用する        | エージェントへのプロキシを構成するには、ゲートウェイを機能させるための追加のファイアウォール規則が必要です。        |
|**インジェスト時にタグ付けとエンリッチメントが必要**     |Logstash を使用して ResourceID を挿入する <br><br>ARM テンプレートを使用して、ResourceID をオンプレミスのマシンに挿入する <br><br>リソース ID を別のワークスペースに取り込む        | Log Analytics はカスタム テーブルの RBAC をサポートしていません <br><br>Microsoft Sentinel では、行レベルの RBAC をサポートしていません <br><br>**ヒント**: Microsoft Sentinel 向けのクロス ワークスペースの設計や機能を導入することもできます。        |
|**操作とセキュリティのログの分割が必要**     | [Microsoft Monitoring Agent または Azure Monitor エージェント](connect-windows-security-events.md)のマルチホーム機能を使用する        |  マルチホーム機能では、エージェントのデプロイのオーバーヘッドが増加します。       |
|**カスタム ログが必要**     |   特定のフォルダー パスからファイルを収集する <br><br>API インジェストを使用する <br><br>PowerShell の使用 <br><br>Logstash を使用する     |   ログのフィルター処理で問題が発生する可能性があります。 <br><br>カスタム メソッドはサポートされません。 <br><br>カスタム コネクタでは、開発者のスキルが必要になる場合があります。       |
| | | |

### <a name="on-premises-linux-log-collection"></a>オンプレミスの Linux ログの収集

|課題/要件  |考えられる解決策  |考慮事項  |
|---------|---------|---------|
|**ログのフィルター処理が必要**     | Syslog-NG を使用する <br><br>Rsyslog を使用する <br><br>エージェントの FluentD 構成を使用する <br><br> Azure Monitor エージェントまたは Microsoft Monitoring Agent を使用する <br><br> Logstash を使用する  |  一部の Linux ディストリビューションは、エージェントでサポートされない可能性があります。 <br> <br>Syslog または FluentD を使用するには、開発者向けの知識が必要です。 <br><br>詳細については、[セキュリティ イベントを収集するための Windows サーバーへの接続](connect-windows-security-events.md)に関するページおよび「[Microsoft Sentinel カスタム コネクタを作成するためのリソース](create-custom-connector.md)」を参照してください。      |
|**エージェントをインストールできない**     |  syslog-ng や rsyslog などの Syslog フォワーダーを使用する。       |         |
|**サーバーをインターネットに接続できない**       | [Log Analytics ゲートウェイ](../azure-monitor/agents/gateway.md)を使用する        | エージェントへのプロキシを構成するには、ゲートウェイを機能させるための追加のファイアウォール規則が必要です。          |
|**インジェスト時にタグ付けとエンリッチメントが必要**      | エンリッチメント用の Logstash、または API や EventHubs などのカスタム メソッドを使用する。         | フィルター処理に追加の作業が必要になる場合があります。    |
|**操作とセキュリティのログの分割が必要**     |  [Azure Monitor エージェント](connect-windows-security-events.md)をマルチホーム構成で使用する。       |         |
|**カスタム ログが必要**     |    Microsoft Monitoring (Log Analytics) エージェントを使用して、カスタム コレクターを作成する。      |      |
| | | |

### <a name="endpoint-solutions"></a>エンドポイント ソリューション

EDR、その他のセキュリティ イベント、Sysmon などのエンドポイント ソリューションからログを収集することが必要な場合は、次のいずれかの方法を使用します。

- Microsoft 365 Defender for Endpoint からログを収集するための **MTP コネクタ**。 このオプションでは、データ インジェストに追加コストが発生します。
- **Windows イベント転送**。

> [!NOTE]
> 負荷分散により、ワークスペースに対して処理できる 1 秒あたりのイベント数が減少します。
>

### <a name="office-data"></a>Office データ

標準のコネクタ データ以外で Microsoft Office のデータを収集する必要がある場合は、次のいずれかの解決策を使用します。

|課題/要件  |考えられる解決策  |考慮事項  |
|---------|---------|---------|
|**Teams、メッセージ トレース、フィッシング データなどから生データを収集する**     |    組み込みの [Office 365 コネクタ](./data-connectors-reference.md#microsoft-office-365)機能を使用し、次に、その他の生データ用のカスタム コネクタを作成する。  |  イベントを対応する recordID にマッピングすることが困難な場合があります。  |
|**国や部門などを分割するための RBAC が必要**     | データにタグを追加し、必要な分離ごとに専用のワークスペースを作成することによって、データ収集をカスタマイズする。|   カスタム データ収集には余分なインジェスト コストがかかります。     |
|**1 つのワークスペースに複数のテナントが必要**     |  Azure LightHouse と統合されたインシデント ビューを使用して、データ収集をカスタマイズする。|  カスタム データ収集には余分なインジェスト コストがかかります。  <br><br>詳細については、「[ワークスペースおよびテナント全体での Microsoft Sentinel の拡張](extend-sentinel-across-workspaces-tenants.md)」を参照してください。      |
| | | |

### <a name="cloud-platform-data"></a>クラウド プラットフォーム データ

|課題/要件  |考えられる解決策  |考慮事項  |
|---------|---------|---------|
|**他のプラットフォームのログをフィルター処理する**     | Logstash を使用する <br><br>Azure Monitor エージェントまたは Microsoft Monitoring (Log Analytics) エージェントを使用する       |    カスタム コレクションには余分なインジェスト コストがかかります。 <br><br>すべての Windows イベントを収集するか、セキュリティ イベントのみを収集するかという課題が生じる可能性があります。     |
|**エージェントを使用できない**     |   Windows イベント転送を使用する      | リソース全体での負荷分散が必要になる場合があります。        |
|**サーバーがエアギャップ ネットワークにある**     | [Log Analytics ゲートウェイ](../azure-monitor/agents/gateway.md)を使用する        | エージェントへのプロキシを構成するには、ゲートウェイを機能させるためのファイアウォール規則が必要です。        |
|**インジェスト時に RBAC、タグ付け、エンリッチメントが必要**     |    Logstash または Log Analytics API を使用してカスタム コレクションを作成する。     |  RBAC はカスタム テーブルでサポートされません <br><br>行レベルの RBAC は、すべてのテーブルでサポートされません。       |
|     |         |         |

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel のデプロイ前のアクティビティとデプロイの前提条件](prerequisites.md)
- [Microsoft Sentinel のベスト プラクティス](best-practices.md)
- [データ ソースの接続](connect-data-sources.md)
