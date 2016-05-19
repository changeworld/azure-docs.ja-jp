<properties 
   pageTitle="Log Analytics のデータ ソース | Microsoft Azure"
   description="データ ソースは、Log Analytics がエージェントや接続されている他のソースから収集するデータを定義します。この記事では、Log Analytics でのデータ ソースの扱い方の概念、それらを構成する方法の詳細、および使用可能なさまざまなデータ ソースの概要について説明します。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Log Analytics のデータ ソース

Log Analytics は、OMS ワークスペース内の接続されたソースからデータを収集して OMS リポジトリに格納します。Log Analytics が収集するデータは、構成するデータ ソースによって定義されます。OMS リポジトリ内のデータは、一連のレコードとして保存されます。データ ソースはそれぞれ異なるタイプのレコードを作成し、各レコード タイプは独自のプロパティ セットを持っています。

![Log Analytics のデータ収集](./media/log-analytics-data-sources/overview.png)

データ ソースは OMS ソリューションとは異なります。OMS ソリューションも接続されたソースからデータを収集し、OMS リポジトリ内にレコードを作成しますが、ソリューションはソリューション ギャラリーからワークスペースに追加することができ、通常、OMS ポータルに追加の分析ツールを提供します。

## データ ソースの概要

次の表に、現在 Log Analytics で使用できるデータ ソースを示します。各データ ソースのリンクをクリックすると、それぞれのデータ ソースについて詳しく説明する記事に移動します。

| データ ソース | イベントの種類 | 説明 |
|:--|:--|:--|
| [カスタム ログ](log-analytics-data-sources-custom-logs.md) | \<LogName\>_CL | ログ情報を含む Windows テキスト ファイルまたは Linux エージェント。 |
| [Windows イベント ログ](log-analytics-data-sources-windows-events.md) | Event | Windows コンピューターのイベント ログから収集されたイベント。 |
| [Windows パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) | Perf | Windows コンピューターから収集されたパフォーマンス カウンター。 |
| [Linux パフォーマンス カウンター](log-analytics-data-sources-performance-counters.md) | Perf | Linux コンピューターから収集されたパフォーマンス カウンター。 |
| [IIS ログ](log-analytics-data-sources-iis-logs.md) | W3CIISLog | W3C 形式の Internet Information Services ログ。 |
| Syslog | Syslog | Windows または Linux コンピューターの Syslog イベント。 |

## データ ソースの構成

Log Analytics の **[設定]** の **[データ]** メニューからデータ ソースを構成します。構成はすべて、OMS ワークスペース内の接続されているすべてのソースに配信されます。現時点では、この構成からエージェントを除外することはできません。

![Windows イベントの構成](./media/log-analytics-data-sources/configure-events.png)

2. OMS コンソールで、**[設定]** タイルを選択します。
3. **[データ]** を選択します。
4. 構成するデータ ソースをクリックします。
5. 構成の詳細については、上記の表のデータ ソース名をクリックして、リンク先のドキュメントを参照してください。

## データ収集

データ ソースの構成は、数分以内に OMS に直接接続されたエージェントに配信されます。指定されたデータがエージェントから収集され、各データ ソースに固有の間隔で Log Analytics に直接配信されます。詳しくは、各データ ソースのドキュメントを参照してください。

接続された管理グループ内の System Center Operations Manager (SCOM) エージェントの場合、データ ソースの構成は管理パックに変換され、既定で 5 分ごとに管理グループに配信されます。このエージェントは、他のエージェントと同様に、管理パックをダウンロードして指定されたデータを収集し、管理サーバーに送信します。データは管理サーバーから OMS に転送されます。どのデータ ソースでも、エージェントが直接 OMS と通信することはありません。SCOM および OMS と接続し、構成の配信頻度を変更する方法の詳細については、「[Configure Integration with System Center Operations Manager](log-analytics-om-agents.md)」 (System Center Operations Manager との統合の構成) を参照してください。

## Log Analytics のレコード

Log Analytics によって収集されたデータはすべて、レコードとして OMS リポジトリに保存されます。さまざまなデータ ソースから収集されたレコードは、独自のプロパティ セットを持ち、**Type** プロパティによって識別されます。各レコードの種類の詳細については、各データ ソースのドキュメントおよびソリューションを参照してください。


## 次のステップ

- Log Analytics に機能を追加し、OMS リポジトリにデータを収集する[ソリューション](log-analytics-add-solutions.md)について学習します。
- [ログ検索](log-analytics-log-searches.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。  
- データ ソースやソリューションから収集された重要なデータについて事前に通知するアラートを構成します。

<!---HONumber=AcomDC_0504_2016-->
