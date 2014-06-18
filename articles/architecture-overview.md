<properties linkid="" urlDisplayName="" pageTitle="アーキテクチャ" metaKeywords="" description="一般的な設計パターンを対象とするアーキテクチャの概要" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="アーキテクチャの概要" authors="waltpo" solutions="" manager="bjsmith" editor="mattshel" />

#アーキテクチャ
Azure で一般的な設計パターンを実装する方法について説明します。

###Azure のシンボル/アイコン セット

[Azure のシンボル/アイコン セットをダウンロード](http://www.microsoft.com/ja-jp/download/details.aspx?id=41937)して、Azure を説明 (または使用) するための技術資料 (アーキテクチャ図、トレーニング資料、プレゼンテーション、データシート、インフォグラフィックス、ホワイト ペーパーなど) を作成してください。シンボルは、PPT、Visio、または PNG 形式でダウンロードできます。ダウンロード中にフィードバックを送付する手順も示していますので、ぜひご感想をお知らせください。

![Azure のシンボル/アイコン セット][azure_symbols]

##設計パターン

###[Competing Consumers (競合コンシューマー)](http://msdn.microsoft.com/ja-jp/library/dn568101.aspx)

![競合コンシューマー][competing_consumers]

複数のコンシューマーが、同じメッセージング チャネルで受信したメッセージを同時に処理できるようにします。システムでこのパターンを使用することで、複数のメッセージを同時に処理して、スループットを最適化し、スケーラビリティと可用性を向上させ、ワークロードのバランスを取ることができます。

###[Command and Query Responsibility Segregation (コマンドとクエリの責務分離)](http://msdn.microsoft.com/ja-jp/library/dn568103.aspx)

![コマンドとクエリの責務分離][cqrs]

別のインターフェイスを使用することで、データを更新する操作からデータを読み取る操作を分離します。このパターンにより、パフォーマンス、スケーラビリティ、およびセキュリティが最大化され、より高い柔軟性で時の経過と共にシステムを進展させることができ、更新コマンドによってドメイン レベルでマージの競合が発生することを防止できます。

###[Leader Election (リーダー選定)](http://msdn.microsoft.com/ja-jp/library/dn568104.aspx)

![リーダー選定][leader_election]

他のインスタンスの管理を担当するリーダーとして 1 つのインスタンスを選定することで、分散アプリケーションで協調するタスク インスタンスのコレクションによって実行されるアクションを調整します。このパターンを使用すると、タスク インスタンスが互いに競合したり、共有リソースの競合が発生したり、他のタスク インスタンスが実行している作業を妨げたりすることを防止できます。

###[Pipes and Filters (パイプとフィルター)](http://msdn.microsoft.com/ja-jp/library/dn568100.aspx)

![パイプとフィルター][pipes_and_filters]

複雑な処理を実行するタスクを、再利用できる一連の要素に分解します。このパターンを使用すると、処理を実行する複数のタスク要素を別々に展開および拡張することにより、パフォーマンス、スケーラビリティ、および再利用性を向上させることができます。

###[Valet Key (バレット キー)](http://msdn.microsoft.com/ja-jp/library/dn568102.aspx)

![バレット キー][valet_key]

クライアントに特定のリソースまたはサービスへの制限付き直接アクセスを提供するトークンまたはキーを使用して、アプリケーション コードからのデータ転送処理の負荷を軽減します。このパターンは、クラウドでホストされるストレージ システムまたはキューを使用するアプリケーションで特に有用です。これを使用すると、コストを最小限に抑え、スケーラビリティとパフォーマンスを最大化することができます。

### その他のガイダンス

Azure のその他の一般的な設計パターンについては、「 [Cloud Design Patterns (クラウドの設計パターン)](http://msdn.microsoft.com/ja-jp/library/dn568099.aspx).」を参照してください。


[competing_consumers]: ./media/architecture-overview/CompetingConsumers.png
[cqrs]: ./media/architecture-overview/CQRS.png
[leader_election]: ./media/architecture-overview/LeaderElection.png
[pipes_and_filters]: ./media/architecture-overview/PipesAndFilters.png
[valet_key]: ./media/architecture-overview/ValetKey.png
[azure_symbols]: ./media/architecture-overview/AzureSymbols.png


