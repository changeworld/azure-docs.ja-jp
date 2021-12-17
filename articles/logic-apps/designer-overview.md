---
title: シングルテナント ワークフロー デザイナーについて
description: シングルテナント Azure Logic Apps のデザイナーを使用し、Azure portal を通じてワークフローを視覚的に作成する方法について説明します。 この最新バージョンの利点と機能について説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129276"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>シングルテナント Azure Logic Apps のワークフロー デザイナーについて

Azure portal で Azure Logic Apps を使用すると、視覚的に、またはプログラムによって、"[*ワークフロー*](logic-apps-overview.md#workflow)" を編集できます。 "[*ロジック アプリ*" リソース](logic-apps-overview.md#logic-app)をポータルで開いた後で、 **[開発者]** の下のリソース メニューで、[ **[コード]** ビュー](#code-view)と **[デザイナー]** ビューのどちらかを選択できます。 ワークフローの開発、編集、実行を視覚的に行う場合は、デザイナー ビューを選択します。 デザイナー ビューとコード ビューはいつでも切り替えることができます。

> [!IMPORTANT]
> 現時点で、デザイナーの最新バージョンは、*シングルテナント* Azure Logic Apps 環境で実行される *Standard* ロジック アプリ リソースのみで使用可能です。 Logic Apps でのさまざまなリソースの種類と実行環境の詳細については、「[Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)」を参照してください。

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="Azure portal のロジック アプリ リソース ページのスクリーンショット。ワークフローを [コード] ビューまたは [デザイナー] ビューで表示するためのオプションが示されています。":::

**[デザイナー]** ビューを選択すると、ワークフローがワークフロー デザイナーで開きます。

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="ワークフロー デザイナーと、スイッチと条件を使用するエンタープライズ ワークフローのサンプルを示すスクリーンショット。" lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>前提条件

- Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- [シングルテナント環境](single-tenant-overview-compare.md)の *Standard* ロジック アプリ リソース。 詳細については、「[Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)」を参照してください。
- シングルテナント ロジック アプリのワークフロー。

## <a name="latest-version-features"></a>最新バージョンの機能

最新のワークフロー デザイナーでは、次のような注目すべき機能と利点を備えた新しいエクスペリエンスが提供されます。

- より複雑なワークフローをサポートする新しいレイアウト エンジン。 
- 新しいレイアウト エンジン、よりコンパクトなキャンバス、カードベースのレイアウトの更新によって、複雑なワークフローを簡単に作成および表示できます。
- ワークフロー レイアウトとは別のパネルを使用してステップを追加および編集します。 この変更により、ワークフロー レイアウトを表示するためのすっきりしたわかりやすいキャンバスが提供されます。 詳細については、「[ワークフローにステップを追加する](#add-steps-to-workflows)」を参照してください。
- キーボード ナビゲーションを使用して、デザイナー上でワークフローのステップ間を移動します。
  - 次のカードに移動: **Ctrl** +  **&darr;**
  - 前のカードに移動: **Ctrl** +  **&uarr;**

## <a name="add-steps-to-workflows"></a>ワークフローにステップを追加する

ワークフロー デザイナーには、ワークフローのステップを追加、編集、および削除するための視覚的な方法が用意されています。 ワークフローの最初のステップとしては、必ず "[*トリガー*](logic-apps-overview.md#trigger)" を追加します。 次に、1つまたは複数の "[*アクション*](logic-apps-overview.md#action)" を追加して、ワークフローを完成させます。

ワークフローにトリガーまたはアクションを追加するには、次の手順を実行します。

1. デザイナーでワークフローを開きます。
1. デザイナーで **[操作を選択してください]** を選択すると、 **[トリガーの追加]** または **[アクションの追加]** という名前のペインが開きます。 
1. 開いたペインで、次の方法で一覧をフィルター処理して操作を見つけます。
    1. 検索バーにサービス、コネクタ、またはカテゴリを入力して、関連する操作を表示します。 たとえば、`Azure Cosmos DB` または `Data Operations` です。 
    1. 使用する特定の操作がわかっている場合は、その名前を検索バーに入力します。 たとえば、`Call an Azure function` または `When an HTTP request is received` です。
    1. **[組み込み]** タブを選択すると、"[*組み込み操作*](logic-apps-overview.md#built-in-operations)" のカテゴリのみが表示されます。 または、 **[Azure]** タブを選択すると、Azure で使用可能なその他のカテゴリの操作が表示されます。
    1. トリガーまたはアクションのみを表示するには、 **[トリガー]** または **[アクション]** タブを選択します。ただし、トリガーは最初のステップとして、アクションはそれ以降のステップとしてしか追加できません。 操作のカテゴリに基づいて、トリガーまたはアクションのみを使用できます。
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="Azure portal の Logic Apps デザイナーのスクリーンショット。新しい操作を追加するために編集中のワークフローが示されています。" lightbox="./media/designer-overview/designer-add-operation.png":::
1. 使用する操作を選択します。 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="Logic Apps デザイナーのスクリーンショット。サービスまたは名前によってフィルター処理できる操作候補のペインが示されています。" lightbox="./media/designer-overview/designer-filter-operations.png":::
1. 必要に応じて、トリガーまたはアクションを構成します。
    1. 必須フィールドには、名前の前に赤いアスタリスク (&ast;) が付いています。
    1. 一部のトリガーとアクションでは、別のサービスへの接続を作成することが必要になる場合があります。 場合によっては、アカウントへのサインイン、またはサービスの資格情報の入力が必要になることがあります。 たとえば、Office 365 Outlook コネクタを使用して電子メールを送信する場合は、Outlook 電子メール アカウントを承認する必要があります。
    1. 一部のトリガーとアクションでは動的コンテンツが使用されます。その場合、情報や式を入力する代わりに変数を選択できます。
1. ツールバーの **[保存]** を選択して、変更を保存します。 この手順では、ワークフローが有効であることも確認されます。 

## <a name="code-view"></a>[コード ビュー]

**[コード]** ビューを使用すると、ワークフロー定義ファイルを JSON 形式で直接編集できます。 このビューで行った変更を保存するには、必ず **[保存]** を選択してください。 

> [!TIP]
> また、 **[コード]** ビューを使用すると、Azure Command Line Interface (Azure CLI) やその他の方法を使用するよりも、簡単にワークフロー定義を探してコピーできます。

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="[コード] ビューの Logic Apps ワークフローのスクリーンショット。Azure portal で編集中の JSON ワークフロー定義が示されています。":::


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)
