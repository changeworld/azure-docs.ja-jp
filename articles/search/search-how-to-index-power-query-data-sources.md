---
title: Power Query コネクタを使用してデータのインデックスを付ける (プレビュー)
titleSuffix: Azure Cognitive Search
description: Power Query コネクタを使用して、さまざまなデータ ソースからデータをインポートします。
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 5ff733cca02d8c4ca9d3014296cffd8db87c3c3a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892819"
---
# <a name="index-data-using-power-query-connectors-preview"></a>Power Query コネクタを使用してデータのインデックスを付ける (プレビュー)

> [!IMPORTANT] 
> Power Query コネクタのサポートは現在、**限定的なパブリック プレビュー** 段階です。 [サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)して、アクセスをリクエストしてください。

インデックス作成のためにインデクサーを使用して外部データ ソースをクロールする場合は、Azure Cognitive Search でデータ ソース接続に [Power Query](/power-query/power-query-what-is-power-query) コネクタを使用できるようになりました。

Power Query コネクタは、より広範なデータ ソース (他のクラウド プロバイダー上のものを含む) にアクセスできます。 このプレビューでサポートされている新しいデータ ソースは次のとおりです。

+ Amazon Redshift
+ Elasticsearch
+ PostgreSQL
+ Salesforce オブジェクト
+ Salesforce レポート
+ Smartsheet
+ Snowflake

この記事では、Power Query コネクタを使用してインデクサーを設定するための Azure portal ベースのアプローチを示します。 現時点では、SDK のサポートはありません。

> [!NOTE]
> プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="supported-functionality"></a>サポートされる機能
Power Query コネクタは、インデクサーで使用されます。 Azure Cognitive Search のインデクサーは、検索可能なデータとメタデータを外部データ ソースから抽出し、インデックスとデータ ソース間のフィールド対フィールドのマッピングに基づいてインデックスを作成するクローラーです。 この方法は、インデックスにデータを追加するコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。 インデクサーは、ユーザーが独自のクローラーまたはプッシュ モデルを記述することなく、データ ソースからコンテンツのインデックスを付けるための便利な方法を提供します。

Power Query データ ソースを参照するインデクサーは、他のインデクサーでサポートされるスキルセット、スケジュール、高基準値変更検出ロジック、およびほとんどのパラメーターに対して同じレベルのサポートを提供します。

