---
title: "新しいログ検索への Azure Log Analytics のアップグレード | Microsoft Docs"
description: "新しい Log Analytics クエリ言語の準備がほぼ整ったので、パブリック プレビューに参加できます。  この記事では、新しい言語の利点、およびワークスペースを変換する方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 1806b70ba0d34f49abfb954abebff8d29ae61291
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>新しいログ検索への Azure Log Analytics のアップグレード

新しい Log Analytics クエリ言語の準備ができました。これを利用するには、ワークスペースをアップグレードする必要があります。  ワークスペースはご自身でアップグレードすることも、ロールアウト期間中に自動的にアップグレードされるのを待つこともできます。ロールアウトは 10 月末から年末にかけて行われます。  この記事では、新しい言語の利点、およびワークスペースを変換する方法について説明します。  

## <a name="why-the-new-language"></a>新しい言語を使用する理由
移行が面倒であることは承知していますし、好き好んでクエリ言語を変更しているわけではありません。  この変更が Log Analytics のユーザーに大きな価値がもたらす理由は複数あります。

- **シンプルながらもパワフル。** 新しい言語は従来の言語よりもわかりやすく、より自然言語に近いコンストラクトを備え、SQL に類似しています。
- **完全なパイプ処理言語。**  新しい言語は、従来の言語より広範なパイプ処理機能を備えています。  事実上すべての出力を他のコマンドにパイプ処理して、以前より複雑なクエリを作成できます。
- **検索時間フィールドの抽出。**  新しい言語は、従来の言語より高度なランタイム計算フィールドをサポートしています。  拡張フィールドで複雑な計算を使用し、結合、集計などの追加コマンドに対して計算フィールドを使用することができます。
- **高度な結合。**  新しい言語では、従来の言語より高度な結合が用意されています。たとえば、複数フィールドでのテーブルの結合、内部結合や外部結合の使用、拡張フィールドでの結合が可能です。
- **日付/時刻の関数。**  新しい言語は、従来の言語より高度な日付/時刻関数を備えています。
- **スマート分析。**  新しい言語には、データセット内のパターンを評価し、さまざまなデータセットを比較する高度なアルゴリズムが用意されています。
- **高度な分析ポータル。**  高度な分析ポータルでは、複数行でのクエリの編集、追加の視覚化、高度な診断など、Log Analytics ポータルでは使用できない分析機能を使用できます。
- **他のアプリケーションとの一貫性。**  新しい言語と高度な分析ポータルは、既に Application Insights での分析に使用されています。  これを Log Analytics 用に実装することで、Azure サービス全体で一貫性が確保されます。
- **改善された Power BI との統合。** 新しい言語のクエリを Power BI Desktop にエクスポートして、豊富なデータ変換機能を活用できます。
- **その他。** 新しい言語の詳細とチュートリアルについては、「[Azure Log Analytics Query Language (Azure Log Analytics クエリ言語)](https://docs.loganalytics.io)」のサイトを参照してください。


## <a name="when-can-i-upgrade"></a>アップグレードのタイミング
アップグレードは、すべての Azure リージョンでロールアウトされるため、一部のリージョンが他のリージョンよりも早く入手可能になる場合があります。  ワークスペースがアップグレードできるようになると、ワークスペース上部にアップグレードを促すバナーが表示されます。

![アップグレード 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

ワークスペースが自動的にアップグレードされる場合、アップグレードが行われたことを示すバナーが表示されます。このバナーには、問題が発生したかどうかを示す概要情報も示されます。

 ![自動アップグレード](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>アップグレード後の動作
変換された場合、ワークスペースには次のような変更が加えられます。

- 保存したすべての検索、アラート ルール、およびビュー デザイナーで作成したビューが、新しい言語に自動的に変換されます。  ソリューションに含まれる検索は自動的には変換されませんが、検索を開いた時点ですぐに変換されます。  
- [マイ ダッシュボード](log-analytics-dashboards.md)は非推奨となり、今後は[ビュー デザイナー](log-analytics-view-designer.md)と [Azure ダッシュボード](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards.md)で置き換えられます。  マイ ダッシュボードに追加したタイルは引き続き利用できますが、読み取り専用です。
- [Power BI 統合](log-analytics-powerbi.md)は、新しいプロセスで置き換えられます。  作成した既存の Power BI スケジュールは無効になるため、新しいプロセスで置き換える必要があります。
- webhook と Runbook を使用した[アラート アクション](log-analytics-alerts-actions.md)からの応答には新しい形式が採用されているため、これに応じてアラート ルールの更新が必要になる場合があります。
- アップグレードに関する一般的な質問については、[ログ検索の FAQ](log-analytics-log-search-faq.md) に関するページをご覧ください。

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>アップグレードに問題があるかどうかを確認する方法
アップグレードが完了すると、ワークスペースの設定に **[アップグレードの概要]** セクションが表示されます。  アップグレードに関する情報は、このセクションで確認してください。

 ![アップグレードの概要](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>アップグレードを手動で実行する方法
ポータル上部にバナーが表示されたら、ワークスペースをアップグレードできます。  

1.  **[詳細を表示して、アップグレードする]** と表示されているバナーをクリックして、アップグレード プロセスを開始します。

    ![アップグレード 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  アップグレード情報ページでアップグレードに関する追加情報を確認します。

    ![アップグレード 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  **[今すぐアップグレード]** をクリックして、アップグレードを開始します。

    ![アップグレード 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>右上隅にある通知ボックスに状態が表示されます。
    
    ![アップグレード 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  これで終了です。  [ログ検索] ページに移動して、アップグレードされたワークスペースを確認します。

    ![アップグレード 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

問題が発生してアップグレードに失敗した場合は、[ディスカッション フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)に移動して質問を投稿するか、Azure Portal から[サポート要求を作成](../azure-supportability/how-to-create-azure-support-request.md)できます。

## <a name="how-do-i-learn-the-new-language"></a>新しい言語を学習する方法
新しい言語は複数のサービスによって使用されているため、新しい言語に関する[ドキュメントをホストする外部サイト](https://docs.loganalytics.io/)を作成しました。  このサイトには、新しい言語を理解するうえで役立つチュートリアル、サンプル、および完全なリファレンスが含まれています。 新しい言語のチュートリアルは「[Getting Started with Queries (クエリの概要)](https://go.microsoft.com/fwlink/?linkid=856078)」で実行できます。また、[Log Analytics のクエリ言語](https://go.microsoft.com/fwlink/?linkid=856079)に関するページでは言語リファレンスにアクセスできます。  

一連のサンプル データを含め、新しい言語をデモ環境で試したい場合は、[プレイグラウンド環境](https://portal.loganalytics.io/demo#/discover/home)をご覧ください。

ただし、従来の Log Analytics クエリ言語に既に慣れている場合は、アップグレードの一部としてワークスペースに追加される言語コンバーターを使用できます。  従来のクエリを入力して、**[変換]** をクリックするだけで、変換後のバージョンが表示されます。  次に、検索ボタンをクリックして検索を実行するか、変換後のクエリをコピーし、警告ルールなど、他の場所で使用できるように貼り付けます。  レガシ言語の一般的なクエリと直接比較している[チート シート](log-analytics-log-search-transition.md)を確認することもできます。

![言語コンバーター](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>次のステップ
- [新しい言語のチュートリアル](https://go.microsoft.com/fwlink/?linkid=856078)を確認します。
- 新しいクエリ言語で[ログ検索ポータルを使用したチュートリアル](log-analytics-log-search-log-search-portal.md)を実行します。
- 新しい[高度な分析ポータル](https://go.microsoft.com/fwlink/?linkid=856587)の概要を確認します。
