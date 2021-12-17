---
title: Logz.io リソースを作成する - Azure パートナー ソリューション
description: Azure で Logz.io リソースを作成する方法について説明するクイックスタート記事。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: d97339251888b522fd31d30b3ce77d1bb4241f3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070073"
---
# <a name="quickstart-create-a-logzio-resource-in-azure-portal"></a>クイックスタート: Azure portal で Logz.io リソースを作成する

この記事では、Logz.io SaaS (サービスとしてのソフトウェア) の Azure 統合を有効にする方法について説明します。 Logz.io は、Azure 環境の正常性とパフォーマンスを監視するために使用します。

## <a name="prerequisites"></a>前提条件

- **サブスクリプションの所有者**: Logz.io を設定するには、Azure サブスクリプションで [所有者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)が割り当てられている必要があります。 この統合を開始する前に、[アクセス権を確認](../../role-based-access-control/check-access.md)してください。
- **リソース プロバイダーの登録**: `Microsoft.Insights` がまだサブスクリプションに登録されていない場合は、登録します。 詳細については、「[リソース プロバイダーを登録する](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」 を参照してください。

## <a name="find-offer"></a>プランを探す

Azure portal を使用して、Azure Marketplace で Logz.io を見つけます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="./media/create/marketplace.png" alt-text="Azure Marketplace。":::

1. Azure Marketplace から、_Logz.io_ を検索します。
1. **Logz.io - Observability based on ELK & Prometheus** を選択します。
1. **[Set up + subscribe]\(設定 + 登録\)** ボタンを選択します。 **[Create new Logz.io account]\(新しい Logz.io アカウントの作成\)** ウィンドウが開きます。

    :::image type="content" source="./media/create/logzio-app.png" alt-text="Logz.io 監視アプリケーション。":::

## <a name="create-new-logzio-account"></a>新しい Logz.io アカウントを作成する

**[Create a Logz.io account]\(Logz.io アカウントの作成\)** 画面の **[基本]** タブで、次の値を入力します。

| プロパティ | 説明 |
| ---- | ---- |
| **サブスクリプション** | ドロップダウン メニューから、所有者アクセス権を持っている Azure サブスクリプションを選択します。 |
| **リソース グループ** | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 [リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)は、Azure ソリューションの関連するリソースを保持するコンテナーです。 |
| **Logz アカウント名** | 作成する Logz.io アカウントの名前を指定します。 |
| **場所** | **[米国西部 2]** または **[西ヨーロッパ]** を選択します。 Logz.io は、これらの Azure リージョンのみをサポートしています。 |
| **[料金プラン]** | 使用できる Logz.io プランの一覧から選択します。 |
| **請求期間** | **[Monthly]\(月単位\)** が既定値です。 |
| **価格** | 選択した Logz.io プランに基づいて指定されます。 |

:::image type="content" source="./media/create/basics.png" alt-text="Logz.io アカウントの作成。":::

これらの値を入力したら、 **[Next: Logs and metrics]\(次へ: ログとメトリック\)** ボタンを選択します。

## <a name="configure-logs"></a>ログを構成する

**[ログとメトリック]** タブで、Logz.io にログとメトリックを送信する Azure リソースを指定します。

Azure から Logz.io に出力できるログには、次の 2 種類があります。

- **サブスクリプション レベルのログ**: 外部 (管理プレーン) からのサブスクリプションの各 Azure リソースに対する操作と、**Service Health** イベントの更新に関する分析情報を提供します。 **アクティビティ ログ** を使用すると、サブスクリプションのリソースに対して行われるすべての書き込み操作 (PUT、POST、DELETE) について、何を、誰が、いつ行ったのかを確認できます。 Azure サブスクリプションごとに 1 つの **アクティビティ ログ** があります。
- **Azure リソース ログ**: Azure リソース内 (データ プレーン) で実行された操作に関する分析情報を提供します。 これには、キー コンテナーからのシークレットの取得や、データベースへの要求などが含まれます。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。

サブスクリプション レベルのログは、 **[Send subscription level logs]\(サブスクリプション レベルのログを送信する\)** というタイトルのボックスをオンにすることで、Logz.io に送信できます。 このボックスがオンになっていないと、サブスクリプション レベルのログは Logz.io に送信されません。

Azure リソース ログは、 **[Send Azure resource logs for all defined resources]\(すべての定義済みリソースについて Azure リソース ログを送信する\)** というタイトルのボックスをオンにすることで、Logz.io に送信できます。 Azure リソース ログの種類は、[[Azure Monitor Resource Log categories]\(Azure Monitor リソース ログのカテゴリ\)](../../azure-monitor/essentials/resource-logs-categories.md) に一覧表示されます。 Logz.io にログを送信する Azure リソースの特定のセットをフィルター処理するには、Azure リソース タグを使用します。

ログを送信するためのタグ ルールは、次のとおりです。

- 既定では、すべてのリソースのログが収集されます。
- _Include_ タグが指定された Azure リソースは、ログを Logz.io に送信します。
- _Exclude_ タグが指定された Azure リソースは、ログを Logz.io に送信 **しません**。
- 包含ルールと除外ルールの間に競合がある場合は、除外ルールが優先されます。

:::image type="content" source="./media/create/logs.png" alt-text="ログとメトリックを設定する。":::

ログとメトリックを設定したら、 **[Next: Tags]\(次へ: タグ\)** ボタンを選択します。

## <a name="add-custom-tags"></a>カスタム タグを追加する

キーと値のペアを追加することにより、新しい Logz.io リソースのカスタム タグを指定できます。

それぞれのキーと値のペアに、 **[名前]** と **[値]** が含まれます。

| プロパティ | 説明 |
| ---- | ---- |
| **名前** | Azure Logz.io リソースに対応するタグの名前。 |
| **Value** | Azure Logz.io リソースに対応するタグの値。 |

:::image type="content" source="./media/create/tags.png" alt-text="カスタム タグを追加する。":::

タグを追加したら、 **[Next: Single sign-on]\(次へ: シングル サインオン\)** ボタンを選択します。

## <a name="configure-single-sign-on"></a>Configure single sign-on

シングル サインオン (SSO) は省略可能な機能です。

- SSO をオプトアウトするには、この手順をスキップします。
- SSO をオプトインする場合は、「[Logz.io シングル サインオンの設定](setup-sso.md)」を参照してください。

AAD を構成したら、 **[シングル サインオン]** タブから、Logz.io SSO アプリケーションを選択します。

:::image type="content" source="./media/create/sso.png" alt-text="シングル サインオンを構成する。":::

**[Next: Review + create]\(次へ: 確認と作成\)** ボタンを選択して、リソース作成の最後のステップに進みます。

## <a name="create-resource"></a>リソースを作成する

**[Review + create]\(確認と作成\)** ページで、すべての検証が実行されます。 **[基本]** 、 **[ログとメトリック]** 、 **[タグ]** 、および **[シングル サインオン]** のタブで行われたすべての選択を確認できます。 Logz.io と Azure Marketplace の使用条件も確認できます。

すべての情報が正しいことを確認します。 デプロイを開始するには、 **[作成]** ボタンを選択します。

:::image type="content" source="./media/create/create-resource.png" alt-text="アカウントを確認して作成する。":::

デプロイが成功すると、 **[リソースに移動]** ボタンを選択することで、デプロイされた Logz.io リソースを確認できます。

## <a name="next-steps"></a>次の手順

- Logz.io 統合を[管理する](manage.md)方法を確認する。
- 統合に関する問題を解決するには、[トラブルシューティング](troubleshoot.md)を参照してください。