## <a name="prerequisites"></a>前提条件
サポートされているデータ ソースのいずれかからデータのプルを開始する前に、すべてのリソースが設定されていることを確認する必要があります。
+ Azure Cognitive Search サービス
    + [サポートされているリージョン](search-how-to-index-power-query-data-sources.md#regional-availability)に設定されている Azure Cognitive Search サービス。
    + Azure Cognitive Search チームがプレビューに対してお客様の検索サービスを有効にしていることを確認します。 [こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)に入力することで、プレビューにサインアップできます。 
+ Azure Blob Storage アカウント
    + プレビューでは、データの仲介役として使用する Blob Storage アカウントが必要です。 データはデータ ソースから Blob Storage へ、その後インデックスに流れます。 この要件は、初期の限定的なプレビューにのみ存在します。

## <a name="getting-started-using-the-azure-portal"></a>Azure portal を使用して作業を開始する
Azure portal は、Power Query コネクタのサポートを提供しています。 コンテナー内のデータをサンプリングしてメタデータを解析することにより、Azure Cognitive Search のデータのインポートウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

 次のビデオでは、Azure Cognitive Search で Power Query コネクタを設定する方法を紹介します。
 
> [!VIDEO https://www.youtube.com/embed/uy-l4xFX1EE]

### <a name="step-1--prepare-source-data"></a>手順 1 - ソース データを準備する
データ ソースにデータが含まれていることを確認します。 [データのインポート] ウィザードでは、メタデータが読み取られ、インデックス スキーマを推論するためにデータのサンプリングが実行されますが、データ ソースからデータの読み込みも行われます。 データがない場合は、ウィザードが停止し、エラーが返されます。 

### <a name="step-2--start-import-data-wizard"></a>手順 2 - [データのインポート] ウィザードを開始する
お客様がプレビューに関して承認された後、Azure Cognitive Search チームは、機能フラグを使用する Azure portal リンクをお客様に提供します。これにより、Power Query コネクタにアクセスできます。 このページを開き、Azure Cognitive Search サービス ページのコマンド バーから **[データのインポート]** を選択して、ウィザードを開始します。

:::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="[データのインポート] コマンドのスクリーンショット" border="true":::

### <a name="step-3--select-your-data-source"></a>手順 3 – データ ソースを選択する
このプレビューを使用してデータをプルできるデータ ソースがいくつかあります。 Power Query を使用するすべてのデータ ソースには、そのタイルに "Powered By Power Query" と記載されています。 データ ソースを選択します。 
 
![データ ソースを選択する](./media/search-power-query-connectors/power-query-import-data.png "データ ソースを選択する")

データ ソースを選択したら、 **[Next: Configure your data]\(次へ: データの構成\)** を選択して、次のセクションに移動します。

### <a name="step-4--configure-your-data"></a>手順 4 – データを構成する
データ ソースを選択したら、接続を構成します。 各データ ソースには、異なる情報が必要です。 いくつかのデータ ソースについては、Power Query ドキュメントに、データに接続する方法に関する追加の詳細が記載されています。 

+ [PostgreSQL](/power-query/connectors/postgresql)
+ [Salesforce オブジェクト](/power-query/connectors/salesforceobjects)
+ [Salesforce レポート](/power-query/connectors/salesforcereports)

接続の資格情報を指定したら、 **[次へ]** を選択します。

### <a name="step-5--select-your-data"></a>手順 5 – データを選択する
インポート ウィザードでは、データ ソースで使用できるさまざまなテーブルがプレビューされます。 この手順では、インデックスにインポートするデータを含む 1 つのテーブルを確認します。
 
![データをプレビューする](./media/search-power-query-connectors/power-query-preview-data.png "データをプレビューする")

テーブルを選択したら、 **[次へ]** を選択します。

### <a name="step-6--transform-your-data-optional"></a>手順 6 – データを変換する (省略可能)
Power Query コネクタには、データを操作できる充実した UI エクスペリエンスが用意されているため、インデックスに適切なデータを送信できます。 列の削除や、行のフィルター処理などを実行できます。 

データを Azure Cognitive Search にインポートする前に変換する必要はありません。

![データを変換する](./media/search-power-query-connectors/power-query-transform-your-data.png "データを変換する") 

Power Query を使用したデータ変換の詳細については、[Power BI Desktop での Power Query の使用](/power-query/power-query-quickstart-using-power-bi)に関するページを参照してください。 

データの変換が完了したら、 **[次へ]** を選択します。

### <a name="step-7--add-azure-blob-storage"></a>手順 7 – Azure Blob Storage を追加する
Power Query コネクタ プレビューでは現在、BLOB ストレージ アカウントを指定する必要があります。 この手順は、初期の限定的なプレビューにのみ存在します。 この BLOB ストレージ アカウントは、データ ソースから Azure Cognitive Search インデックスに移動するデータの一時ストレージとして機能します。

フル アクセス ストレージ アカウントの接続文字列を指定することをお勧めします。 
```
{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }
```

この接続文字列は、ストレージ アカウント ブレードに移動し、[設定]、[キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定]、[アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。

データ ソース名と接続文字列を指定した後、[Next: Add cognitive skills (Optional)]\(次へ: コグニティブ スキルの追加 (省略可能)\) を選択します。 

### <a name="step-8--add-cognitive-skills-optional"></a>手順 8 – コグニティブ スキルを追加する (省略可能)
[AI エンリッチメント](cognitive-search-concept-intro.md)はインデクサーの拡張機能であり、コンテンツをより検索しやすくするために使用できます。

これは、このプレビューの省略可能な手順です。 完了したら、 **[Next: Customize target index]\(次へ: ターゲット インデックスのカスタマイズ\)** を選択します。

### <a name="step-9--customize-target-index"></a>手順 9 – ターゲット インデックスをカスタマイズする
インデックス ページには、フィールドが、データ型とインデックスの属性を設定するための一連のチェック ボックスと共に一覧表示されます。 このウィザードで、メタデータに基づいてソース データをサンプリングすることで、フィールドの一覧を生成できます。

属性列の上部にあるチェック ボックスをクリックすることで、属性を一括選択できます。 クライアント アプリに返してフル検索処理の対象にする必要があるすべてのフィールドで、 [取得可能] と [検索可能] を選択します。 整数はフルテキスト検索もあいまい検索もできないことに注意してください (数値は逐語的に評価され、多くの場合フィルターで役立ちます)。

詳細については、インデックス属性と言語アナライザーの説明を参照してください。

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されるため、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドのほとんどのプロパティは編集できなくなります。

![インデックスを作成する](./media/search-power-query-connectors/power-query-index.png "インデックスを作成する")

完了したら、 **[Next: Create an Indexer]\(次へ: インデクサーの作成\)** を選択します。

### <a name="step-10--create-an-indexer"></a>手順 10 - インデクサーを作成する
最後の手順では、インデクサーを作成します。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

[データのインポート] ウィザードで出力されるインデクサーにより、データ ソースがクロールされ、選択したデータが Azure Cognitive Search のインデックスにインポートされます。

インデクサーを作成するときに、必要に応じて、スケジュールに基づいたインデクサーの実行と、変更検出の追加を選択することができます。 変更検出を追加するには、'高基準値' 列を指定します。

![インデクサーを作成する](./media/search-power-query-connectors/power-query-indexer-configuration.png "インデクサーを作成する")

このページの入力が完了したら、 **[送信]** を選択します。

## <a name="high-water-mark-change-detection-policy"></a>高基準値変更検出ポリシー
この変更検出ポリシーは、行が最後に更新されたときのバージョンまたは時刻を取得する "高基準" 列に依存します。

### <a name="requirements"></a>必要条件
+ すべての挿入は列の値を指定します。
+ 項目を更新すると、列の値も変更されます。
+ この列の値は挿入または更新のたびに増加します。

## <a name="unsupported-column-names"></a>サポートされていない列名
Azure Cognitive Search インデックス内のフィールド名は、特定の要件を満たす必要があります。 これらの要件の 1 つは、"/" などの一部の文字が使用できないということです。 データベース内の列名がこれらの要件を満たしていない場合、インデックス スキーマ検出で列が有効なフィールド名として認識されず、その列がインデックス用に提案されるフィールドとして一覧表示されません。 通常、[フィールド マッピング](search-indexer-field-mappings.md)を使用すると、この問題が解決しますが、フィールド マッピングはポータルでサポートされていません。

サポートされていないフィールド名を持つテーブル内の列からのコンテンツにインデックスを付けるには、データのインポート プロセスの "データを変換する" フェーズ中に列の名前を変更します。 たとえば、"Billing code/Zip code" という列の名前を "zipcode" に変更できます。 列の名前を変更すると、インデックス スキーマ検出によってそれが有効なフィールド名として認識され、インデックス定義に提案として追加されます。

## <a name="regional-availability"></a>リージョン別の提供状況
プレビューは、次のリージョンで検索サービスを利用しているお客様のみがご利用いただけます。
+ 米国中部
+ 米国東部
+ 米国東部 2
+ 米国中北部
+ 北ヨーロッパ
+ 米国中南部
+ 米国中西部
+ 西ヨーロッパ
+ 米国西部
+ 米国西部 2

## <a name="preview-limitations"></a>プレビューの制限事項
このプレビューにはすばらしい機能が多数ありますが、いくつかの制限があります。 このセクションでは、プレビューの現在のバージョンに固有の制限事項について説明します。
+ このバージョンのプレビューでは、データ ソースからバイナリ データをプルする機能はサポートされていません。 
+ 現時点では、[デバッグ セッション](cognitive-search-debug-session.md)はサポートされていません。

## <a name="next-steps"></a>次のステップ
Power Query コネクタを使用して新しいデータ ソースからデータをプルする方法について説明しました。 インデクサーの詳細については、「[Azure Cognitive Search のインデクサー](search-indexer-overview.md)」を参照してください。