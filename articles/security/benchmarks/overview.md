---
title: Azure セキュリティ ベンチマークの概要
description: セキュリティ ベンチマークの概要
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: de6bbb81b04b93cce8ab7ddaf1d983cb7e4a700d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990591"
---
# <a name="overview-of-azure-security-controls"></a>Azure セキュリティ制御の概要

Azure セキュリティ ベンチマークには、Azure 上のアプリケーションとデータのセキュリティを向上させるための推奨事項が含まれています。

このベンチマークの対象は、クラウド中心のコントロール領域です。 これらのコントロールは、Center for Internet Security (CIS) コントロール バージョン 7.1 で説明されているような、よく知られたセキュリティ ベンチマークと一貫性があります。

Azure セキュリティ ベンチマークでは、次のコントロールが使用されます。 

- [ネットワークのセキュリティ](security-control-network-security.md)
- [ログ記録と監視](security-control-logging-monitoring.md)
- [ID およびアクセス制御](security-control-identity-access-control.md)
- [データ保護](security-control-data-protection.md)
- [脆弱性の管理](security-control-vulnerability-management.md)
- [インベントリと資産の管理](security-control-inventory-asset-management.md)
- [セキュリティで保護された構成](security-control-secure-configuration.md)
- [マルウェアからの防御](security-control-malware-defense.md)
- [データの復旧](security-control-data-recovery.md)
- [インシデント対応](security-control-incident-response.md)
- [侵入テストとレッド チーム演習](security-control-penetration-tests-red-team-exercises.md)

[Azure セキュリティ ベンチマーク v1 の Excel スプレッドシート](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)をダウンロードすることもできます。

## <a name="azure-security-benchmark-recommendations"></a>Azure セキュリティ ベンチマークの推奨事項 

各推奨事項には次の情報が含まれます。 

- **Azure ID**: 推奨事項に対応する Azure セキュリティ ベンチマーク ID。 
- **CIS ID**: この推奨事項に対応する CIS ベンチマークの推奨事項。  
- **責任**: お客様またはサービス プロバイダー (またはその両方) が、この推奨事項の実装に責任があるかどうか。 セキュリティ責任は、パブリック クラウドで共有されます。 一部のセキュリティ コントロールはクラウド サービス プロバイダーのみが使用できるため、プロバイダーはそれらに対処する必要があります。 これらは一般的な見解です。個々のサービスによっては、責任が Azure セキュリティ ベンチマークに記載されているものと異なることがあります。 これらの違いについては、個々のサービスのベースラインの推奨事項で説明されています。 
- **[詳細]** :推奨事項の根拠と、その実装方法に関するガイダンスへのリンク。 推奨事項が Azure Security Center によってサポートされている場合は、その情報も一覧表示されます。

Azure セキュリティ ベンチマークの取り組みに関するフィードバックと積極的な参加をお待ちしております。 ベンチマーク チームに直接情報を提供したい場合は、[https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark) のフォームに入力してください。

## <a name="next-steps"></a>次の手順

- 最初セキュリティ コントロールを参照してください。[ネットワークのセキュリティ](security-control-network-security.md)
- 「[Azure セキュリティ ベンチマークの概要](introduction.md)」を読む
- [Azure セキュリティ ベンチマーク v1 の Excel スプレッドシート](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)をダウンロードする
