<properties 
	pageTitle="Stream Analytics プレビュー リリースの制限事項 | Azure" 
	description="Azure Stream Analytics ジョブのパブリック プレビュー リリースにおける制限事項について説明します。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Azure Stream Analytics プレビューの制限事項と既知の問題

このドキュメントでは、[Azure Stream Analytics][stream.analytics.documentation] のプレビュー リリースに関する制限事項と既知の問題について説明します。これらの制限事項は、ほとんどの場合、お客様から早期のフィードバックを得ることを目的としているか、現行の容量の制約に基づいたものです。
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## 制限事項

### リージョン別の提供状況
プレビュー リリースでは、米国中部リージョンと西ヨーロッパ リージョンでのみ Stream Analytics のジョブをプロビジョニングできます。

### スケーリング 
**ストリーミング ユニットのクォータ**

現行の容量制約に基づき、各サブスクリプションに対してリージョン 1 つにつき 12 個のストリーミング ユニット クォータが適用されます。詳細については、「[Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale.jobs]」を参照してください。ビジネス ニーズ上、この制限の緩和が必要と思われる場合は、[Microsoft サポート][microsoft.support]までお知らせください。一般提供の制約の範囲内でご要望に沿えるようできる限り配慮させていただきます。

**ストリーミング ユニットの使用量**

このプレビュー リリースでは、ジョブに提供されるストリーミング ユニットの数が、選択した数や課金されている数よりも多くなる場合があります。また、ストリーミング ユニットには調整のための削減が実施されないため、コンピューティング リソースの可用性によっては、保証されたパフォーマンスよりも実際のパフォーマンスが高くなることがあります。

**パーティション キー**

**Partition By** を使用してクエリをスケール アウトする場合は、パーティション分割の基準となるフィールドを **PartitionId** にする必要があります。他のユーザー定義フィールドに基づくパーティション分割は、今後のリリースで有効になる予定です。ジョブのスケーリングの詳細については、「[Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale.jobs]」を参照してください。

### 入力



**文字エンコード**

CSV および JSON が入力ソースの場合、サポートされるエンコード形式は UTF-8 のみです。


### クエリの複雑さ
1 つの Stream Analytics ジョブ クエリ定義内で使用できる集計関数は、最大 7 つです。

### クエリの数
指定された入力ソースでサポートされるクエリの最大数は 5 つです。


### ライフサイクル管理

**ジョブのアップグレード**

現時点では、Stream Analytics は、実行中のジョブの定義または構成に対するライブ編集をサポートしていません。実行中のジョブに対する入力、出力、クエリ、スケール、または構成を変更するには、先にジョブを停止する必要があります。

**ジョブの停止と再開**

ジョブを停止しても、ジョブの進行状況に関する状態は保持されません。そのため、現時点では、ジョブを再開したときに前回停止したところから再実行されるように構成することはできません。この制限事項は、今後のリリースで解消される予定です。ジョブの開始と停止に関するベスト プラクティスについては、「[Azure Stream Analytics 開発者ガイド][stream.analytics.developer.guide]」を参照してください。

### Monitoring
待機時間など、ジョブの使用状況とパフォーマンスに関連するメトリックの一部は、プレビュー リリースでは使用できません。また、プレビュー リリースではジョブ スループットがイベントの数に関してのみ表示され、イベントのサイズは対象になりません。

## リリース ノートと既知の問題

このセクションでは、Azure Stream Analytics に関する既知の問題について説明します。今後、問題の解決や新たな問題の発生があったり、既知の問題の詳細が判明したりした場合は、このセクションの内容が変更されます。


### 管理特権のイベントハブ アクセス許可が必要
現時点では、Stream Analytics の共有アクセス ポリシーには、イベントハブの入力ソースと出力ターゲットに対するアクセス許可の管理を設定する必要があります。

### Azure Storage アカウント構成の遅延
Stream Analytics ジョブをリージョンで初めて作成すると、そのリージョンで Stream Analytics ジョブを監視するために、新しいストレージ アカウントを作成するか、既存のアカウントを指定するように求められます。監視の構成に待機時間が生じるために、同じリージョンで 30 分以内に別の Stream Analytics のジョブを作成すると、構成したばかりのアカウントが **[監視ストレージ アカウント]** ボックスの一覧に表示される代わりに、2 つ目のストレージ アカウントを指定するように求められます。不要なストレージ アカウントの作成を回避するには、そのリージョンで初めてジョブを作成した後に 30 分待ってから、追加のジョブをプロビジョニングしてください。

