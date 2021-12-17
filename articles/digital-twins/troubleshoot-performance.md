---
title: 'トラブルシューティング: パフォーマンス'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins インスタンスのパフォーマンスのトラブルシューティングに関するヒント。
author: baanders
ms.author: baanders
ms.date: 10/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 67f182ffbaf439d2c6b592854156073d6104efa7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131431"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>Azure Digital Twins のトラブルシューティング: パフォーマンス

Azure Digital Twins の使用中に遅延またはその他のパフォーマンスに関する問題が発生している場合は、このアーティクルのヒントを参照してトラブルシューティングを行ってください。

## <a name="isolate-the-source-of-the-delay"></a>遅延の原因を特定する

遅延が Azure Digital Twins またはソリューション内の別のサービスによって発生しているかどうかを判断します。 この遅延を調査するには、Azure portal から [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md) の **API 待機時間** メトリックを使用できます。 Azure Digital Twins インスタンスに対する Azure Monitor メトリック表示する方法については、[トラブルシューティング: メトリック](troubleshoot-metrics.md)に関する記事を参照してください。

## <a name="check-regions"></a>リージョンを確認する

ソリューションが Azure Digital Twins を他の Azure サービス (Azure Functions など) と組み合わせて使用している場合は、各サービスのデプロイのリージョンを確認します。 サービスが別々のリージョンにデプロイされている場合、ソリューション全体で遅延が発生する可能性があります。 意図的に分散ソリューションを作成していない場合は、誤って遅延が発生しないように、すべてのサービス インスタンスを同じリージョン内にデプロイすることを検討してください。

## <a name="check-logs"></a>ログを確認する

Azure Digital Twins を使用すると、サービス インスタンスのログを収集でき、その他のデータの中でもパフォーマンスを監視するのに役立ちます。 ログは [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) またはカスタムのストレージ メカニズムに送信できます。 インスタンスでのログ記録を有効にするには、[トラブルシューティング: 診断ログ](troubleshoot-diagnostics.md)に関する記事の手順に従ってください。 ログのタイムスタンプを分析して、待機時間を測定し、それらに一貫性があるかどうかを評価することで、原因を確認できます。

## <a name="check-api-frequency"></a>API の頻度を確認する

パフォーマンスに影響する可能性のあるもう 1 つの要因は、API 呼び出しの再承認にかかる時間です。 API 呼び出しの頻度を検討します。 呼び出しの間隔が 15 分を超えている場合は、システムが呼び出しごとに再承認を行い、追加の時間がかかっている場合があります。 この問題を回避するには、コードにタイマーまたは同等のものを追加して、少なくとも 15 分に 1 回は Azure Digital Twins を呼び出すようにします。

## <a name="contact-support"></a>サポートにお問い合せください

上記の手順でトラブルシューティングを行った後もパフォーマンスの問題が発生する場合は、[Azure ヘルプとサポート] からサポート リクエストを作成して、トラブルシューティングに関するその他のサポートを受けることができます。 

次の手順に従います。

1. インスタンスの[メトリック](troubleshoot-metrics.md)と[ログ](troubleshoot-diagnostics.md)を収集します。
2. Azure portal で [[Azure ヘルプとサポート]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動します。 プロンプトに従って、問題の詳細を入力し、推奨される解決策を確認し、メトリックやログ ファイルを共有し、サポート チームが問題の調査に使用できるその他の情報を送信します。 サポート リクエストの作成については、「[Azure サポート リクエストを作成する](../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Digital Twins インスタンスのトラブルシューティングを行うその他の方法については、次の記事を参照してください。
* [トラブルシューティング: メトリック](troubleshoot-metrics.md)
* [トラブルシューティング: 診断ログ](troubleshoot-diagnostics.md)
* [トラブルシューティング: アラート](troubleshoot-alerts.md)
* [トラブルシューティング: リソースの正常性](troubleshoot-resource-health.md)
