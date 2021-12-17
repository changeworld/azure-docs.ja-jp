---
title: 既存の Datadog へのリンク - Azure パートナー ソリューション
description: この記事では、Azure portal を使用して Datadog の既存のインスタンスにリンクする方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: b142b7d6e34905fe02de2a7546f9265289016808
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110657144"
---
# <a name="quickstart-link-to-existing-datadog-organization"></a>クイックスタート: 既存の Datadog 組織にリンクする

このクイックスタートでは、Datadog の既存の組織にリンクします。 [新しい Datadog 組織を作成](create.md)することも、既存の Datadog 組織にリンクすることもできます。

## <a name="prerequisites"></a>前提条件

Azure で Datadog の最初のインスタンスを作成する前に、[環境を構成](prerequisites.md)します。 これらの手順は、このクイックスタートの次の手順に進む前に完了しておく必要があります。

## <a name="find-offer"></a>プランを探す

Datadog を検索するには、Azure portal を使用します。

1. [Azure Portal](https://portal.azure.com/) にアクセスしてサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/link-to-existing-organization/marketplace.png" alt-text="Marketplace アイコン。":::

1. Marketplace で、「**Datadog**」を検索します。

1. プランの概要画面で、 **[Set up + subscribe]\(設定 + 登録\)** を選択します。

   :::image type="content" source="media/link-to-existing-organization/datadog-app-2.png" alt-text="Azure Marketplace 内の Datadog アプリケーション。":::

## <a name="link-to-existing-datadog-organization"></a>既存の Datadog 組織にリンクする

Datadog 組織を作成するか、Azure サブスクリプションを既存の Datadog 組織にリンクするかを尋ねる選択項目がポータルに表示されます。

既存の Datadog 組織にリンクする場合は、 **[Azure サブスクリプションの既存の Datadog 組織へのリンク]** の下の **[作成]** を選択します。

:::image type="content" source="media/link-to-existing-organization/datadog-create-link-selection.png" alt-text="Datadog 組織を作成またはリンクする" border="true":::

Azure 内の新しい Datadog リソースを **US3** の既存の Datadog 組織にリンクすることができます。

Datadog リソースを作成するためのフォームがポータルに表示されます。

:::image type="content" source="media/link-to-existing-organization/link-to-existing.png" alt-text="既存の Datadog 組織にリンクする。" border="true":::

次の値を指定します。

|プロパティ | 説明
|:-----------|:-------- |
| サブスクリプション | Datadog リソースの作成に使用する Azure サブスクリプションを選択します。 所有者アクセス権が必要です。 |
| リソース グループ | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 [リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)は、Azure ソリューション用に関連するリソースを保持するコンテナーです。 |
| リソース名 | Datadog リソースの名前を指定します。 新しい Datadog 組織を作成する場合は、この名前が新しい Datadog 組織の名前となります。 |
| 場所 | [米国西部 2] を選択します。 現在、サポートされているリージョンは、米国西部 2 のみです。 |

**[Datadog 組織へのリンク]** を選択します。 このリンクを通じて、Datadog 認証ウィンドウが開きます。 Datadog にサインインします。

既定では、Azure によって現在の Datadog 組織が自分の Datadog リソースにリンクされます。 別の組織にリンクする場合は、次に示すように、認証ウィンドウで適切な組織を選択します。

:::image type="content" source="media/link-to-existing-organization/select-datadog-organization.png" alt-text="リンクする適切な Datadog 組織を選択する" border="true":::

**[Next: Metrics and logs]\(次へ: メトリックとログ\)** を選択し、メトリックとログを構成します。

## <a name="configure-metrics-and-logs"></a>メトリックとログを構成する

Azure リソース タグを使用して、どのメトリックとログを Datadog に送信するかを構成します。 特定のリソースのメトリックとログを含めたり除外したりすることができます。

**メトリック** を送信するためのタグ ルールは次のとおりです。

- 既定では、仮想マシン、仮想マシン スケール セット、および App Service プランを除く、すべてのリソースのメトリックが収集されます。
- *Include* タグが適用された仮想マシン、仮想マシン スケール セット、および App Service プランの場合は、メトリックが Datadog に送信されます。
- *Exclude* タグが適用された仮想マシン、仮想マシン スケール セット、および App Service プランの場合、メトリックは Datadog に送信されません。
- 含めるルールと除外するルールの間に競合がある場合は、除外するルールが優先されます

**ログ** を送信するためのタグ ルールは次のとおりです。

- 既定では、すべてのリソースのログが収集されます。
- *Include* タグが適用された Azure リソースの場合は、ログが Datadog に送信されます。
- *Exclude* タグが適用された Azure リソースの場合、ログは Datadog に送信されません。
- 含めるルールと除外するルールの間に競合がある場合は、除外するルールが優先されます。

たとえば、次のスクリーンショットは、*Datadog = True* とタグ付けされた仮想マシン、仮想マシン スケール セット、および App Service プランでのみ、Datadog にメトリックを送信するタグ ルールを示しています。

:::image type="content" source="media/link-to-existing-organization/config-metrics-logs.png" alt-text="ログとメトリックを構成する" border="true":::

Azure から Datadog に出力できるログには 2 種類あります。

* **サブスクリプション レベルのログ** - [コントロール プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)でのリソースの操作に関する分析情報を提供します。 サービス正常性イベントの更新も含まれます。 アクティビティ ログを使用すると、いつ、だれが、どのような書き込み操作 (PUT、POST、DELETE) をしたかを確認できます。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。

* **Azure リソース ログ** - [データ プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)で Azure リソースに対して行われた操作に関する分析情報を提供します。 たとえば、キー コンテナーからシークレットを取得することは、データ プレーン操作です。 また、データベースに対して要求を行うことも、データ プレーン操作です。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。

サブスクリプション レベルのログを Datadog に送信するには、 **[Send subscription activity logs]\(サブスクリプションのアクティビティ ログを送信する\)** を選択します。 このオプションをオフのままにすると、サブスクリプション レベルのログは Datadog に送信されません。

Azure リソース ログを Datadog に送信するには、 **[Send Azure resource logs for all defined resources]\(すべての定義済みリソースについて Azure リソース ログを送信する\)** を選択します。 Azure リソース ログの種類は、[[Azure Monitor Resource Log categories]\(Azure Monitor リソース ログのカテゴリ\)](../../azure-monitor/essentials/resource-logs-categories.md) に一覧表示されます。  Datadog にログを送信する Azure リソースのセットをフィルター処理するには、Azure リソース タグを使用します。

Datadog に送信されるログには、Azure によって課金されます。 詳細については、Azure Marketplace パートナーに送信される[プラットフォーム ログの価格](https://azure.microsoft.com/pricing/details/monitor/)をご確認ください。

メトリックとログの構成が完了したら、 **[Next: Single sign-on]\(次へ: シングル サインオン\)** を選択します。

## <a name="configure-single-sign-on"></a>Configure single sign-on

Datadog リソースを既存の Datadog 組織にリンクする場合は、この手順でシングル サインオンを設定することはできません。 代わりに、Datadog リソースを作成した後でシングル サインオンを設定します。 詳細については、「[シングル サインオンを再構成する](manage.md#reconfigure-single-sign-on)」を参照してください。

:::image type="content" source="media/link-to-existing-organization/linking-sso.png" alt-text="既存の Datadog 組織にリンクする場合のシングル サインオン。" border="true":::

**タグ** を選択します。

## <a name="add-custom-tags"></a>カスタム タグを追加する

新しい Datadog リソースにカスタム タグを指定できます。 Datadog リソースに適用するタグの名前と値のペアを指定します。

:::image type="content" source="media/link-to-existing-organization/tags.png" alt-text="Datadog リソースにカスタム タグを追加する。" border="true":::

タグの追加が完了したら、 **[次へ: レビューと作成]** を選択します。

## <a name="review--create-datadog-resource"></a>Datadog リソースを確認および作成する

選択内容と利用規約を確認します。 検証が完了した後、 **[作成]** を選択します。

:::image type="content" source="media/link-to-existing-organization/review-create.png" alt-text="Datadog リソースを確認および作成する。" border="true":::

Azure によって Datadog リソースがデプロイされます。

プロセスが完了したら、 **[リソースに移動]** を選択して、Datadog リソースを表示します。

:::image type="content" source="media/link-to-existing-organization/go-to-resource.png" alt-text="Datadog リソースのデプロイ。" border="true":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Datadog リソースを管理する](manage.md)
