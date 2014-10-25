<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# アーキテクチャ

Azure で一般的な設計パターンを実装する方法について説明します。

### Azure のシンボル/アイコン セット

[Azure のシンボル/アイコン セットをダウンロード][Azure のシンボル/アイコン セットをダウンロード]して、Azure を説明 (または使用) するための技術資料 (アーキテクチャ図、トレーニング資料、プレゼンテーション、データシート、インフォグラフィックス、ホワイト ペーパーなど) を作成してください。シンボルは、PPT、Visio、または PNG 形式でダウンロードできます。ダウンロード中にフィードバックを送付する手順も示していますので、ぜひご感想をお知らせください。

![Azure のシンボル/アイコン セット][Azure のシンボル/アイコン セット]

## 設計パターン

### [競合コンシューマー][競合コンシューマー]

![競合コンシューマー][1]

複数のコンシューマーが、同じメッセージング チャネルで受信したメッセージを同時に処理できるようにします。システムでこのパターンを使用することで、複数のメッセージを同時に処理して、スループットを最適化し、スケーラビリティと可用性を向上させ、ワークロードのバランスを取ることができます。

### [コマンドとクエリの責務分離][コマンドとクエリの責務分離]

![コマンドとクエリの責務分離][2]

別のインターフェイスを使用することで、データを更新する操作からデータを読み取る操作を分離します。このパターンにより、パフォーマンス、スケーラビリティ、およびセキュリティが最大化され、より高い柔軟性で時の経過と共にシステムを進展させることができ、更新コマンドによってドメイン レベルでマージの競合が発生することを防止できます。

### [リーダー選定][リーダー選定]

![リーダー選定][3]

他のインスタンスの管理を担当するリーダーとして 1 つのインスタンスを選定することで、分散アプリケーションで協調するタスク インスタンスのコレクションによって実行されるアクションを調整します。このパターンを使用すると、タスク インスタンスが互いに競合したり、共有リソースの競合が発生したり、他のタスク インスタンスが実行している作業を妨げたりすることを防止できます。

### [パイプとフィルター][パイプとフィルター]

![パイプとフィルター][4]

複雑な処理を実行するタスクを、再利用できる一連の要素に分解します。このパターンを使用すると、処理を実行する複数のタスク要素を別々に展開および拡張することにより、パフォーマンス、スケーラビリティ、および再利用性を向上させることができます。

### [バレット キー][バレット キー]

![バレット キー][5]

クライアントに特定のリソースまたはサービスへの制限付き直接アクセスを提供するトークンまたはキーを使用して、アプリケーション コードからのデータ転送処理の負荷を軽減します。このパターンは、クラウドでホストされるストレージ システムまたはキューを使用するアプリケーションで特に有用です。これを使用すると、コストを最小限に抑え、スケーラビリティとパフォーマンスを最大化することができます。

### その他のガイダンス

Azure のその他の一般的な設計パターンについては、「[Cloud Design Patterns (クラウドの設計パターン)][Cloud Design Patterns (クラウドの設計パターン)]」を参照してください。

  [Azure のシンボル/アイコン セットをダウンロード]: http://www.microsoft.com/ja-jp/download/details.aspx?id=41937
  [Azure のシンボル/アイコン セット]: ./media/architecture-overview/AzureSymbols.png
  [競合コンシューマー]: http://msdn.microsoft.com/ja-jp/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [コマンドとクエリの責務分離]: http://msdn.microsoft.com/ja-jp/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [リーダー選定]: http://msdn.microsoft.com/ja-jp/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [パイプとフィルター]: http://msdn.microsoft.com/ja-jp/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [バレット キー]: http://msdn.microsoft.com/ja-jp/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [Cloud Design Patterns (クラウドの設計パターン)]: http://msdn.microsoft.com/ja-jp/library/dn568099.aspx
