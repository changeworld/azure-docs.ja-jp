---
title: Azure Security Center とは | Microsoft Docs
description: Azure Security Center の主な機能とそのしくみについて説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 1b0a6c6d6daa686404021afb2e10b25bb9f54191
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319389"
---
# <a name="what-is-azure-security-center"></a>Azure Security Center とは
Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と高度な脅威保護を実現します。 Security Center を使用して、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。

Security Center を使う意義

- **一元化されたポリシー管理** - すべてのハイブリッド クラウド ワークロードのセキュリティ ポリシーを一元的に管理して、会社や規制のセキュリティ要件に確実に準拠できます。
- **継続的なセキュリティ評価** - マシン、ネットワーク、ストレージおよびデータ サービス、アプリケーションのセキュリティ体制を監視して、潜在的なセキュリティの問題を検出します。
- **実行可能な推奨事項** - 優先順位が付けられた実行可能なセキュリティの推奨事項により、攻撃者が悪用する前にセキュリティの脆弱性を修復します。
- **優先順位が付けられたアラートとインシデント** - 優先順位が付けられたセキュリティ アラートとインシデントにより、最も重大な脅威にまず重点を置きます。
- **高度なクラウド防御** - 管理ポートと、VM 上で実行されているアダプティブ アプリケーション制御への Just In Time アクセスに関する脅威を軽減します。
- **統合されたセキュリティ ソリューション** - 接続されたパートナー ソリューションなどのさまざまなソースからセキュリティ データを収集、検索、分析します。

**[Security Center - 概要]** では、Azure と Azure 以外のワークロードのセキュリティ対策を簡単に確認できるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 組み込みのダッシュボードでは、セキュリティ アラートと注意が必要な脆弱性を即時に把握できます。

![概要][1]

## <a name="centralized-policy-management"></a>一元化されたポリシー管理
**[ポリシーとコンプライアンス]** セクション (上の図) には、サブスクリプションのカバレッジ、ポリシーのコンプライアンス、セキュリティ体制に関する概要が表示されます。

### <a name="subscription-coverage"></a>サブスクリプションのカバレッジ
このセクションには、アクセス権 (読み取りまたは書き込み) を持つサブスクリプションの総数と、サブスクリプションが実行されている Security Center のカバレッジ レベル (Standard または無料) が表示されます。

- **対象 (Standard)** - 対象のサブスクリプションは、Security Center で提供されている最大レベルの保護下で実行されています
- **対象 (Free)** - 対象のサブスクリプションは、Security Center で提供されている制限付きの無料レベルの保護下で実行されています
- **該当しません** - この状態のサブスクリプションは、Security Center によって監視されません

グラフを選択すると、**[カバレッジ]** ウィンドウが開きます。 タブ (**[該当しません]**、**[基本カバレッジ]**、または **[Standard カバレッジ]**) を選択すると、各状態のサブスクリプションの一覧が表示されます。 いずれかのタブでサブスクリプションを選択すると、サブスクリプションに関する詳細情報が表示されます。 この情報から、サブスクリプションの所有者を特定して Security Center を有効にするために連絡したり、サブスクリプションのカバレッジを広げたりすることができます。

![Security Center のカバレッジ][9]

### <a name="policy-compliance"></a>ポリシーのコンプライアンス
ポリシーのコンプライアンスは、割り当てられたすべてのポリシーのコンプライアンス要因によって決まります。 管理グループ、サブスクリプション、またはワークスペースの全体的なコンプライアンス スコアは、割り当ての加重平均です。 加重平均では、1 つの割り当てにおけるポリシーの数と、割り当てが適用されるリソースの数が考慮されます。

たとえば、サブスクリプションに 2 つの VM と 5 つのポリシーが割り当てられたイニシアティブがある場合、このサブスクリプションには 10 のアセスメントがあります。 VM の 1 つが 2 つのポリシーに準拠していない場合、サブスクリプションの割り当ての全体的なコンプライアンス スコアは 80% になります。

このセクションには、全体的なコンプライアンス率と最も適合性が低いサブスクリプションが表示されます。 **[環境のポリシー コンプライアンスを表示する]** を選択すると、**[ポリシー管理]** ウィンドウが開きます。 **[ポリシー管理]** には、管理グループ、サブスクリプション、およびワークスペースの階層構造が表示されます。 ここでは、サブスクリプションまたは管理グループを選択してセキュリティ ポリシーを管理します。

