---
title: インクルード ファイル
description: インクルード ファイル
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "121778803"
---
## <a name="troubleshoot-usage-spikes"></a>使用量の急増のトラブルシューティング

このセクションでは、Azure 使用状況ファイルに使用量の急増が表示される様子、使用量の急増を回避する方法、リソースを監視する方法、Azure サポートに連絡するタイミングについて説明します。 これは、Enterprise Agreement (EA) または Microsoft 顧客契約をお持ちのお客様を対象としています。 EA 管理者または課金管理者のロールが必要となります。 アクセス許可の詳細については、[Azure の請求書と毎日の使用状況データのダウンロードまたは表示](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)に関する記事を参照してください。

特定のサービスまたはリソースに対する Azure の使用量の急増または予期しない料金は、多くの場合、インシデントまたは意図しない誤用によって発生します。

どちらの場合も、サポートに問い合わせる前に、影響を受けるサービスとリソースを絞り込む必要があります。これにより、適切なサポート分野を選択できます。

使用量と関連する料金の増加の根本原因は、Microsoft では判断できないことを理解していただくことが重要です。 そのため、お客様は Azure portal でご自身の詳細な使用状況データをダウンロードすることができます。

Microsoft では、セキュリティ上の懸念とお客様のプライバシーのため、仮想マシン、ネットワーク、データ転送などのデプロイされている Azure リソースを監視することはありません。 ただし、Microsoft では、Azure 使用量の監視方法についてはお客様にお伝えするようにしています。 最終的には、ご自身の使用量の監視は、お客様に行っていただく必要があります。

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>使用状況ファイルに急増が表示される様子

前のセクションで説明したフィルターを適用すると、異常を見つけることができます。 たとえば、**帯域幅** 測定カテゴリの急増のトラブルシューティングを行っているとします。

ピボット テーブル ツール の [行] セクションに、**製品** と **インスタンス ID** (Microsoft 顧客契約のリソース ID) を配置します。 次に、[値] に **コスト** を、[フィルター] に **サブスクリプション ID** を、[列] に **日付** を追加します。 次に、サブスクリプション ID のデータのみが表示されるようにフィルター処理します。 たとえば、「 `111111111111-1111-1111-111111111111` 」のように入力します。

次の図は、帯域幅 (データ転送) の急増がどのように表示されるかを示しています。

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="使用量の急増を示す Excel のスクリーンショット。" lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

急増は特定のリソースに関するものです。 この場合、Excel ファイルの行 7 には、ストレージ アカウント **storageaccountnameazurefile1** のコスト値が表示されています。 2020 年10 月 1 日のコスト値は、ゼロ (0) 米国ドルに近い値 (2.23043 E-06。0.000002230431449 に相当) です。 2020 年 10 月 2 日と 2020 年 10 月 3 日には、このコストが 10,000 および 28,000 米国ドルに達して大幅に急増していることがわかります。 2020 年 10 月 4 日にはコストは通常に戻っています (9.29 E-07)。

この例では、高額の帯域幅料金が発生したリソース、発生した日付、具体的な製品 (リージョン間 – データ転送 (送信) – ヨーロッパ) を特定しました。 急増が大規模なデータ転送に起因するかどうかを判断してください。 前のセクションの情報を使用して、影響を受けるリソースを確認します。

言及された日付のリソースからの転送がなかったと判断した場合は、Azure テクニカル チームに問い合わせてください。 チームの支援で、問題の原因となっているバグまたはインシデントがあるかどうかを判断できます。 この例では、影響を受けるリソースはストレージ アカウントです。 そのため、Azure Storage テクニカル チームに問い合わます。 同様に、急増が仮想マシンに影響を与えている場合は、Azure Virtual Machines テクニカル チームに連絡して、Virtual Machines サービスに影響を与えている進行中のインシデントがあるかどうかを判断してください。

進行中のインシデントがある場合、Azure テクニカル チームが Azure Billing チームとの調整を行って、返金請求を検討します。

### <a name="tools-to-monitor-azure-usage"></a>Azure の使用量を監視するためのツール

Azure Cost Management で常にコストを管理し、予算を作成できます。 詳細については、次を参照してください。

- [Azure Budgets でのコストの管理](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [チュートリアル:Azure の予算を作成して管理する](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

Storage の使用量については、Storage Analytics ツールの使用をお勧めします。 これにより、トランザクションごとのログを使用できます。 ログには詳細が記載されていますが、独自に包括的なトレースとデバッグを行うことができます。 詳細については、次を参照してください。

- [Storage Analytics](../articles/storage/common/storage-analytics.md)
- [Storage Analytics のログの形式](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [Azure Storage Analytics ログを有効にして管理する (クラシック)](../articles/storage/common/manage-storage-analytics-logs.md)

ネットワーク関連の使用量については、[ネットワーク モニター](https://www.microsoft.com/download/details.aspx?id=4865)や Fiddler などのネットワーク キャプチャ ツールを使用できます。

Windows オペレーティング システム イメージを使用する Virtual Machines に関する問題については、[Windows イベント ログ](/windows/win32/wes/windows-event-log)を使用できます。

サービスとしてのプラットフォーム (PaaS) のデプロイの場合は、アプリケーションで [Azure Diagnostics](../articles/cloud-services/cloud-services-dotnet-diagnostics.md) を有効にします。

サービスとしてのインフラストラクチャ (IaaS) のデプロイの場合は、[Windows Communication Foundation のトレース](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing)を有効にします。

[IIS 8.5 用の強化されたログ記録](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85)を有効にします。

[Azure App Service の Web アプリの診断ログを有効にします](../articles/app-service/troubleshoot-diagnostic-logs.md)。

実際の状況に対する詳細とアドバイスについては、Microsoft カスタマー サクセス アカウント マネージャーに問い合わせて、クラウド ソリューション アーキテクトの支援を要請してください。