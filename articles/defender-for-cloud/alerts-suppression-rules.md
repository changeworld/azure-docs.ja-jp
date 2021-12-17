---
title: Microsoft Defender for Cloud の警告抑制ルールを使用して誤検知やその他の望ましくないセキュリティアラートを抑制する
description: この記事では、クラウドの抑制規則に Microsoft Defender を使用して、不要なセキュリティ警告を非表示にする方法について説明します
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/09/2021
ms.service: defender-for-cloud
ms.topic: how-to
ms.openlocfilehash: 38f10155de47db37b3ab73d81285b578c895e769
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525608"
---
# <a name="suppress-alerts-from-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud からのアラートを抑制する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このページでは、アラート抑制ルールを使用して、クラウドの Defender からの誤検知や望ましくないセキュリティアラートを抑制する方法について説明します。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|Free<br>(ほとんどのセキュリティアラートは、 [セキュリティが強化](enable-enhanced-security.md)された機能でのみ使用できます)|
|必要なロールとアクセス許可:|**セキュリティ管理者** および **所有者** は、ルールを作成および削除できます。<br>**セキュリティ閲覧者** および **閲覧者** は、ルールを表示できます。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||


## <a name="what-are-suppression-rules"></a>抑制ルールとは

さまざまな Microsoft Defender プランによって、環境のあらゆる分野で脅威が検出され、セキュリティアラートが生成されます。

1 つのアラートが特に注意する必要がないか無関係である場合は、手動で無視することもできます。 そうする代わりに、抑制ルール機能を使用して、今後は同様のアラートを自動的に無視することもできます。 通常は、次のような場合に抑制ルールを使用します。

- ユーザーが擬陽性と判断したアラートを抑制する

- あまりにも頻繁にトリガーされるため有用ではないアラートを抑制する

抑制ルールは、アラートが自動的に無視される条件を定義します。

> [!CAUTION]
> セキュリティアラートを抑制すると、クラウドの脅威保護に対する Defender の有効性が低下します。 抑制ルールを使用する場合は、その潜在的な影響を慎重に確認し、長期的に監視する必要があります。

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="アラート抑制ルールの作成。":::

## <a name="create-a-suppression-rule"></a>抑制ルールを作成する

次のいくつかの方法で、不要なセキュリティ アラートを抑制するルールを作成できます。

- 管理グループ レベルでアラートを抑制するには、Azure Policy を使用します。
- サブスクリプション レベルでアラートを抑制するには、以下で説明するように、Azure portal または REST API を使用できます。

抑制ルールで無視できるのは、選択したサブスクリプションで既にトリガーされたアラートのみです。

Azure portal で直接ルールを作成するには:

1. [Defender for Cloud のセキュリティ警告] ページ:

    - 今後表示しない特定のアラートを選択し、詳細ウィンドウで **[アクションの実行]** を選択します。

    - または、ページの上部にある **[抑制ルール]** リンクを選択し、[抑制ルール] ページで **[新しい抑制ルールの作成]** を選択します。

        ![[新しい抑制ルールの作成]** ボタン。](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. [新しい抑制ルール] ペインで、作成した新しいルールの詳細を入力します。
    - ルールでは、**すべてのリソース** でそのアラートを無視して、今後はこれと同様のアラートが一切表示されないようにすることができます。     
    - ルールでは、アラートが特定の IP アドレス、プロセス名、ユーザー アカウント、Azure リソース、または場所に関係する場合など、**特定の条件** でそのアラートを無視することもできます。

    > [!TIP]
    > 特定のアラートから新しいルールのページを開いた場合は、そのアラートとサブスクリプションが新しいルールに自動的に構成されます。 **[新しい抑制ルールの作成]** リンクを使用した場合は、選択したサブスクリプションはポータルの現在のフィルターと一致します。

    [![抑制ルールの作成ペイン。](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. ルールの詳細を入力します。
    - **名前** - ルールの名前。 ルール名は、文字または数字で始まり、2 から 50 文字で指定する必要があります。ダッシュ (-) とアンダースコア (_) 以外の記号は使用できません。 
    - **状態** - 有効または無効。
    - **理由** - あらかじめ登録された理由のいずれかを選択するか、合うものがない場合は [その他] を選択します。
    - **有効期限** - そのルールの終了日時。 ルールは最大 6 か月間実行できます。
1. 必要に応じて、 **[シミュレート]** ボタンを使用してルールをテストし、そのルールがアクティブだったなら、どれほどのアラートが無視されていたかを確認することができます。
1. ルールを保存します。 


## <a name="edit-a-suppression-rule"></a>抑制ルールを編集する

作成したルールを編集する場合は、[抑制ルール] ページを使用します。

1. Defender for Cloud のセキュリティ警告 ページで、ページの上部にある  **抑制規則** リンクを選択します。
1. [抑制ルール] ページが開き、選択したサブスクリプションのすべてのルールが表示されます。

    [![抑制ルールの一覧。](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 1 つのルールを編集する場合は、そのルールの省略記号メニュー (...) を開き、 **[編集]** を選択します。
1. 必要な変更を加えて、 **[適用]** を選択します。 

## <a name="delete-a-suppression-rule"></a>抑制ルールを削除する

作成した 1 つまたは複数のルールを削除する場合は、[抑制ルール] ページを使用します。

1. Defender for Cloud のセキュリティ警告 ページで、ページの上部にある  **抑制規則** リンクを選択します。
1. [抑制ルール] ページが開き、選択したサブスクリプションのすべてのルールが表示されます。
1. 1 つのルールを削除する場合は、そのルールの省略記号メニュー (...) を開き、 **[削除]** を選択します。
1. 複数のルールを削除する場合は、削除するルールのチェック ボックスを選択し、 **[削除]** を選択します。
    ![1 つまたは複数の抑制ルールの削除。](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>API を使用して抑制ルールの作成および管理を行う

クラウドの REST API に対して Defender を使用して、アラート抑制ルールを作成、表示、または削除することができます。 

抑制ルールに関連する REST API の HTTP メソッドは次のとおりです。

- **PUT**: 指定されたサブスクリプションで抑制ルールを作成または更新する。

- **GET**:

    - 指定されたサブスクリプションに対して構成されているすべてのルールを一覧表示する。 このメソッドは、該当するルールの配列を返します。

    - 指定されたサブスクリプションの特定のルールの詳細を取得する。 このメソッドは、1 つの抑制ルールを返します。

    - 設計フェーズのままで、抑制ルールの影響をシミュレートする。 この呼び出しは、そのルールがアクティブだったなら無視されていたはずの既存のアラートを識別します。

- **DELETE**: 既存のルールを削除します (ただし、そのルールによって既に無視されているアラートの状態は変更されません)。

詳細と使用例については、[API のドキュメント](/rest/api/securitycenter/)を参照してください。 


## <a name="next-steps"></a>次のステップ

この記事では、不要なアラートを自動的に消去する Microsoft Defender for Cloud の抑制規則について説明します。

セキュリティの警告の詳細については、次のページを参照してください。

- [セキュリティの警告とインテントの強制終了チェーン](alerts-reference.md) -Defender for Cloud から得られるセキュリティの警告のリファレンスガイドです。