![ポリシー管理][10]

セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Security Center では、ポリシーを定義し、Security Center で監視および推奨するコントロールを決定して、ワークロードの種類やデータの機密性に合わせて調整します。 Security Center でセキュリティ ポリシーを編集するには、管理グループまたはサブスクリプションをクリックします。 Azure Policy を使用し、新しい定義の作成、追加のポリシーの定義、管理グループ間でのポリシーの割り当てを行うこともできます。

**[設定の編集 >]** を選択して、サブスクリプション、管理グループ、リソース グループ、またはワークスペース レベルで次の Security Center 設定を編集します。

- **データ収集**: エージェントのプロビジョニングとセキュリティ [データ収集](security-center-enable-data-collection.md)の設定を決定します。
- **電子メール通知**: セキュリティ連絡先と[電子メール通知](security-center-provide-security-contact-details.md)の設定を決定します。
- **価格レベル**: Free または Standard [価格選択](security-center-pricing.md)を定義します。 選択したレベルでは、スコープ内のリソースに使用できる Security Center の機能を決定します。
- **セキュリティ構成の編集**: Security Center によって評価される OS 構成を表示および変更し、潜在的なセキュリティの脆弱性を識別します。

詳細については、「[Security Center のセキュリティ ポリシーを Azure Policy に統合する](security-center-azure-policy.md)」を参照してください。

### <a name="manage-and-govern-your-security-posture"></a>セキュリティ体制の管理と制御
ダッシュボードの右側の **[ポリシーとコンプライアンス]** には、全体的なセキュリティ体制を改善するためにすぐに措置を取ることができる分析情報が表示されます。 次に例をいくつか示します。

- セキュリティ標準へのコンプライアンスを検討して追跡するために、Security Center のポリシーを定義し、割り当てる
- SIEM コネクタに対して Security Center セキュリティ アラートを使用できるようにする
- 経時的なポリシー コンプライアンス

## <a name="continuous-security-assessment"></a>継続的なセキュリティ評価
**[Security Center - 概要]** の [リソース セキュリティの検疫] セクションには、特定された問題の数と各リソースの種類のセキュリティ状態が表示され、リソースのセキュリティの検疫の概要を確認できます。 継続的な評価を使用して、セキュリティ更新プログラムが不足しているシステムや公開されているネットワーク ポートなど、潜在的なセキュリティの問題を検出できます。

### <a name="secure-score"></a>セキュリティ スコア
Azure Security Center のセキュリティ スコアは、セキュリティに関する推奨事項をレビューし、優先順位を設定するので、最初に実行する推奨事項を把握できます。これは、最も重大なセキュリティの脆弱性を探して調査の優先順位を付けるのに役立ちます。 セキュリティ スコアは、セキュリティで保護されたワークロードを実現するためにセキュリティを強化するのに役立つ測定ツールです。 詳細については、[Azure Security Center のセキュリティ スコア](security-center-secure-score.md)に関するページを参照してください。

### <a name="health-monitoring"></a>正常性の監視
**[リソース正常性の監視]** でリソースの種類を選択すると、リソースと特定された脆弱性の一覧が表示されます。 リソースの種類は、計算とアプリケーション、ネットワーク、データとストレージ、ID とアクセスです。

この例では、**[計算とアプリ]** を選択しました。 **[Compute]** には、次の 4 つのタブがあります。

- **[概要]**: Security Center によって特定された監視および推奨事項。
- **[VM およびコンピューター]**: VM およびコンピューターの一覧と、それぞれの現在のセキュリティ状態。
- **[Cloud Services]\(クラウド サービス\)**: Security Center によって監視されている Web および worker ロールの一覧。
- **[App Services (プレビュー)]**: Web アプリケーションとアプリ サービス環境の一覧と、それぞれの現在のセキュリティ状態。

![セキュリティ正常性の監視][3]

詳細については、[セキュリティ正常性の監視](security-center-monitoring.md)に関するページを参照してください。

## <a name="actionable-recommendations"></a>実行可能な推奨事項
Security Center は Azure リソースと Azure 以外のリソースのセキュリティの状態を分析して、潜在的なセキュリティ脆弱性を特定します。 **[リソース]** の **[推奨事項]** を選択すると、セキュリティ問題に対処するプロセスを案内する、優先順位が付けられたセキュリティの推奨事項の一覧が表示されます。

