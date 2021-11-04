---
title: Azure portal を使用して GraphQL API をインポートする | Microsoft Docs
titleSuffix: ''
description: API Management での GraphQL のサポート、GraphQL API の追加、GraphQL の制限事項について説明します。
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c1541e9de6d2f9346826e256fcb854b0ca7cdac3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091727"
---
# <a name="import-a-graphql-api-preview"></a>GraphQL API をインポートする (プレビュー)

GraphQL は、API 用のオープンソースの業界標準クエリ言語です。 GraphQL API は、リソースに対するアクションを中心に設計されたエンドポイントベース (または REST スタイル) の API とは異なり、より広範な一連のユース ケースをサポートし、データ型、スキーマ、クエリに重点を置いています。

API Management では、GraphQL API の発行に伴うセキュリティ、認証、承認の課題に取り組んでいます。 API Managementを使用して GraphQL API を公開すると、次のことが可能になります。
* Azure portal を使用して GraphQL サービスを API として追加する。  
* GraphQL 固有の攻撃をセキュリティで保護するために、既存のアクセス制御}ポリシーと[新しいポリシー](graphql-validation-policies.md)の両方を適用することで、GraphQL API をセキュリティで保護する。 
* スキーマを調べ、Azure と開発者ポータルで GraphQL API に対してテスト クエリを実行する。 

[!INCLUDE [preview-callout-graphql.md](./includes/preview/preview-callout-graphql.md)]

この記事では、次のことを行います。
> [!div class="checklist"]
> * GraphQL API を使用する利点の詳細を確認します。
> * API Management インスタンスに GraphQL API を追加します。
> * GraphQL API をテストします。
> * API Management での GraphQL API の制限事項について確認します。

## <a name="prerequisites"></a>前提条件

- 既存の API Management インスタンスがある。 [まだない場合は、作成してください](get-started-create-service-instance.md)。
- GraphQL API。 

## <a name="add-a-graphql-api"></a>GraphQL API を追加する

1. API Management インスタンスに移動します。
1. サイド ナビゲーション メニューの **[API]** セクションで、 **[API]** を選択します。
1. **[Define a new API]\(新しい API の定義\)** で、**GraphQL** アイコンを選びます。

    :::image type="content" source="media/graphql-api/import-graphql-api.png" alt-text="API の一覧から GraphQL アイコンを選ぶ。":::

1. ダイアログ ボックスで **[完全]** を選択し、必要なフォーム フィールドを入力します。

    :::image type="content" source="media/graphql-api/create-from-graphql-schema.png" alt-text="GraphQL を作成するためのフィールドのデモンストレーション":::

    | フィールド | 説明 |
    |----------------|-------|
    | 表示名 | GraphQL API を表示するときに使用する名前。 |
    | 名前 | GraphQL API の未加工の名前。 表示名を入力すると自動的に設定されます。 |
    | GraphQL API エンドポイント | ベース URL と GraphQL API のエンドポイント名。 <br /> たとえば、「 *https://example.com/your-GraphQL-name* 」のように入力します。 一般的な ["Star Wars" GraphQL エンドポイント](https://swapi-graphql.netlify.app/.netlify/functions/index)をデモとして使用することもできます。 |
    | スキーマ ファイルのアップロード | スキーマ ファイルを参照してアップロードする場合に選択します。 |
    | 説明 | API の説明を追加します。 |
    | URL スキーム | [HTTP]、[HTTPS]、または [両方] を選択します。 既定の選択は *両方* です。 |
    | API URL サフィックス| この API Management インスタンスでこの特定の API を識別するための URL サフィックスを追加します。 この API Management インスタンス内で一意である必要があります。 |
    | ベース URL | API ベース URL を表示する編集できないフィールド |
    | タグ | GraphQL API を新規または既存のタグに関連付けます。 |
    | 製品 | GraphQL API を製品に関連付けて発行します。 |
    | ゲートウェイ | GraphQL API を既存のゲートウェイに関連付けます。 既定のゲートウェイの選択: *マネージド*。 |
    | この API をバージョン管理しますか? | GraphQL API をバージョン管理する場合に選択します。 |
 
1. **Create** をクリックしてください。

## <a name="test-your-graphql-api"></a>GraphQL API をテストする

1. API Management インスタンスに移動します。
1. サイド ナビゲーション メニューの **[API]** セクションで、 **[API]** を選択します。
1. **[すべての API]** の下で、使用する GraphQL API を選択します。
1. **[テスト]** タブを選択して、テスト コンソールにアクセスします。 
1. **[ヘッダー]** で、次のようにします。
    1. **[名前]** ドロップダウン メニューからヘッダーを選びます。
    1. **[値]** フィールドに値を入力します。
    1. ヘッダーをさらに追加するには、 **[+ ヘッダーの追加]** を選びます。
    1. ヘッダーを削除するには、**ごみ箱アイコン** を使用します。
1. GraphQL API に製品を追加した場合は、 **[Apply product scope]\(製品スコープの適用\)** で製品スコープを適用します。
1. **[クエリ エディター]** で、以下のいずれかを行います。
    1. サイド メニューの一覧から、少なくとも 1 つのフィールドまたはサブフィールドを選択します。 選択したフィールドとサブフィールドがクエリ エディターに表示されます。
    1. クエリ エディターで入力を開始して、クエリを作成します。
    
        :::image type="content" source="media/graphql-api/test-graphql-query.png" alt-text="クエリ エディターへのフィールドの追加のデモ":::

1. **[クエリ変数]** で、{2}同じクエリまたは変更を再利用し、異なる値を渡す変数を追加します。
1. **[送信]** をクリックします。
1. **[応答]** を表示します。

    :::image type="content" source="media/graphql-api/graphql-query-response.png" alt-text="テスト クエリの応答を表示する":::

1. 前の手順を繰り返して、さまざまなペイロードをテストします。
1. テストが完了したら、テスト コンソールを終了します。

## <a name="limitations"></a>制限事項

* GraphQL のパス スルーのみがサポートされています。 
* API Management の 1 つの GraphQL API は、1 つの GraphQL バックエンド エンドポイントにのみ対応します。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