### ジョブごとに独自のイベントハブ コンシューマー グループを使用することが必要
Stream Analytics ジョブの各入力は、独自のイベント ハブ コンシューマー グループを持つように構成する必要があります。ジョブに自己結合または複数の出力が含まれる場合、一部の入力は複数の閲覧者によって読み取られる可能性があります。これにより、1 つのコンシューマー グループの総閲覧者数がコンシューマー グループあたり 5 人というイベント ハブの制限を超えます。この場合は、クエリを、追加のイベント ハブ経由でルーティングされる複数のクエリや中間結果に分割する必要があります。イベント ハブごとに 20 個のコンシューマー グループという制限もある点に注意してください。詳細については、「[Event Hub プログラミング ガイド][azure.event.hubs.developer.guide]」を参照してください。

### 入力/出力の追加 - イベント ハブ 
イベント ハブ ソースの **[入力の追加]** および **[出力の追加]** ダイアログ ボックスの 3 ページ目に、**[Event Hub]** というドロップダウン リストがあります。このリストには、現在のサブスクリプションの Service Bus 名前空間と、他のサブスクリプションのイベント ハブに接続するオプションの両方が含まれています。同じサブスクリプションのイベント ハブに接続する場合は、ここでその Service Bus 名前空間を選択します。現在のサブスクリプションに含まれていないイベント ハブに接続する場合は、**[別のサブスクリプションの Event Hub を使用する]** を選択します。


### 同じクエリ ステップを複数回参照することはできない
このプレビュー リリースでは、**WITH** 句を使用して定義された 1 つのサブクエリ ステップを複数回参照することはできません。この影響を受ける可能性のある一般的なシナリオは、同一ステップの別名を複数使用した自己結合です。この動作を回避するには、サブクエリが同じで名前の異なる 2 つのステップを作成してください。

### サポートされていない型変換を実行すると結果が null 値になる
「[Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)][stream.analytics.query.language.reference]」の「Data Types (データ型)」セクションでサポートされていない型変換をイベント値に実行すると、結果は null 値になります。このプレビュー リリースでは、型変換の例外を示すエラーはログに記録されません。

### メモリ不足の問題
順序が適切でないイベントや複雑なクエリに対する Streaming Analytics ジョブの許容範囲が広く、保持している状態が大量になると、ジョブのメモリ不足が発生し、ジョブが再起動されることがあります。開始と停止の操作は、ジョブの操作ログに記録されます。この動作を回避するには、クエリを複数のパーティションにスケール アウトしてください。今後のリリースでは、影響を受けるジョブを再起動する代わりにパフォーマンスを低下させることで、この制限事項が解消される予定です。

### パーティション分割されたクエリ内の空のイベント ハブ シャードで何も出力されない
パーティション分割されていないサブクエリが含まれているパーティション分割されたクエリを 2 つ目のステップとして実行する場合、入力のイベント ハブ パーティションのいずれかが完全に空になっていると、クエリの結果が生成されません。これに対するエラーが、ジョブの処理ログに反映されます。この問題を回避するには、すべてのイベント ハブ パーティションに受信イベントがあるかどうかを常に確認してください。

### SQL Database イベントのボリューム制限
出力ターゲットとして SQL Database を使用すると、大量の出力データによって Stream Analytics ジョブがタイムアウトになる場合があります。この問題を解決するには、集計または結合演算子を使用して出力の量を削減するか、出力ターゲットとして Azure BLOB ストレージまたは Event Hubs を選択してください。

### 大きな BLOB 入力はサポートされていない
BLOB ストレージからサイズが大きなファイルを取得すると、Stream Analytics ジョブがクラッシュする場合があります。この問題を回避するには、各 BLOB のサイズを 10 MB 以下にしてください。

### 列ヘッダーにスペースが含まれていると出力エントリが null になる
Stream Analytics では、列ヘッダーのスペースは除去されません。列名の先頭または末尾にスペースが含まれていると、ジョブ出力が null エントリになります。


## サポートを受ける
その他のサポートについては、「[Azure Stream Analytics forum (Azure Stream Analytics のフォーラム)](stream-analytics-forum.md)」を参照してください。


## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](stream-analytics-query-language-reference.md)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->