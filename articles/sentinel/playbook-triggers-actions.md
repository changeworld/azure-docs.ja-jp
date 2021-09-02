---
title: Azure Sentinel のプレイブックでトリガーとアクションを使用する | Microsoft Docs
description: Azure Sentinel アラートとインシデント情報へのアクセス権をプレイブックに提供し、その情報を使用して対応アクションを実行する方法についての詳は、以下を参照してください。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 045178a30088ccfd8b76d70d210e29c5a9253ac1
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294373"
---
# <a name="use-triggers-and-actions-in-azure-sentinel-playbooks"></a>Azure Sentinel のプレイブックでトリガーとアクションを使用する

このドキュメントでは、[Logic Apps Azure Sentinel コネクタ](/connectors/azuresentinel/)のトリガーとアクションの種類について、プレイブックが Azure Sentinel とワークスペースのテーブルの情報をやり取りするために使用できることを説明しています。 さらに、必要となる可能性のある特定の種類の Microsoft Azure Sentinel 情報へのアクセス方法も示しています。

このドキュメントは、[Azure Sentinel へのプレイブックの認証](authenticate-playbooks-to-sentinel.md)のガイドと、他のプレイブックのドキュメントである[チュートリアル: Azure Sentinelの自動化ルールでプレイブックを使う](tutorial-respond-threats-playbook.md)と関連しています。 これら 3 つのドキュメントは、互いを前後に参照します。

プレイブックの概要については、[Azure Sentinel でプレイブックを使用して脅威の応答を自動化する](automate-responses-with-playbooks.md)を参照してください。

Azure Sentinel コネクタの完全な仕様については、[Azure Logic Apps のドキュメント](/connectors/azuresentinel/)を参照してください。

## <a name="permissions-required"></a>必要なアクセス許可

| ロールまたはコネクタ コンポーネント | トリガー | 「Get」アクション | インシデントの更新、<br>コメントの追加 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Azure Sentinel [レスポンダー](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Azure Sentinel のアクセス許可](./roles.md)を参照してください。

## <a name="azure-sentinel-triggers-summary"></a>Azure Sentinel トリガーの概要

Azure Sentinel コネクタはさまざまな方法で使用できます。コネクタのコンポーネントは 2 つのフローに分割できますが、それぞれが異なる Azure Sentinel の発生によってトリガされます。

| トリガー | の完全なトリガー名<br>Logic Apps デザイナー | いつ使用するか | 既知の制限事項 
| --------- | ------------ | -------------- | -------------- | 
| **インシデント トリガー** | "Azure Sentinel インシデント作成ルールがトリガーされたとき (プレビュー)" | ほとんどのインシデント自動化シナリオに推奨されます。<br><br>プレイブックは、エンティティやアラートを含むインシデント オブジェクトを受け取ります。 このトリガーを使用すると、プレイブックを **自動化ルール** にアタッチすることができるので、Azure Sentinel でインシデントが作成されたときにトリガーされ、[自動化ルールのすべてのメリット](./automate-incident-handling-with-automation-rules.md)をインシデントに適用できます。 | このトリガーを持つプレイブックは、Azure Sentinel から手動で実行できません。<br><br>このトリガーを使用するプレイブックでは、アラートのグループ化はサポートされていません。つまり、各インシデントで送信された最初のアラートのみを受信します。
| **アラート トリガー** | "Azure Sentinel アラートへの対応がトリガーされたとき" | Azure Sentinel ポータルから手動でアラートを実行する必要があるプレイブックや、アラートに対してインシデントを生成しない **スケジュールされた** 分析ルールの場合に推奨されます。 | このトリガーを使用して、**Microsoft セキュリティ** 分析ルールによって生成されたアラートの応答を自動化することはできません。<br><br>このトリガーを使用するプレイブックは、**自動化ルール** で呼び出すことはできません。 |
|

