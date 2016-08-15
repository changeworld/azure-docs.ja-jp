<properties
	pageTitle="Azure CDN でのリアルタイム統計情報 | Microsoft Azure"
	description="リアルタイム統計情報は、クライアントにコンテンツを配信するときの Azure CDN のパフォーマンスに関するリアルタイム データを提供します。"
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Microsoft Azure CDN でのリアルタイム統計情報

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## 概要

この文書では、Microsoft Azure CDN のリアルタイム統計情報について説明します。帯域幅、キャッシュの状態、CDN プロファイルへの同時接続数など、クライアントにコンテンツを配信するときのリアルタイム データは、この機能から得ることができます。本番稼働イベントを含め、サービスの正常性を随時継続的に監視することができます。

次のグラフを使用できます。

* [帯域幅](#bandwidth)
* [状態コード](#status-codes)
* [キャッシュの状態](#cache-statuses)
* [接続](#connections)


## リアルタイム統計にアクセスする

1. [Azure ポータル](https://portal.azure.com)で、CDN プロファイルに移動します。

	![CDN プロファイル ブレード](./media/cdn-real-time-stats/cdn-profile-blade.png)

2. [CDN プロファイル] ブレードで、**[管理]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-real-time-stats/cdn-manage-btn.png)

	CDN 管理ポータルが開きます。

3. **[分析]** タブにマウス ポインターを合わせ、次に **[リアルタイム統計情報]** フライアウトにポインターを合わせます。**[HTTP ラージ オブジェクト]** をクリックします。

	![CDN management portal](./media/cdn-real-time-stats/cdn-premium-portal.png)

	リアルタイム統計グラフが表示されます。
	
それぞれのグラフには、ページの読み込みを起点とし、選択された期間にわたってリアルタイムの統計情報が表示されます。グラフは、数秒おきに自動的に更新されます。**[グラフの更新]** ボタンが存在する場合、そのボタンをクリックするとグラフが消去され、その後、選択したデータだけが表示されます。

## 帯域幅

![Bandwidth graph](./media/cdn-real-time-stats/cdn-bandwidth.png)

**帯域幅**グラフには、指定期間に現在のプラットフォームで使用された帯域幅の使用量が表示されます。グラフの斜線部分は、帯域幅使用量を示します。帯域幅の正確な使用量は、現在のところ、折れ線グラフの下に直接表示されます。

## 状態コード

![Status code graph](./media/cdn-real-time-stats/cdn-status-codes.png)

**[状態コード]** グラフは、選択された期間に特定の HTTP 応答コードが返された頻度を示します。

> [AZURE.TIP]  各 HTTP 状態コード オプションの詳細については、「[Azure CDN HTTP Status Codes (Azure CDN HTTP 状態コード)](https://msdn.microsoft.com/library/mt759238.aspx)」を参照してください。

HTTP 状態コードの一覧は、グラフの上に直接表示されます。この一覧は、折れ線グラフに追加できる各状態コードとその状態コードの現在の毎秒発生回数を示します。既定では、グラフの状態コードごとに 1 つの線が表示されます。ただし、選択すれば、CDN 構成にとって特別な意味のある状態コードのみを監視できます。そのためには、必要な状態コードを確認し、その他すべてのオプションをオフにしたうえで、**[グラフの更新]** をクリックします。

特定の状態コードについて記録されたデータを一時的に非表示にすることもできます。グラフのすぐ下にある凡例から、非表示にする状態コードをクリックします。状態コードはすぐにグラフから消えます。その状態コードをもう一度クリックすると、再び表示されます。

## キャッシュの状態

![Cache Statuses graph](./media/cdn-real-time-stats/cdn-cache-status.png)

**[キャッシュの状態]** グラフは、選択された期間に特定の種類のキャッシュ状態が発生した頻度を示します。

> [AZURE.TIP]  各キャッシュ状態コード オプションの詳細については、「[Azure CDN Cache Status Codes (Azure CDN キャッシュ状態コード)](https://msdn.microsoft.com/library/mt759237.aspx)」を参照してください。

キャッシュの状態コードの一覧は、グラフのすぐ上に表示されます。この一覧は、折れ線グラフに追加できる各状態コードとその状態コードの現在の毎秒発生回数を示します。既定では、グラフの状態コードごとに 1 つの線が表示されます。ただし、選択すれば、CDN 構成にとって特別な意味のある状態コードのみを監視できます。そのためには、必要な状態コードを確認し、その他すべてのオプションをオフにしたうえで、**[グラフの更新]** をクリックします。

特定の状態コードについて記録されたデータを一時的に非表示にすることもできます。グラフのすぐ下にある凡例から、非表示にする状態コードをクリックします。状態コードはすぐにグラフから消えます。その状態コードをもう一度クリックすると、再び表示されます。

## 接続

![Connections graph](./media/cdn-real-time-stats/cdn-connections.png)

このグラフは、エッジ サーバーとの間で確立された接続の数を示しています。資産に対する要求が CDN を通過するたびに接続が確立されます。

## 次のステップ

- [詳細な HTTP レポート](cdn-advanced-http-reports.md)について詳しく調べる
- [使用量パターン](cdn-analyze-usage-patterns.md)の分析

<!---HONumber=AcomDC_0803_2016-->