<properties
   pageTitle="変更の追跡を使用して違いを特定する"
   description="Microsoft Azure Operational Insights で構成変更の追跡ソリューションを使用して、環境内で発生したソフトウェアおよび Windows サービスの変更を簡単に特定できます"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# 変更の追跡を使用して違いを特定する

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights で構成変更の追跡ソリューションを使用すると、環境内で発生したソフトウェアおよび Windows サービスの変更を簡単に特定することができます。また、構成の変更を特定すると、運用上の問題を特定できます。

ソリューションをインストールして、Operations Manager エージェントおよび Operational Insights の基本構成モジュールを更新します。監視対象サーバーにインストールされているソフトウェアと Windows サービスの変更が読み取られ、データはクラウドのオペレーション インサイト サービスに送信され、処理されます。受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。変更が見つかると、変更があるサーバーが [変更の追跡] ダッシュボードに表示されます。[変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

## 変更の追跡を使用する

Operational Insights で変更の追跡を使用するには、事前にソリューションをインストールする必要があります。ソリューションのインストールについて詳しくは、[ソリューション ギャラリーを使用したソリューションの追加または削除](operational-insights-add-solution.md)に関する記事を参照してください。

インストールした後で、Operational Insights の **[概要]** ページにある **[変更の追跡]** タイルを使用すると、監視対象サーバーの変更の概要を確認できます。

![[変更の追跡] タイルの画像](./media/operational-insights-change-tracking/overview-change-track.png)

インフラストラクチャに対する変更を確認し、変更の詳細を次のカテゴリ別に表示することができます。

- ソフトウェアや Windows サービスに対する構成の種類ごとの変更

- アプリケーションに対するソフトウェアの変更および個々のサーバーに対する更新

- 各アプリケーションのソフトウェア変更の合計数

- 個々のサーバーでの Windows サービスの変更

![[変更の追跡] ダッシュボードの画像](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![[変更の追跡] ダッシュボードの画像](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### 変更の種類別に変更を表示するには

1. **[概要]** ページで、**[変更の追跡]** タイルをクリックします。

2. **[変更の追跡]** ダッシュボードにあるいずれかの変更の種類ブレードで概要情報を確認し、**ログの検索**ページで、詳細情報を表示する変更の 1 つをクリックします。

3. どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。結果を絞り込むファセットを使用してフィルター処理することもできます。

4. ログ検索のすべてのページは、結果の詳細を CSV ファイルに**エクスポート**して、Excel または他のアプリケーションで開いて表示または編集することができます。

<!---HONumber=July15_HO2-->