これら 2 つのフローで使用されるスキーマは同一ではありません。
推奨される方法は、ほとんどのシナリオに適用可能な **Azure Sentinel のインシデント トリガー** フローを使用することです。

### <a name="incident-dynamic-fields"></a>インシデントの動的フィールド

**Azure Sentinel インシデント作成ルールがトリガーされたとき** から受け取った **Incident** オブジェクトには、以下のダイナミック フィールドが含まれています。

- インシデントのプロパティ (「Incident: field name」として表示)

- アラート (配列)

  - インシデントのプロパティ (「Alert: field name」として表示)

    **Alert: \<property name>** のようにアラートのプロパティを選択すると、1 つのインシデントに複数のアラートが含まれることがあるため、*for each* のループが自動的に生成されます。

- エンティティ (すべてのアラートのエンティティの配列)

- ワークスペース情報フィールド (インシデントが作成された Sentinel ワークスペースに適用される)
  - サブスクリプション ID
  - ワークスペース名
  - ワークスペース ID
  - リソース グループ名

## <a name="azure-sentinel-actions-summary"></a>Azure Sentinel アクションの概要

| コンポーネント | いつ使用するか |
| --------- | -------------- |
| **Alert - Get Incident** | プレイブック内でアラート トリガーで始まります。 インシデントのプロパティ取得や、**インシデントの更新**、**インシデントにコメントを追加** のアクションで使用する **インシデントの ARM ID** を取得する場合に便利です。 |
| **インシデントの取得** | 外部ソースから、または Sentinel 以外のトリガーを使用してプレイブックをトリガーする場合。 **インシデント ARM ID** を使用して識別します。 インシデントのプロパティとコメントを取得します。 |
| **インシデントの更新** | インシデントの **Status** 変更 (インシデントを閉じるときなど)、**Owner** の割り当て、タグの追加や削除、**Severity**、**Title**、**Description** の変更などを行うことができます。
| **インシデントにコメントを追加** | 外部ソースから収集された情報でインシデントを強化するため。プレイブックがエンティティに対して行ったアクションを監査するため。インシデントの調査に有用な追加情報を提供するため。 |
| **エンティティ - 取得\<*entity type*\>** | 特定のエンティティタイプ (**IP**、**Account**、**Host**、**URL**、**FileHash**) で動作するプレイブックでは、プレイブック作成時に既知であり、それを解析して固有のフィールドで動作させる必要があります。 |
|

## <a name="work-with-incidents---usage-examples"></a>インシデントの使用 - 使用例

> [!TIP] 
> アクション **インシデントの更新** と **インシデントへのコメントの追加** には、**インシデント ARM ID** が必要です。 <br>
**Alert - Get Incident** アクションを使用して、事前に **インシデント ARM ID** を取得します。

### <a name="update-an-incident"></a>インシデントの更新
-  **インシデントの作成時** にプレイブックがトリガーされる

    ![インシデント トリガーの単純な更新フローの例](media/playbook-triggers-actions/incident-simple-flow.png)

