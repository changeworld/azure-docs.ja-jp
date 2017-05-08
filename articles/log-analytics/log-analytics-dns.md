---
title: "Azure Log Analytics の DNS 分析ソリューション | Microsoft Docs"
description: "Log Analytics の DNS 分析ソリューションをセットアップし、このソリューションを使用して、DNS インフラストラクチャに関するセキュリティ、パフォーマンス、操作に関連する洞察を収集します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 4473ca68374b96f10c60282135e83f7ec390bb48
ms.lasthandoff: 04/20/2017


---

# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>DNS 分析 (プレビュー) ソリューションを使用した DNS インフラストラクチャに関する洞察の収集

この記事では、Log Analytics の DNS 分析ソリューションをセットアップし、このソリューションを使用して、DNS インフラストラクチャに関するセキュリティ、パフォーマンス、操作に関連する洞察を収集する方法について説明します。

DNS 分析により、次のことが可能になります。

- 悪意のあるドメイン名を解決しようとしているクライアントを特定する
- 古いリソース レコードを特定する
- クエリ対象になる頻度が高いドメイン名と話好きな DNS クライアントを特定する
- DNS サーバーの要求負荷を確認する
- 動的 DNS 登録エラーを確認する

DNS 分析ソリューションは、Windows DNS の分析ログと監査ログおよび他の関連データを DNS サーバーから収集して分析し、関連付けます。

## <a name="connected-sources"></a>接続先ソース

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| **接続先ソース** | **サポート** | **説明** |
| --- | --- | --- |
| [Windows エージェント](log-analytics-windows-agents.md) | はい | ソリューションでは、Windows エージェントから DNS 情報を収集します。 |
| [Linux エージェント](log-analytics-linux-agents.md) | なし | ソリューションでは、ダイレクト Linux エージェントから DNS 情報は収集しません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | はい | ソリューションでは、接続された SCOM 管理グループ内のエージェントから DNS 情報を収集します。 SCOM エージェントから OMS への直接接続は必要ありません。 データは管理グループから OMS リポジトリに転送されます。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | なし | ソリューションでは、Azure Storage は使用されません。 |

### <a name="data-collection-details"></a>データ収集の詳細

