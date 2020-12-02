---
title: QnA Maker アプリの管理 - QnA Maker
description: QnA Maker では、複数のユーザーがナレッジ ベースを共同利用できます。 QnA Maker には、アクティブ ラーニングを使用してナレッジ ベースの質を向上させる機能があります。 既存の質問の削除や変更は行わずに、レビュー、承認、却下できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 9c042d044f5ceba5a64d6bd7dfefa34bbc69b107
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353206"
---
# <a name="manage-qna-maker-app"></a>QnA Maker アプリの管理

QnA Maker では共同作成者の役割に基づいて共同作成者のアクセスを制限できるため、さまざまな作成者やコンテンツ エディターと共同作業できます。
[QnA Maker コラボレーター認証の概念](../Concepts/role-based-access-control.md)に関する詳細を参照してください。

[アクティブ ラーニング](../Concepts/active-learning-suggestions.md)で代わりの質問を提案することでナレッジ ベースの品質を改善することもできます。 ユーザーの送信が考慮されて、代わりの質問の一覧に提案として表示されます。 それらの提案を代わりの質問として追加するか拒否するかを柔軟に選択できます。

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御を追加する (Azure RBAC)

QnA Maker を使用すると、複数の担当者が同じ QnA Maker リソース内のすべてのナレッジベースで共同作業を行うことができます。 この機能は、[Azure ロール ベースのアクセス制御 (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md) で提供されています。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker リソース レベルでのアクセス

1 つの QnA Maker サービスにある特定のナレッジ ベースの共有はできません。 より詳細なアクセス制御が必要な場合は、さまざまな QnA Maker リソースにわたってナレッジ ベースを配布することを検討した上で、各リソースにロールを追加してください。

## <a name="add-a-role-to-a-resource"></a>リソースにロールを追加する

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker リソースにユーザー アカウントを追加する

次の手順では、コラボレーター ロールを使用しますが、これらの手順を使用して、いずれかの[ロール](../reference-role-based-access-control.md)を追加することができます。

1. [Azure](https://portal.azure.com/) portal にサインインして、使用する QnA Maker リソースに移動します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **[アクセス制御 (IAM)]** タブを開きます。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **[追加]** を選択します。

    ![QnA Maker IAM の [追加]](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 次の一覧からロールを選択します。

    |Role|
    |--|
    |所有者|
    |Contributor|
    |Cognitive Services QnA Maker 閲覧者|
    |Cognitive Services QnA Maker エディター|
    |Cognitive Services ユーザー|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM のロールの追加。":::

1. ユーザーのメール アドレスを入力し、 **[保存]** を押します。

    ![QnA Maker IAM の電子メールの追加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker ナレッジ ベースを表示する

QnA Maker サービスを共有する相手が [QnA Maker ポータル](https://qnamaker.ai)にログインすると、自身のロールに基づいて、そのサービスのすべてのナレッジ ベースを表示できます。

ナレッジ ベースを選択すると、その QnA Maker リソースでの現在のロールがナレッジベース名の横に表示されます。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="編集モードのナレッジ ベースのスクリーンショット。Web ページの左上隅にあるナレッジ ベース名の横に、かっこで囲まれたロール名が表示されています。":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>アクティブ ラーニングを使用するためにランタイム バージョンをアップグレードする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

アクティブ ラーニングは、ランタイム バージョン 4.4.0 以上でサポートされています。 ナレッジ ベースが以前のバージョンで作成された場合は、この機能を使用するために[ランタイムをアップグレード](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) では、ランタイムが QnA Maker サービス自体によってホストされるため、ランタイムを手動でアップグレードする必要がありません。

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>代わりの質問のためにアクティブ ラーニングを有効にする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

アクティブ ラーニングは、既定では無効になっています。 これを有効にして、提案された質問を表示します。 アクティブ ラーニングを有効にした後は、クライアント アプリから QnA Maker に情報を送信する必要があります。 詳細については、「[ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)」を参照してください。

1. **[発行]** を選択してナレッジ ベースを発行します。 アクティブ ラーニング クエリは、GenerateAnswer API 予測エンドポイントからのみ収集されます。 QnA Maker ポータルの [テスト] ウィンドウへのクエリは、アクティブ ラーニングには影響しません。

1. QnA Maker ポータルでアクティブ ラーニングを有効にするには、右上隅に移動して自分の **名前** を選択し、[**Service settings\(サービス設定\)**](https://www.qnamaker.ai/UserSettings) に移動します。

    ![[Service settings]\(サービス設定\) ページで、アクティブ ラーニングの提案された代わりの質問を有効にします。 右上のメニューで自分のユーザー名を選択し、[Service Settings]\(サービス設定\) を選択します。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker サービスを見つけて **[Active Learning] (アクティブ ラーニング)** を切り替えます。

    > [!div class="mx-imgBorder"]
    > [![[Service settings]\(サービス設定\) ページで、アクティブ ラーニング機能をオンに切り替えます。機能を切り替えられないときは、サービスをアップグレードしなければならない場合があります。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > 上の画像の具体的なバージョンは、単なる例として表示されています。 実際のバージョンは、異なる場合があります。
    **[Active Learning]\(アクティブ ラーニング\)** が有効になると、ユーザーが送信した質問に基づいて、ナレッジ ベースから定期的に新しい質問が提案されます。 設定を再度切り替えると、 **[Active Learning] (アクティブ ラーニング)** を無効にできます。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) のアクティブ ラーニングは、既定では **有効** になっています。 提案された代わりの質問を表示するには、[編集] ページの [[表示] オプションを使用](../How-To/improve-knowledge-base.md#view-suggested-questions)します。

---

## <a name="review-suggested-alternate-questions"></a>推奨される代わりの質問を確認する

各ナレッジ ベースの **[編集]** ページで、[代わりに推奨された質問を確認](improve-knowledge-base.md)します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./manage-knowledge-bases.md)