-  **アラートの作成時** にプレイブックがトリガーされる

    ![アラート トリガーの単純なインシデント更新フローの例](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>インシデント情報の使用

インシデントの詳細をメールで送信する基本的なプレイブック:
-  **インシデントの作成時** にプレイブックがトリガーされる

    ![インシデント トリガーの単純な取得フローの例](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  **アラートの作成時** にプレイブックがトリガーされる

    ![アラート トリガーの単純なインシデント取得フローの例](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>インシデントにコメントを追加する

-  **インシデントの作成時** にプレイブックがトリガーされる

    ![インシデント トリガーの単純なコメント追加の例](media/playbook-triggers-actions/incident-comment.png)

-  **アラートの作成時** にプレイブックがトリガーされる

    !["アラート トリガーの単純なコメント追加の例"](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>特定のエンティティ型を使用する

**エンティティ** の動的フィールドは JSON オブジェクトの配列で、それぞれがエンティティを表します。 各エンティティ型には、一意のプロパティに応じて、独自のスキーマがあります。

**"エンティティ - 取得\<entity name>"** アクションを使用すると、次の操作を実行できます。

- 要求された型でエンティティの配列をフィルター処理します。
- この型の特定のフィールドを解析して、次のアクションで動的フィールドとして使用できるようにします。

入力は **エンティティ** 動的フィールドです。

応答は特殊なプロパティが解析されたエンティティの配列であり、*For each* ループで直接使用できます。

次のエンティティの型がサポートされています:

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [Host](/connectors/azuresentinel/#entities---get-hosts)
- [アカウント](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [FileHash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="エンティティ アクションの一覧":::

その他のエンティティ型の場合は、Logic Apps の組み込みアクションを使用して同様の機能を実現できます。

- [**フィルター配列**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)を使用して要求された型でエンティティの配列をフィルター処理します。

- [**Parse JSON** ](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) を使用したアクションで動的フィールドとして使用できるように、この型の特定のフィールドを解析します。

## <a name="work-with-custom-details"></a>カスタム詳細を操作する

**インシデント トリガー** で使用できる **アラートのカスタム詳細** 動的フィールドは、JSON オブジェクトの配列であり、それぞれがアラートのカスタム詳細を表します。 [カスタムの詳細](surface-custom-details-in-alerts.md)は、キーと値のペアで、アラート内のイベントからの情報を表面化し、インシデントの一部として表現、追跡、分析できるようにするものです。

アラートのこのフィールドはカスタマイズ可能であるため、スキーマは、表示されるイベントの型によって異なります。 カスタム詳細フィールドの解析方法を決定するスキーマを生成するために、このイベントのインスタンスからデータを供給する必要があります。

次の例を参照してください。

![分析ルールで定義されたカスタム詳細。](./media/playbook-triggers-actions/custom-details-values.png)

これらのキーと値のペアでは、キー (左側の列) は作成するカスタム フィールドを表し、値 (右側の列) はカスタム フィールドを設定するイベント データのフィールドを表します。

スキーマを生成するには、次の JSON コードを指定できます。 このコードでは、キー名を配列として表示し、値 (値を含む列ではなく、実際の値を示す) を配列の項目として表示しています。

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. **JSON の解析** 組み込みアクションを使用して新しいステップを追加します。 検索フィールドに「json の解析」と入力して検索できます。

1. **[動的なコンテンツ]** ボックスの一覧で、インシデント トリガーの下にある **アラートのカスタム詳細** を選択します。

    ![動的コンテンツから [Alert custom details (カスタム詳細のアラート)] を選択します。](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    これにより、インシデントにアラートの配列が含まれているため、**for each** ループが作成されます。

1. **[サンプルのペイロードを使用してスキーマを生成する]** リンクをクリックします。

    ![[サンプルのペイロードを使用してスキーマを生成する] リンクを選択します。](./media/playbook-triggers-actions/generate-schema-link.png)

1. サンプル ペイロードを提供します。 サンプルのペイロードは、Log Analytics (**Logs** ブレード) でこのアラートの別のインスタンスを探し、カスタム詳細オブジェクト (**Extended Properties** の下) をコピーすることで見つけることができます。 次のスクリーンショットは、上に示した JSON コードを使用しています。

    ![サンプルの JSON ペイロードを入力します。](./media/playbook-triggers-actions/sample-payload.png)

1. カスタム フィールドは、**配列** 型の動的フィールドを使用する準備ができています。 ここで説明したように、配列とその項目は、スキーマと **動的コンテンツ** の下に表示される一覧の両方に表示されます。

    ![スキーマで使用準備ができているフィールド。](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>次のステップ

このアーティクルでは、Azure Sentinel プレイブックでのトリガーとアクションを使用して脅威に対応する方法について詳しく説明しました。 
- Azure Sentinel を使用して[脅威を予防的に検出する方法](hunting.md)をご覧ください。