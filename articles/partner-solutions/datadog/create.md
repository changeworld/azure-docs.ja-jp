---
title: Datadog を作成する - Azure パートナー ソリューション
description: この記事では、Azure portal を使用して Datadog のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 6b2737877ecc550540761bfb704aee98f6e7c2f4
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110655257"
---
# <a name="quickstart-get-started-with-datadog-by-creating-new-instance"></a>クイックスタート: 新しいインスタンスを作成して Datadog の使用を開始する

このクイックスタートでは、Datadog の新しいインスタンスを作成します。 新しい Datadog 組織を作成することも、[既存の Datadog 組織にリンク](link-to-existing-organization.md)することもできます。

## <a name="prerequisites"></a>前提条件

Azure で Datadog の最初のインスタンスを作成する前に、[環境を構成](prerequisites.md)します。 これらの手順は、このクイックスタートの次の手順に進む前に完了しておく必要があります。

## <a name="find-offer"></a>プランを探す

Datadog を検索するには、Azure portal を使用します。

1. [Azure Portal](https://portal.azure.com/) にアクセスしてサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace アイコン。":::

1. Marketplace で、「**Datadog**」を検索します。

1. プランの概要画面で、 **[Set up + subscribe]\(設定 + 登録\)** を選択します。

   :::image type="content" source="media/create/datadog-app-2.png" alt-text="Azure Marketplace 内の Datadog アプリケーション。":::

## <a name="create-a-datadog-resource-in-azure"></a>Azure に Datadog リソースを作成する

Datadog 組織を作成するか、Azure サブスクリプションを既存の Datadog 組織にリンクするかを尋ねる選択項目がポータルに表示されます。

新しい Datadog 組織を作成する場合は、 **[新しい Datadog 組織の作成]** で **[作成]** を選択します。

:::image type="content" source="media/create/datadog-create-link-selection.png" alt-text="Datadog 組織を作成またはリンクする" border="true":::

Datadog リソースを作成するためのフォームがポータルに表示されます。

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="[Create Datadog resource]\(Datadog リソースの作成\)" border="true":::

次の値を指定します。

|プロパティ | 説明
|:-----------|:-------- |
| サブスクリプション | Datadog リソースの作成に使用する Azure サブスクリプションを選択します。 所有者アクセス権が必要です。 |
| リソース グループ | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 [リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)は、Azure ソリューション用に関連するリソースを保持するコンテナーです。 |
| リソース名 | Datadog リソースの名前を指定します。 新しい Datadog 組織を作成する場合は、この名前が新しい Datadog 組織の名前となります。 |
| 場所 | [米国西部 2] を選択します。 現在、サポートされているリージョンは、米国西部 2 のみです。 |
| 料金プラン | 新しい組織を作成する場合は、利用可能な Datadog プランの一覧から選択します。 |
| [請求期間] | 月単位。 |

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

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="ログとメトリックを構成する" border="true":::

Azure から Datadog に出力できるログには 2 種類あります。

1. **サブスクリプション レベルのログ** - [コントロール プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)でのリソースの操作に関する分析情報を提供します。 サービス正常性イベントの更新も含まれます。 アクティビティ ログを使用すると、いつ、だれが、どのような書き込み操作 (PUT、POST、DELETE) をしたかを確認できます。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。

1. **Azure リソース ログ** - [データ プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)で Azure リソースに対して行われた操作に関する分析情報を提供します。 たとえば、キー コンテナーからシークレットを取得することは、データ プレーン操作です。 また、データベースに対して要求を行うことも、データ プレーン操作です。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。

サブスクリプション レベルのログを Datadog に送信するには、 **[Send subscription activity logs]\(サブスクリプションのアクティビティ ログを送信する\)** を選択します。 このオプションをオフのままにすると、サブスクリプション レベルのログは Datadog に送信されません。

Azure リソース ログを Datadog に送信するには、 **[Send Azure resource logs for all defined resources]\(すべての定義済みリソースについて Azure リソース ログを送信する\)** を選択します。 Azure リソース ログの種類は、[[Azure Monitor Resource Log categories]\(Azure Monitor リソース ログのカテゴリ\)](../../azure-monitor/essentials/resource-logs-categories.md) に一覧表示されます。  Datadog にログを送信する Azure リソースのセットをフィルター処理するには、Azure リソース タグを使用します。

Datadog に送信されるログには、Azure によって課金されます。 詳細については、Azure Marketplace パートナーに送信される[プラットフォーム ログの価格](https://azure.microsoft.com/pricing/details/monitor/)をご確認ください。

メトリックとログの構成が完了したら、 **[Next: Single sign-on]\(次へ: シングル サインオン\)** を選択します。

## <a name="configure-single-sign-on"></a>Configure single sign-on

組織で Azure Active Directory を ID プロバイダーとして使用している場合は、Azure portal から Datadog へのシングル サインオンを確立できます。 組織で別の ID プロバイダーを使用している場合、またはこの時点でシングル サインオンを確立しない場合は、このセクションをスキップできます。

Azure Active Directory でシングル サインオンを確立するには、 **[Azure Active Directory を使用したシングル サインオンを有効にする]** のチェック ボックスをオンにします。

Azure portal によって、Azure Active Directory から適切な Datadog アプリケーションが取得されます。 このアプリは、前の手順で指定したエンタープライズ アプリと一致します。

Datadog アプリの名前を選択します。

:::image type="content" source="media/create/sso.png" alt-text="Datadog へのシングル サインオンを有効にする。" border="true":::

**タグ** を選択します。

## <a name="add-custom-tags"></a>カスタム タグを追加する

新しい Datadog リソースにカスタム タグを指定できます。 Datadog リソースに適用するタグの名前と値のペアを指定します。

:::image type="content" source="media/create/tags.png" alt-text="Datadog リソースにカスタム タグを追加する。" border="true":::

タグの追加が完了したら、 **[次へ: レビューと作成]** を選択します。

## <a name="review--create-datadog-resource"></a>Datadog リソースを確認および作成する

選択内容と利用規約を確認します。 検証が完了した後、 **[作成]** を選択します。

:::image type="content" source="media/create/review-create.png" alt-text="Datadog リソースを確認および作成する。" border="true":::

Azure によって Datadog リソースがデプロイされます。

プロセスが完了したら、 **[リソースに移動]** を選択して、Datadog リソースを表示します。

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog リソースのデプロイ。" border="true":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Datadog リソースを管理する](manage.md)
