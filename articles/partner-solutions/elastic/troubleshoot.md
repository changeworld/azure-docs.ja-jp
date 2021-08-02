---
title: Elastic のトラブルシューティング - Azure パートナー ソリューション
description: この記事では、Azure と Elastic の統合のトラブルシューティングについて説明します
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4bc4bc9d9bb5890ed95bbb446f570d25e43d813
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952569"
---
# <a name="troubleshooting-elastic-integration-with-azure"></a>Azure と Elastic の統合のトラブルシューティング

このドキュメントでは、Elastic を使用するソリューションのトラブルシューティングについて説明します。

## <a name="unable-to-create-an-elastic-resource"></a>Elastic リソースを作成できない

Azure と Elastic の統合は、Azure サブスクリプションに対する "*所有者*" アクセス権を持つユーザーだけが設定できます。 [適切なアクセス権があることを確認します](../../role-based-access-control/check-access.md)。

## <a name="logs-not-being-emitted-to-elastic"></a>Elastic にログが送信されない

Elastic にログを送信するのは、[Azure Monitor リソースのログ カテゴリ](../../azure-monitor/essentials/resource-logs-categories.md)の一覧に含まれているリソースだけです。 リソースから Elastic にログが送信されているかどうかを確認するには、そのリソースの [Azure 診断設定](../../azure-monitor/essentials/diagnostic-settings.md)に移動します。 使用可能な診断設定オプションがあることを確認します。

:::image type="content" source="media/troubleshoot/check-diagnostic-setting.png" alt-text="診断設定を確認する":::

## <a name="purchase-errors"></a>購入エラー

* 購入に失敗するのは、有効なクレジット カードが Azure サブスクリプションに接続されていないか、支払い方法がサブスクリプションに関連付けられていないためです。

  別の Azure サブスクリプションを使用してください。 または、サブスクリプションのクレジット カードまたは支払い方法を追加するか、更新してください。 詳細については、[クレジットと支払い方法の更新](../../cost-management-billing/manage/change-credit-card.md)に関するページを参照してください。

* この EA サブスクリプションでは、Marketplace での購入は許可されていません。

  別のサブスクリプションを使用してください。 または、お使いの EA サブスクリプションで Marketplace の購入が有効かどうかを確認してください。 詳細については、[Marketplace での購入を有効にする](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)方法に関する記事を参照してください。

## <a name="get-support"></a>サポートを受ける

Azure と Elastic の統合についてサポートに問い合わせるには、左ペインで **[新しいサポート リクエスト]** を選択します。 **[Open an Elastic Support ticket]\(Elastic のサポート チケットを開く\)** を選択します。

:::image type="content" source="media/troubleshoot/open-ticket.png" alt-text="サポート チケットを開く":::

Elastic サイトでサポート リクエストを開きます。

:::image type="content" source="media/troubleshoot/elastic-support.png" alt-text="Elastic のサポートを開く":::

## <a name="next-steps"></a>次の手順

Elastic の[インスタンスの管理](manage.md)について確認します。