DNS 分析ソリューションでは、Log Analytics エージェントがインストールされている DNS サーバーから DNS インベントリと DNS イベント関連データを収集します。 このデータが Log Analytics にアップロードされ、ソリューション ダッシュボードに表示されます。 インベントリ関連データ (DNS サーバーの数、ゾーンの数、リソース レコードの数など) は、DNS PowerShell コマンドレットを実行して収集します。 データは 2 日に 1 回更新されます。 イベント関連データは、Windows Server 2012 R2 の強化された DNS ログと診断機能によって提供される[分析ログと監査ログ](https://technet.microsoft.com/library/dn800669.aspx#enhanc)からほぼリアルタイムで収集されます。

## <a name="configuration"></a>構成

次の情報を使用して、ソリューションを構成します。

- 監視対象の各 DNS サーバーに [Windows](log-analytics-windows-agents.md) エージェントまたは [Operations Manager](log-analytics-om-agents.md) エージェントが必要です。
- DNS 分析ソリューションを OMS ワークスペースに追加します。[Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関する記事に記載されている手順に従って追加します。

さらに構成を行わなくても、ソリューションはデータの収集を開始します。 ただし、次の構成を使用してデータ収集をカスタマイズできます。

###  <a name="configure-the-solution"></a>ソリューションの構成

ソリューション ダッシュボードで **[構成]** をクリックして、[DNS 分析構成] ページを開きます。 次の 2 種類の構成変更を行うことができます。

- **[ホワイトリストに含まれるドメイン名]**: ソリューションでは、すべてのルックアップ クエリが処理されるわけではありません。 ドメイン名サフィックスのホワイトリストが保持されています。 このホワイトリストのドメイン名サフィックスと一致するドメイン名に解決されるルックアップ クエリは処理されません。 ホワイトリストに含まれるドメイン名を処理しないことで、Log Analytics に送信されるデータを最適化できます。 既定のホワイトリストには、よく使用されるパブリック ドメイン名 (www.google.com や www.facebook.com など) が含まれています。 スクロール バーを使用して、既定のリスト全体を確認できます。

このリストを変更して、ルックアップの洞察の対象としないドメイン名サフィックスを追加したり、ドメイン名サフィックスを削除したりできます。

- **[話好きなクライアントのしきい値]**: ルックアップ要求数のしきい値を超えた DNS クライアントは、**[DNS クライアント]** ブレードで強調表示されます。 既定のしきい値は 1000 です。 しきい値は編集できます。

![ホワイトリストに含まれるドメイン名](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>管理パック

Microsoft Monitoring Agent (MMA) を使用して OMS ワークスペースに接続すると、次の管理パックがインストールされます。

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

SCOM 管理グループが OMS ワークスペースに接続されている場合、このソリューションを追加したときに次の管理パックが SCOM にインストールされます。 これらの管理パックの構成や保守は不要です。

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。

## <a name="use-the-solution"></a>ソリューションの使用

このセクションでは、すべてのダッシュボード機能とそれらの使用方法について説明します。

DNS 分析ソリューションをワークスペースに追加すると、OMS の概要ページのソリューション タイルに DNS インフラストラクチャの簡単な概要が示されます。 これには、データが収集されている DNS サーバーの数と、悪意のあるドメインを解決するために過去 24 時間にクライアントによって行われた要求の数が含まれています。 タイルをクリックすると、ソリューション ダッシュボードが開きます。

![[DNS 分析] タイル](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>ソリューションのダッシュボード

ソリューション ダッシュボードには、ソリューションのさまざまな機能の集計情報が表示されます。 また、フォレンジック分析と診断の詳細ビューへのリンクも含まれています。 既定では、過去 7 日間のデータが表示されます。 次の図のように、日時選択コントロールを使用して日付と時間の範囲を変更できます。

![日時選択コントロール](./media/log-analytics-dns/dns-time.png)

ソリューション ダッシュボードには、以下のブレードが表示されます。

**[DNS セキュリティ]** - 悪意のあるドメインと通信しようとしている DNS クライアントを報告します。 DNS 分析では、Microsoft 脅威インテリジェンス フィードを使用して、悪意のあるドメインにアクセスしようとしているクライアント IP を検出します。 多くの場合、マルウェアに感染したデバイスは、マルウェア ドメイン名を解決することで、悪意のあるドメインの &quot;コマンド アンド コントロール&quot; センターに &quot;ダイヤルアウト&quot; します。

![[DNS セキュリティ] ブレード](./media/log-analytics-dns/dns-security-blade.png)

一覧のクライアント IP をクリックすると、ログ検索が開き、それぞれのクエリのルックアップの詳細が表示されます。 次の例では、[IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot) との通信が行われたことが DNS 分析によって検出されています。

![IRCbot を示すログ検索の結果](./media/log-analytics-dns/ircbot.png)

この情報により、次のことを確認できます。

- 通信を開始したクライアント IP
- 悪意のある IP に解決されるドメイン名
- ドメイン名の解決先の IP アドレス
- 悪意のある IP アドレス
- 問題の重大度
- 悪意のある IP をブラックリストに含める理由
- 検出時刻

**[クエリ対象のドメイン]** - 環境内の DNS クライアントによるクエリの対象になる頻度が最も高いドメイン名が表示されます。 クエリ対象のすべてのドメイン名の一覧を表示し、ログ検索で特定のドメイン名のルックアップ要求の詳細にドリルダウンできます。

![[クエリ対象のドメイン] ブレード](./media/log-analytics-dns/domains-queried-blade.png)

**[DNS クライアント]** - 選択した期間内にクエリ数の**しきい値に違反**したクライアントを報告します。 すべての DNS クライアントの一覧を表示し、ログ検索で各クライアントによって実行されたクエリの詳細を確認できます。

![[DNS クライアント] ブレード](./media/log-analytics-dns/dns-clients-blade.png)

**[動的 DNS 登録]** - 名前登録エラーを報告します。 アドレス [リソース レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (タイプ A および AAAA) のすべての登録エラーが、その登録要求を行ったクライアント IP と共に強調表示されます。 この情報を使用して、登録エラーの根本原因を見つけることができます。

1. クライアントが更新しようとしている名前に対して権限のあるゾーンを見つけます。
2. ソリューションを使用して、そのゾーンのインベントリ情報を確認します。
3. ゾーンの動的更新が有効になっていることを確認します。
4. ゾーンがセキュリティで保護された動的更新を使用するように構成されているかどうかを確認します。

![[動的 DNS 登録] ブレード](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**[名前登録要求]** - 上のタイルには、成功および失敗した DNS 動的更新要求数の傾向が示されます。 下のタイルには、失敗した DNS 更新要求を DNS サーバーに送信した上位 10 個のクライアントが、失敗の回数で並べ替えられて表示されます。

![[名前登録要求] ブレード ](./media/log-analytics-dns/name-reg-req-blade.png)

**[DNS 分析のサンプル クエリ]** - 生の分析データを直接取得する最も一般的な検索クエリの一覧が表示されます。

![サンプル クエリ](./media/log-analytics-dns/queries.png)

これらのクエリは、カスタム レポート用の独自のクエリを作成するための出発点として使用できます。

- **[サーバーの一覧]:** このリンクをクリックすると DNS ログ検索ページが開き、DNS サーバーの一覧が表示されます。この一覧には、関連付けられている FQDN、ドメイン名、フォレスト名、サーバー IP が含まれています。
- **[DNS ゾーンの一覧]:** このリンクをクリックすると DNS ログ検索ページが開き、すべての DNS ゾーンの一覧が表示されます。この一覧には、関連付けられているゾーン名、動的更新の状態、ネーム サーバー、DNSSEC 署名の状態が含まれています。
- **[使用されていないリソース レコード]:** このリンクをクリックすると DNS ログ検索ページが開き、すべての使用されていない/古いリソース レコードの一覧が表示されます。 この一覧には、リソース レコード名、リソース レコードの種類、関連付けられている DNS サーバー、レコードの生成時刻、ゾーン名が含まれています。 この一覧を使用して、使用されなくなった DNS リソース レコードを特定できます。 この情報に基づいて、DNS サーバーからこれらのエントリを削除するための操作を実行できます。
- **[DNS サーバーのクエリ負荷]:** このリンクをクリックすると、DNS サーバーの DNS 負荷の全体像を把握できる DNS ログ検索ページが開きます。この情報はサーバーの容量計画に役立ちます。 **[メトリック]** タブに移動すると、ビューをグラフ表示に変更できます。 このビューは、DNS サーバー間で DNS 負荷がどのように分散されているかを理解する上で役立ちます。 ビューには、各サーバーの DNS クエリ レートの傾向が示されます。
    ![DNS サーバーのクエリのログ検索の結果](./media/log-analytics-dns/dns-servers-query-load.png)  
- **[DNS ゾーンのクエリ負荷]:** このリンクをクリックすると DNS ログ検索ページが開き、ソリューションによって管理されている DNS サーバーのすべてのゾーンに関する 1 秒あたりの DNS ゾーン クエリ統計情報が表示されます。 **[メトリック]** タブをクリックすると、詳細レコードから結果のグラフ表示にビューが変更されます。
- **[構成イベント]:** このリンクをクリックすると DNS ログ検索ページが開き、すべての DNS 構成変更イベントと、関連するメッセージが表示されます。 イベントの時刻、イベント ID、DNS サーバー、またはタスク カテゴリに基づいて、これらのイベントをフィルター処理できます。 これにより、特定の時点で特定の DNS サーバーに加えられた変更を監査できます。
- **[DNS 分析ログ]:** このリンクをクリックすると DNS ログ検索ページが開き、ソリューションによって管理されているすべての DNS サーバーのすべての分析イベントが表示されます。 イベントの時刻、イベント ID、DNS サーバー、ルックアップ クエリを実行したクライアント IP、クエリの種類のタスク カテゴリに基づいて、これらのイベントをフィルター処理できます。 DNS サーバー分析イベントにより、DNS サーバーで動作状況の追跡が可能になります。 サーバーが DNS 情報を送受信するたびに、分析イベントがログに記録されます。

### <a name="dns-log-search"></a>DNS ログ検索

検索クエリは [検索] ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。 検索結果に対して変換やフィルター処理、レポート作成などを実行する高度なクエリを作成することもできます。 まず、以下のクエリを使用できます。

検索クエリ フィールドに「`Type=DnsEvents`」と入力して、ソリューションによって管理されている DNS サーバーで生成されたすべての DNS イベントを表示します。 結果には、ルックアップ クエリ、動的登録、構成変更に関連するすべてのイベントのログ データが表示されます。

![dnsevents のログ検索](./media/log-analytics-dns/log-search-dnsevents.png)  

- ルックアップ クエリのログ データを表示するには、LHS ファセット コントロールから **LookUpQuery** を選択して SubType をフィルター処理します。 選択した期間のルックアップ クエリ イベントが含まれたリスト/テーブルが表示されます。
- 動的登録のログ データを表示するには、LHS ファセット コントロールから **DynamicRegistration** を選択して SubType をフィルター処理します。 選択した期間のすべての動的登録イベントが含まれたリスト/テーブルが表示されます。
- 構成変更のログ データを表示するには、LHS ファセット コントロールから **ConfigurationChange** を選択して SubType をフィルター処理します。 選択した期間のすべての構成変更イベントが含まれたリスト/テーブルが表示されます。

検索クエリ フィールドに「`Type=DnsInventory`」と入力して、ソリューションによって管理されている DNS サーバーのすべての DNS インベントリ関連データを表示します。 結果には、DNS サーバー、DNS ゾーン、リソース レコードのログ データが表示されます。
![dnsinventory のログ検索](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>フィードバック

フィードバックは次の方法でお送りいただけます。

- **UserVoice**: DNS 分析の機能についてアイデアを投稿できます。 [OMS UserVoice ページ](https://aka.ms/dnsanalyticsuservoice)をご覧ください。
- **コーホートへの参加**: Microsoft が実施しているコーホートに参加して、DNS 分析の今後の機能向上にご協力ください。コーホートに参加すると、新機能にいち早く触れることができます。 コーホートへの参加に関心がある場合は、[こちらの簡単なアンケート](https://aka.ms/dnsanalyticssurvey)にご記入ください。

## <a name="next-steps"></a>次のステップ

- [ログを検索](log-analytics-log-searches.md)して、詳細な DNS ログ レコードを確認します。

