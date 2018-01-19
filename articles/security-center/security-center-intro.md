---
title: "Azure Security Center とは | Microsoft Docs"
description: "Azure Security Center の主な機能とそのしくみについて説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 50a54b8d2a73807aa9a0217f7ccf971b8c516494
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2018
---
# <a name="what-is-azure-security-center"></a>Azure Security Center とは
Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Security Center を使用して、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。

Security Center を使う意義

- **一元化されたポリシー管理** - すべてのハイブリッド クラウド ワークロードのセキュリティ ポリシーを一元的に管理して、会社や規制のセキュリティ要件に確実に準拠できます。
- **継続的なセキュリティ評価** - マシン、ネットワーク、ストレージおよびデータ サービス、アプリケーションのセキュリティを監視して、潜在的なセキュリティの問題を検出します。
- **実行可能な推奨事項** - 優先順位が付けられた実行可能なセキュリティの推奨事項により、攻撃者が悪用する前にセキュリティの脆弱性を修復します。
- **高度なクラウド防御** - 管理ポートとホワイトリストへの Just In Time アクセスで、VM で実行されているアプリケーションをコントロールして、脅威を軽減します。
- **優先順位が付けられたアラートとインシデント** - 優先順位が付けられたセキュリティ アラートとインシデントにより、最も重大な脅威にまず重点を置きます。
- **統合されたセキュリティ ソリューション** - 接続されたパートナー ソリューションなどのさまざまなソースからセキュリティ データを収集、検索、分析します。

**[Security Center - 概要]** では、Azure と Azure 以外のワークロードのセキュリティ対策を簡単に確認できるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 組み込みのダッシュボードでは、セキュリティ アラートと注意が必要な脆弱性を即時に把握できます。

![概要][1]

## <a name="centralized-policy-management"></a>一元化されたポリシー管理
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Security Center では、ポリシーを定義し、ワークロードの種類やデータの機密性に合わせて調整します。

Security Center のポリシーには、次のコンポーネントが含まれています。

- **データ収集**: エージェントのプロビジョニングとセキュリティ [データ収集](security-center-enable-data-collection.md)の設定を決定します。
- **セキュリティ ポリシー**: [セキュリティ ポリシー](security-center-policies.md)を編集して、Security Center がどのコントロールを監視し、推奨するかを決定します。
- **電子メール通知**: セキュリティ連絡先と[電子メール通知](security-center-provide-security-contact-details.md)の設定を決定します。
- **価格レベル**: Free または Standard [価格選択](security-center-pricing.md)を定義します。 選択したレベルでは、スコープ内のリソースに使用できる Security Center の機能を決定します。

![セキュリティ ポリシー][2]

詳しくは、「[セキュリティ ポリシーの概要](security-center-policies-overview.md)」をご覧ください。

## <a name="continuous-security-assessment"></a>継続的なセキュリティ評価
Security Center は、コンピューティング リソース、仮想ネットワーク、ストレージおよびデータ サービス、アプリケーションのセキュリティの状態を分析します。 継続的な評価を使用して、セキュリティ更新プログラムが不足しているシステムや公開されているネットワーク ポートなど、潜在的なセキュリティの問題を検出できます。 [防止] セクションのタイルを選択して、リソースの一覧と特定済みの脆弱性など、詳しい情報を表示します。

![セキュリティ正常性の監視][3]

詳しくは、[セキュリティ正常性の監視](security-center-monitoring.md)に関するページをご覧ください。

## <a name="actionable-recommendations"></a>実行可能な推奨事項
Security Center は Azure リソースと Azure 以外のリソースのセキュリティの状態を分析して、潜在的なセキュリティ脆弱性を特定します。 優先順位が設定されたセキュリティに関する推奨事項の一覧では、セキュリティの問題に対処するプロセスが説明されます。

![Recommendations][4]

詳しくは、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)に関する記事をご覧ください。

## <a name="just-in-time-vm-access"></a>Just In Time VM アクセス
Azure VM の管理ポートへの制御された Just In Time アクセスによってネットワーク攻撃対象領域を縮小することで、ブルート フォース攻撃などのネットワーク攻撃にさらされる機会を大幅に減らします。

![Just In Time VM アクセス][5]

ユーザーが仮想マシンに接続する方法についての規則を指定します。 必要な場合は、Security Center から、または PowerShell を使用してアクセスを要求することができます。 要求が規則に準拠している限り、要求された時間に対してアクセス権が自動的に付与されます。

詳しくは、[Just In Time を使用した仮想マシン アクセスの管理](security-center-just-in-time.md)に関する記事をご覧ください。

## <a name="adaptive-application-controls"></a>アダプティブ アプリケーション制御
特定の Azure ワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。

![アダプティブ アプリケーション制御][6]

Security Center によって生成された推奨されるアプリケーション ホワイトリスト規則を確認し、クリックして適用するか、既に構成されている規則を編集します。

詳しくは、[アダプティブ アプリケーション制御](security-center-adaptive-application.md)に関する記事をご覧ください。

## <a name="prioritized-alerts-and-incidents"></a>優先順位が付けられたアラートとインシデント
Security Center は、高度な分析とグローバル脅威インテリジェンスを使用して攻撃や侵害後のアクティビティを検出します。 アラートは優先順位が付けられてインシデントにグループ化されるので、最も重大な脅威にまず重点を置くことができます。 独自のカスタム セキュリティ アラートも作成できます。

![優先順位が付けられたアラートとインシデント][7]

ビジュアルな対話形式の調査エクスペリエンスで攻撃のスコープと影響を迅速に評価し、定義済みまたはアドホック クエリを使用してセキュリティ データを詳しく調査できます。

詳しくは、[セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)に関するページをご覧ください。

## <a name="integrate-your-security-solutions"></a>セキュリティ ソリューションの統合
Security Center で、接続されたパートナー ソリューション (ネットワーク ファイアウォールなど) や他の Microsoft サービスなど、さまざまなソースからセキュリティ データを収集、検索、分析できます。

![セキュリティ ソリューションの統合][8]

詳しくは、[セキュリティ ソリューションの統合](security-center-partner-integration.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

- Security Center を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- Security Center の Free 価格レベルは、Azure サブスクリプションがある場合に有効です。 高度なセキュリティ管理と脅威検出の機能を利用するには、Standard 価格レベルにアップグレードする必要があります。 Standard レベルは、最初の 60 日間は無料です。 詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」ページをご覧ください。
- Security Center Standard を有効にする準備ができている場合は、「[Azure Security Center クイック スタート ガイド](security-center-get-started.md)」で手順をご覧ください。


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
