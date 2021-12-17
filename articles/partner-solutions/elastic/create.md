---
title: Elastic アプリケーションを作成する - Azure パートナー ソリューション
description: この記事では、Azure portal を使用して Elastic のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 456decb74534cfd3ca5bfbf966c57b4182814225
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952606"
---
# <a name="quickstart-get-started-with-elastic"></a>クイックスタート: Elastic の使用を開始する

このクイックスタートでは、Azure portal を使用して Elastic のインスタンスと Azure ソリューションを統合します。

## <a name="prerequisites"></a>前提条件

- サブスクリプション所有者 - Elastic と Azure の統合は、Azure サブスクリプションに対する "_所有者_" アクセス権を持つユーザーだけが作成できます。 設定を始める前に、[適切なアクセス権があることを確認してください](../../role-based-access-control/check-access.md)。
- シングル サインオン アプリ - シングル サインオン (SSO) により Azure portal と Elastic Cloud の間を自動的に移動できること。 このオプションは、すべての Azure ユーザーに対して自動的に有効になります。 

## <a name="find-offer"></a>プランを探す

Azure portal を使用して Elastic アプリケーションを見つけます。

1. Web ブラウザーで、[Azure portal](https://portal.azure.com/) に移動してサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace アイコン。":::

1. _[Elastic]_ を検索し、利用可能なオファリングから **[Elasticsearch (Elastic Cloud)]** を選択します。

1. **[Set up + subscribe]\(設定 + 登録\)** を選択します。

   :::image type="content" source="media/create/set-up.png" alt-text="オファーを選択します。":::

## <a name="create-resource"></a>リソースを作成する

Elastic のオファーを選択すると、アプリケーションを設定できるようになります。

1. **[Create Elastic Resource]\(Elastic リソースの作成\)** の [基本] ページで、次の値を指定します。

    :::image type="content" source="media/create/create-resource.png" alt-text="Elastic リソースを設定するフォーム。":::

    | プロパティ | 説明 |
    | ---- | ---- |
    | **サブスクリプション** | ドロップダウンから、所有者アクセス権のある Azure サブスクリプションを選択します。 |
    | **リソース グループ** | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../../azure-resource-manager/management/overview.md)に関するページをご覧ください。 |
    | **[Elastic account name]\(Elastic アカウント名\)** | 作成する Elastic アカウントの名前を指定します |
    | **[リージョン]** | **[米国西部 2]** または **[英国南部]** を選択します。 プレビュー期間中は、これらのリージョンだけが Elastic でサポートされます。 |
    | **[料金プラン]** | **従量課金制**。 |
    | **価格** | 選択した Elastic プランに基づいて指定します。 |

   完了したら、 **[Next: Logs and Metrics]\(次へ: ログとメトリック\)** を選択します。

1. **[Logs & metrics]\(ログとメトリック\)** で、Elastic に送信するログを指定します。

    :::image type="content" source="media/create/configure-logs.png" alt-text="送信するログを選択します。":::

   Azure から Elastic に出力できるログには 2 種類あります。

   **サブスクリプション ログ** では、サブスクリプションの各 Azure リソースでの操作についての分析情報が[管理プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)から提供されます。 また、このログでは Service Health イベントで更新も提供されます。 アクティビティ ログを使用して、サブスクリプションのリソースに対して行われるすべての書き込み操作 (PUT、POST、DELETE) について、何を、誰が、いつ行ったのかを確認します。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。

   **Azure リソース ログ** では、[データ プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)内で発生した操作に関する分析情報が提供されます。 たとえば、キー コンテナーからのシークレットの取得や、データベースへの要求は、データ プレーン アクティビティです。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。 Azure リソース ログの種類は、[[Azure Monitor Resource Log categories]\(Azure Monitor リソース ログのカテゴリ\)](../../azure-monitor/essentials/resource-logs-categories.md) に一覧表示されます。

   ログを Elastic に送信する Azure リソースをフィルター処理するには、リソース タグを使用します。 ログを送信するためのタグ ルールは次のとおりです。

   * 既定では、すべてのリソースのログが収集されます。 
   * *Include* タグが指定されたリソースは、ログを Elastic に送信します。 
   * *Exclude* タグが指定されたリソースは、ログを Elastic に送信しません。 
   * 含めるルールと除外するルールの間に競合がある場合は、除外するルールが優先されます。
 
   **[次へ: タグ]** を選択して、新しい Elastic リソースのタグを設定します。

1. **[タグ]** で、新しい Elastic リソースのカスタム タグを追加します。 各タグは名前と値で構成されます。 タグの追加が終わったら、 **[次へ: 確認と作成]** を選択して、リソース作成の最後のステップに移動します。 

   :::image type="content" source="media/create/add-tags.png" alt-text="Elastic リソースにタグを追加します":::

1. **[確認と作成]** では、構成が検証されます。 前のフォームで行った選択を確認できます。 このオファリングの使用条件を確認することもできます。

   :::image type="content" source="media/create/review-validation.png" alt-text="選択を確認して検証します":::

   検証が成功し、条件を確認したら、 **[作成]** を選択します。

1. Azure によってデプロイが開始されます。

   :::image type="content" source="media/create/deployment-in-progress.png" alt-text="デプロイの状態":::

1. デプロイが完了したら、 **[リソースに移動]** を選択して、デプロイされたリソースを表示します。

    :::image type="content" source="media/create/deployment-complete.png" alt-text="デプロイの状態を表示します":::


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Elastic リソースを管理する](manage.md)