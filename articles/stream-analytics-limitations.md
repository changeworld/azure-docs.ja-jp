<properties title="Azure Stream Analytics limitations in the preview release" pageTitle=" Stream Analytics プレビュー リリースの制限事項 | Azure" description="Azure Stream Analytics ジョブのパブリック プレビュー リリースにおける制限事項について説明します。" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

#Azure Stream Analytics プレビューの制限事項と既知の問題

このドキュメントでは、[Azure Stream Analytics][stream.analytics.documentation] のプレビュー リリースに関する制限事項と既知の問題について説明します。これらの制限事項は、ほとんどの場合、お客様から早期のフィードバックを得ることを目的としているか、現行の容量の制約に基づいたものです。 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##この記事の内容
+ [制限事項](#limitations) 
+ [リリース ノートと既知の問題](#knownissues)
+ [次のステップ]

##<a name="limitations"></a> 制限事項

###リージョン別の提供状況
プレビュー リリースでは、米国中部リージョンと西ヨーロッパ リージョンでのみ Stream Analytics のジョブをプロビジョニングできます。

###スケール 
**ストリーミング ユニットのクォータ**

現行の容量制約に基づき、各サブスクリプションに対してリージョン 1 つにつき 12 個のストリーミング ユニット クォータが適用されます。詳細については、「[Stream Analytics ジョブのスケーリング | Azure][stream.analytics.scale.jobs]」を参照してください。ビジネス ニーズ上、この制限の緩和が必要と思われる場合は、[Microsoft サポート][microsoft.support]までお知らせください。一般提供の制約の範囲内でご要望に添えるようできる限り配慮させていただきます。 

**ストリーミング ユニットの使用量**

このプレビュー リリースでは、ジョブに提供されるストリーミング ユニットの数が、選択した数や課金されている数よりも多くなる場合があります。また、ストリーミング ユニットには調整のための削減が実施されないため、コンピューティング リソースの可用性によっては、保証されたパフォーマンスよりも実際のパフォーマンスが高くなることがあります。

**パーティション キー**

PARTITION BY を使用してクエリをスケール アウトする場合は、パーティション分割の基準となるフィールドを PartitionId にする必要があります。他のユーザー定義フィールドに基づくパーティション分割は、今後のリリースで有効になる予定です。
ジョブのスケーリングの詳細については、「[Stream Analytics ジョブのスケーリング | Azure][stream.analytics.scale.jobs]」を参照してください。

###入力



**文字エンコード**

CSV および JSON が入力ソースの場合、サポートされるエンコード形式は UTF-8 のみです。


###クエリの複雑さ
1 つの Stream Analytics ジョブ クエリ定義内で使用できる集計関数は、最大 7 つです。

###ライフサイクル管理

**ジョブのアップグレード**

現時点では、Stream Analytics は、実行中のジョブの定義または構成に対するライブ編集をサポートしていません。実行中のジョブに対する入力、出力、クエリ、スケール、または構成を変更するには、先にジョブを停止する必要があります。

**ジョブの停止と再開**

ジョブを停止しても、ジョブの進行状況に関する状態は保持されません。そのため、現時点では、ジョブを再開したときに前回停止したところから再実行されるように構成することはできません。この制限事項は、今後のリリースで解消される予定です。ジョブの開始と停止に関するベスト プラクティスについては、「[Azure Stream Analytics developer guide (Azure Stream Analytics 開発者ガイド)][stream.analytics.developer.guide]」を参照してください。 

###監視
待機時間など、ジョブの使用状況とパフォーマンスに関連するメトリックの一部は、プレビュー リリースでは使用できません。また、プレビュー リリースではジョブ スループットがイベントの数に関してのみ表示され、イベントのサイズは対象になりません。

##<a name="knownissues"></a>リリース ノートと既知の問題

このセクションでは、Azure Stream Analytics に関する既知の問題について説明します。今後、問題の解決や新たな問題の発生があったり、既知の問題の詳細が判明したりした場合は、このセクションの内容が変更されます。


###管理特権の Event Hub アクセス許可が必要
現時点では、Stream Analytics の共有アクセス ポリシーには、Event Hub の入力ソースと出力ソースに対するアクセス許可の管理を設定する必要があります。

###Azure Storage アカウント構成の遅延
Stream Analytics をリージョンで初めて作成すると、そのリージョンで Stream Analytics ジョブを監視するために、新しいストレージ アカウントを作成するか、既存のアカウントを指定するように求められます。監視の構成に待機時間が生じるせいで、同じリージョンで 30 分以内に別の Stream Analytics のジョブを作成すると、構成したばかりのアカウントが [ストレージ アカウントの監視] ドロップダウン リストに表示される代わりに、2 つ目のストレージ アカウントを指定するように求められます。不要なストレージ アカウントの作成を回避するには、そのリージョンで初めてジョブを作成した後に 30 分待ってから、追加のジョブをプロビジョニングしてください。 

###ジョブで Event Hub の既定のコンシューマー グループが使用される
入力として Event Hub を指定すると、Stream Analytics のジョブは既定のコンシューマー グループを使用して Event Hub からデータを取り込みます。構成を追加せずにこれが実行されると、他の受信者が Event Hub にアクセスできなくなります。複数の受信者が Event Hub にアクセスできるようにするには、追加のコンシューマー グループを構成する必要があります。詳細については、「[Event Hub プログラミング ガイド][azure.event.hubs.developer.guide]」を参照してください。

###入力/出力の追加 - Event Hub 
Event Hub ソースの [入力の追加] および [出力の追加] ダイアログ ボックスの 3 ページ目に、[Event Hub] というドロップダウン リストがあります。このリストには、現在のサブスクリプションの Service Bus 名前空間と、他のサブスクリプションの Event Hub に接続するオプションの両方が含まれています。同じサブスクリプションの Event Hub に接続する場合は、ここでその Service Bus 名前空間を選択します。現在のサブスクリプションに含まれていない Event Hub に接続する場合は、[別のサブスクリプションの Event Hub を使用する] を選択します。  


###同じクエリ ステップを複数回参照することはできない
このプレビュー リリースでは、WITH 句を使用して定義された 1 つのサブクエリ ステップを複数回参照することはできません。この影響を受ける可能性のある一般的なシナリオは、同一ステップの別名を複数使用した自己結合です。この動作を回避するには、サブクエリが同じで名前の異なる 2 つのステップを作成してください。

###サポートされていない型変換を実行すると結果が NULL 値になる
「[Azure Stream Analytics Query Language Reference (Azure Stream Analytics クエリ言語リファレンス)][stream.analytics.query.language.reference]」の「Data Types (データ型)」セクションでサポートされていない型変換をイベント値に実行すると、結果は NULL 値になります。このプレビュー リリースでは、型変換の例外を示すエラーはログに記録されません。 

###メモリ不足の問題
順序が適切でないイベントや複雑なクエリに対する Streaming Analytics ジョブの許容範囲が広く、保持している状態が大量になると、ジョブのメモリ不足が発生し、ジョブが再起動されることがあります。開始と停止の操作は、ジョブの処理ログに記録されます。この動作を回避するには、クエリを複数のパーティションにスケール アウトしてください。今後のリリースでは、影響を受けるジョブを再起動する代わりにパフォーマンスを低下させることで、この制限事項が解消される予定です。

###パーティション分割されたクエリ内の空の Event Hub シャードで何も出力されない
パーティション分割されていないサブクエリが含まれているパーティション分割されたクエリを 2 つ目のステップとして実行する場合、入力の Event Hub パーティションのいずれかが完全に空になっていると、クエリの結果が生成されません。これに対するエラーが、ジョブの処理ログに反映されます。この問題を回避するには、すべての Event Hub パーティションに受信イベントがあるかどうかを常に確認してください。

###SQL Database イベントのボリューム制限
出力ソースとして SQL Database を使用すると、大量の出力データによって Stream Analytics ジョブがタイムアウトになる場合があります。この問題を解決するには、集計または結合演算子を使用して出力の量を削減するか、出力ソースとして BLOB ストレージまたは Event Hub を選択してください。

###大きな BLOB 入力はサポートされていない
BLOB ストレージからサイズが大きなファイルを取得すると、Stream Analytics ジョブがクラッシュする場合があります。この問題を回避するには、各 BLOB のサイズを 10 MB 以下にしてください。

###LEFT OUTER JOIN はサポートされていない
LEFT OUTER JOIN 操作は、Stream Analytics クエリ言語で有効になっていますが、サポートされていません。LEFT OUTER JOIN が含まれたクエリを数分間実行すると、メモリ消費の問題が発生します。期間の短いクエリのテスト以外のシナリオでは、この操作の使用はお勧めしません。この制限は、今後のリリースで解消される予定です。


##<a name="nextsteps"></a>次のステップ

- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Azure Stream Analytics の使用][stream.analytics.get.started]
- [Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale.jobs]
- [Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]
- [Azure Stream Analytics クエリ言語リファレンス][stream.analytics.query.language.reference]
- [Azure Stream Analytics management REST API reference (Azure Stream Analytics の管理 REST API リファレンス)][stream.analytics.rest.api.reference] 

<!--Anchors-->
[制限事項]: #Limitations
[リリース ノートと既知の問題]: #Release-notes-and-known-issues
[次のステップ]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ja-jp/library/azure/dn789972.aspx

[別の azure.microsoft.com ドキュメント トピックへのリンク 1]: ../virtual-machines-windows-tutorial/
[別の azure.microsoft.com ドキュメント トピックへのリンク 2]: ../web-sites-custom-domain-name/
[別の azure.microsoft.com ドキュメント トピックへのリンク 3]: ../storage-whatis-account/
