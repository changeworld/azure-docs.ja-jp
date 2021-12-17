---
title: ビジュアルの作成
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Synapse Analytics でビジュアルの作成機能を使用する方法について説明します
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788078"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory でのビジュアルの作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory および Synapse Analytics ユーザー インターフェイス (UI) エクスペリエンスを使用すると、コードを記述することなくデータ ファクトリまたは Synapse パイプライン用のリソースを視覚的に作成してデプロイできます。 パイプライン キャンバスにアクティビティをドラッグし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。

現在、この UI は Microsoft Edge と Google Chrome でのみサポートされています。

## <a name="authoring-canvas"></a>作成キャンバス

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**作成キャンバス** を開くには、鉛筆アイコンをクリックします。 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="作成キャンバス":::

ここでは、ファクトリを構成するパイプライン、アクティビティ、データセット、データ フローを作成します。 同様に、リンクされたサービス、トリガー、および統合ランタイムは、[[管理]](#management-hub) タブで編集できます。作成キャンバスを使用してパイプラインの構築を開始するには、[コピー アクティビティを使用したデータのコピー](tutorial-copy-data-portal.md)に関するページを参照してください。 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
**作成キャンバス** を開くには、パイプライン/統合アイコンをクリックします。 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="作成キャンバス":::

ここでは、Synapse でパイプラインとアクティビティを作成します。 同様に、パイプラインで使用されるデータ フローは [開発] タブで編集でき、これに関連付けられているリンクされたサービス、トリガー、および統合ランタイムは、[[管理]](#management-hub) タブで編集できます。作成キャンバスを使用してパイプラインの構築を開始するには、[コピー アクティビティを使用したデータのコピー](tutorial-copy-data-portal.md)に関するページを参照してください。 

---

既定のビジュアル作成エクスペリエンスは、サービスと直接連携します。 Azure Repos Git または GitHub 統合もサポートされるため、パイプラインでの作業時にソース管理とコラボレーションを使用できます。 これらの作成エクスペリエンスの違いについて詳しくは、[ソース管理](source-control.md)に関するページを参照してください。

### <a name="properties-pane"></a>プロパティ ペイン

パイプライン、データセット、データ フローなどの最上位レベルのリソースの場合、上位のプロパティは、キャンバスの右側のプロパティ ペインで編集できます。 プロパティ ペインには、名前、説明、注釈、その他の上位プロパティなどのプロパティが含まれています。 パイプライン アクティビティやデータ フロー変換などのサブリソースは、キャンバスの下部にあるパネルを使用して編集されます。 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="プロパティ ペイン":::

プロパティ ペインは、既定ではリソースの作成時にのみ表示されます。 編集するには、キャンバスの右上隅にあるプロパティ ペイン アイコンをクリックします。

### <a name="related-resources"></a>関連資料

プロパティ ペインで、 **[関連項目]** タブを選択すると、選択したリソースに依存しているリソースを確認できます。現在のリソースを参照しているすべてのリソースが、ここに表示されます。

:::image type="content" source="media/author-visually/related-resources.png" alt-text="関連資料":::

たとえば、上の図では、現在選択されているデータセットが 1 つのパイプラインと 2 つのデータ フローによって使用されています。

## <a name="management-hub"></a>管理ハブ

UI の *[管理]* タブによってアクセスされる管理ハブは、サービスのグローバル管理アクションをホストするポータルです。 ここでは、データ ストアと外部コンピューティングへの接続、ソース管理の構成、およびトリガーの設定を管理できます。 詳細については、[管理ハブ](author-management-hub.md)の機能に関するページを参照してください。

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="リンクされたサービスの管理":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="リンクされたサービスの管理":::

---

## <a name="expressions-and-functions"></a>式と関数

静的な値の代わりに式と関数を使用すると、サービス内の多くのプロパティを指定できます。

プロパティ値の式を指定するには、 **[動的なコンテンツの追加]** を選択するか、フィールドにフォーカスがあるときに **Alt + P** キーを押します。

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="動的なコンテンツの追加":::

これにより、**式ビルダー** が開き、サポートされているシステム変数、アクティビティの出力、関数、およびユーザー指定の変数またはパラメーターから式を作成できます。 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="式ビルダー":::

式言語について詳しくは、[式と関数](control-flow-expression-language-functions.md)に関するページを参照してください。

## <a name="provide-feedback"></a>フィードバックの提供

機能に関するコメントを送信するには、またはツールの問題について Microsoft に通知するには、 **[フィードバック]** を選択します。

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="フィードバック":::

## <a name="next-steps"></a>次のステップ

パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