![Recommendations][4]

詳細については、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)に関するページを参照してください。

### <a name="most-prevalent-recommendations"></a>最も一般的な推奨事項
ダッシュボードの右側の **[リソース]** には、最も多数のリソースに存在する最も一般的な推奨事項の一覧が表示されます。 最も一般的な推奨事項では、注意が必要な個所が強調表示されます。 右矢印を選択すると、最も影響度が高い推奨事項が表示されます。

![最も一般的な推奨事項][11]

これは、環境内で最も影響度が高い 1 つの推奨事項です。 この推奨事項を解決すると、コンプライアンスが最も向上します。 この例の推奨事項は、[ディスク暗号化を適用する] です。 **[コンプライアンスを改善する]** を選択すると、推奨事項の説明と影響を受けるリソースの一覧が表示されます。

![ディスク暗号化の適用][12]

## <a name="threat-protection"></a>脅威の防止
この領域では、リソースに対して検出されたセキュリティ アラートと、そのアラートの重大​​度を確認できます。

### <a name="prioritized-alerts-and-incidents"></a>優先順位が付けられたアラートとインシデント
Security Center は、高度な分析とグローバル脅威インテリジェンスを使用して攻撃や侵害後のアクティビティを検出します。 アラートは優先順位が付けられてインシデントにグループ化されるので、最も重大な脅威にまず重点を置くことができます。 独自のカスタム セキュリティ アラートも作成できます。

**[セキュリティ アラート (重要度別)]** または **[セキュリティ アラート (時系列)]** を選択すると、アラートに関する詳細情報が表示されます。

![優先順位が付けられたアラートとインシデント][7]

ビジュアルな対話形式の調査エクスペリエンスで攻撃のスコープと影響を迅速に評価し、定義済みまたはアドホック クエリを使用してセキュリティ データを詳しく調査できます。

詳細については、[セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)に関するページを参照してください。

ダッシュボードの右側には、最初に対処すべきアラートの優先順位を付け、一般的な脆弱性がある場所を理解するために役立つ情報が表示されます。

- **最も攻撃を受けたリソース**: アラート数が最も多い特定のリソース
- **最も一般的なアラート**: 最も多数のリソースに影響するアラートの種類

## <a name="just-in-time-vm-access"></a>Just In Time VM アクセス
Azure VM の管理ポートへの制御された Just In Time アクセスによってネットワーク攻撃対象領域を縮小することで、ブルート フォース攻撃などのネットワーク攻撃にさらされる機会を大幅に減らします。

![Just In Time VM アクセス][5]

ユーザーが仮想マシンに接続する方法についての規則を指定します。 必要な場合は、Security Center から、または PowerShell を使用してアクセスを要求することができます。 要求が規則に準拠している限り、要求された時間に対してアクセス権が自動的に付与されます。

詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](security-center-just-in-time.md)」を参照してください。

## <a name="adaptive-application-controls"></a>アダプティブ アプリケーション制御
特定の Azure ワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。

![アダプティブ アプリケーション制御][6]

Security Center によって生成された推奨されるアプリケーション ホワイトリスト規則を確認し、クリックして適用するか、既に構成されている規則を編集します。

詳細については、[アダプティブ アプリケーション制御](security-center-adaptive-application.md)に関するページを参照してください。

## <a name="integrate-your-security-solutions"></a>セキュリティ ソリューションの統合
Security Center で、接続されたパートナー ソリューション (ネットワーク ファイアウォールなど) や他の Microsoft サービスなど、さまざまなソースからセキュリティ データを収集、検索、分析できます。

![セキュリティ ソリューションの統合][8]

詳細については、[セキュリティ ソリューションの統合](security-center-partner-integration.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Security Center を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- Security Center の Free 価格レベルは、Azure サブスクリプションがある場合に有効です。 高度なセキュリティ管理と脅威検出の機能を利用するには、Standard 価格レベルにアップグレードする必要があります。 Standard レベルは、最初の 60 日間は無料です。 詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」ページをご覧ください。
- Security Center Standard を有効にする準備ができている場合は、「[Azure Security Center クイック スタート ガイド](security-center-get-started.md)」で手順をご覧ください。


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
