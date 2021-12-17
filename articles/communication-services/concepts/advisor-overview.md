---
title: Azure Advisor for Azure Communication Services を活用する
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 向け Azure Advisor オファリングについて説明します。
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 6c229f4afddc77e9b85f0dbd7fb4669facf8788e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983990"
---
# <a name="azure-advisor-for-azure-communication-services"></a>Azure Advisor for Azure Communication Services

[Azure Advisor](../../advisor/advisor-overview.md) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure Communication Services は Azure Advisor にオンボードされていて、お客様のコミュニケーション リソースを最適化する方法に関する推奨事項を提供します。 これらの推奨事項は、[Azure portal](https://portal.azure.com) の [[Advisor] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)で確認できます。 推奨事項はご利用の [Azure アクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)に格納されます。[ARM テンプレート](../../advisor/advisor-alerts-arm.md)または[ポータル](../../advisor/advisor-alerts-portal.md)を使用して、これらの推奨事項に対するアラートを構成することができます。 

## <a name="install-the-latest-sdks"></a>最新の SDK をインストールする

最新の修正プログラムと更新プログラムを確実に適用するには、SDK を常に最新の状態に維持することをお勧めします。 使用している SDK の新しいバージョンが入手可能な場合は、最新の SDK への更新に関する推奨事項が **[パフォーマンス]** カテゴリに表示されます。

![チャット SDK を更新するように勧める、Azure Advisor での推奨事項の例。](./media/advisor-chat-sdk-update-example.png)

この機能では、次に示す SDK と、それらが対応するすべての言語がサポートされています。 この機能で送信されるのは、一般公開されている最新バージョンの SDK に関するレコメンデーションのみであることに注意してください。 ベータ版またはプレビュー バージョンでは、レコメンデーションやアラートはトリガーされません。 詳細については、使用可能な [SDK オプション](./sdk-options.md)に関するページを参照してください。

* 呼び出し元 (クライアント)
* チャット
* SMS
* ID
* 電話番号
* 管理
* ネットワーク トラバーサル
* 呼び出し自動化

## <a name="next-steps"></a>次のステップ

次のドキュメントもご覧ください。

- [ログの記録と診断](./logging-and-diagnostics.md)
- [メトリック](./metrics.md)
