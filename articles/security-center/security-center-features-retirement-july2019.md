---
title: Security Center の機能の廃止 (2019 年 7 月) | Microsoft Docs
description: この記事では、2019 年 7 月 31 日に終了する Security Center の機能について説明します。
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 614dabe842c7fe99da3ddb486c27003c79382ea1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231396"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Security Center の機能の廃止 (2019 年 7 月)

マイクロソフトでは、過去 6 か月にわたって、Azure Security Center に対していくつかの[強化策](https://azure.microsoft.com/updates/?product=security-center)を実施してきました。  
機能の強化に伴い、2019 年 7 月 31 日をもって、複数の冗長な機能や関連する API を Security Center から削除する予定です。  

廃止になる機能のほとんどは、Azure Security Center または Log Analytics 内の新しい機能に置き換えることができます。また、いくつかの機能は、先日発表された [Azure Sentinel (プレビュー)](https://azure.microsoft.com/services/azure-sentinel/) を使用して実装することが可能です。

Security Center から廃止される機能の一覧は、次のとおりです。

- [イベント ダッシュボード](#menu_events)
- [メニュー項目の検索](#menu_search)
- [[ID およびアクセス] にある [クラシック ID とアクセスの表示] (プレビュー)](#menu_classicidentity)
- [[セキュリティ アラート マップ] にある [セキュリティ イベント マップ] ボタン (プレビュー)](#menu_securityeventsmap)
- [カスタムのアラート ルール (プレビュー)](#menu_customalerts)
- [[脅威の防止] セキュリティ アラートにある [調査] ボタン](#menu_investigate)
- [セキュリティ ソリューションのサブセット](#menu_solutions)
- [セキュリティ ポリシーに対するセキュリティ構成の編集](#menu_securityconfigurations)
- [Log Analytics ワークスペースに対するセキュリティおよび監査のダッシュボード (元は OMS ポータルで使用されていた)](#menu_securityomsdashboard)

以下に、廃止される各機能の詳細情報と、置き換えられた機能を使用して実行できる手順を示します。

## イベント ダッシュボード<a name="menu_events"></a>
Security Center は、Microsoft Monitoring Agent を使用して、さまざまなセキュリティ関連の構成とイベントをマシンから収集し、ワークスペースにこれらのイベントを格納します。 [イベント ダッシュ ボード](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)では、このデータを表示でき、基本的に Log Analytics への別のエントリ ポイントを提供します。

今後は、イベント ダッシュ ボードは廃止されます。

![イベント ワークスペースの選択画面][1]

ユーザーがワークスペースをクリックすると表示されるイベント ダッシュボードも、廃止されます。

![イベント ダッシュボード][2]

### <a name="events-dashboard---new-experience"></a>イベント ダッシュボード - 新しいエクスペリエンス

お客様は、Log Analytics のネイティブ機能を使用して、それらのワークスペース上で注目すべきイベントを閲覧することをお勧めします。
Security Center からカスタムの注目すべきイベントを既に作成している場合、[Log Analytics] -> [ワークスペースの選択] -> [保存された検索] から、これらにアクセスできます。 データが失われたり、変更されたりすることはありません。 ネイティブの注目すべきイベントも、同じ画面から使用できます。

![保存された検索のワークスペース][3]

## メニュー項目の検索<a name="menu_search"></a>
Azure Security Center では現在、Azure Monitor ログ検索を使用してセキュリティ データが取得され、分析されます。 この画面は基本的に、Log Analytics の [[検索](https://docs.microsoft.com/azure/security-center/security-center-search)] ページへのファサードの役割を担い、これによってユーザーは選択したワークスペース上で検索クエリを実行できます。 今後は、この検索ウィンドウは廃止されます。

![Search page][4]

### <a name="search-menu-entry---new-experience"></a>メニュー項目の検索 - 新しいエクスペリエンス

お客様は、**Log Analytics** のネイティブ機能を使用して、それらのワークスペース上で検索クエリを実行することをお勧めします。 これを行うには、Azure 内で Log Analytics に移動して、次を選択します:[ログ]。

![Log Analytics の [ログ] のページ][5]

## クラシック ID とアクセス (プレビュー)<a name="menu_classicidentity"></a>
Security Center での "クラシック" ID とアクセスのエクスペリエンスでは、ログの分析における ID とアクセス関連の情報をお客様が表示するための 1 つの方法を提供していました。 これは、以下のクリックから行われていました。

[クラシック ID とアクセスの表示] をクリックする

![ID ページ][6]

画面に従って、このページに [ID とアクセスのダッシュボード] が開かれます。

![ID ページ - ワークスペースの選択][7]

ワークスペースをクリックすると、[ID とアクセス] のログ分析のダッシュボードが開き、お客様は自身のワークスペース上にある ID とアクセスの情報を閲覧できます。

![ID ページ - ダッシュボード][8]

上の 3 つの画面はすべて、今後は廃止されます。 データは、ログ分析のセキュリティ ソリューション内で引き続き使用でき、変更または削除されることはありません。
[ID およびアクセス] ダッシュボードの設定に使用していた元の Log Analytics クエリは、Security Center の [GitHub リポジトリ](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)で確認できます。

### <a name="classic-identity--access-preview---new-experience"></a>クラシック ID とアクセス (プレビュー) - 新しいエクスペリエンス
ログ分析のダッシュボードでは、指定されたワークスペース上のみで分析情報を提供してきましたが、ネイティブな Security Center 機能では、[ID とアクセス] の推奨事項の安全スコアに従って重要な箇所への注目を促す使いやすいビューの中で、すべてのサブスクリプションとそれらに関連付けられたすべてのワークスペースへの視覚化を提供します。
[ID とアクセス] のログ分析のダッシュボードにあるすべての機能へは、Security Center 内にある [ID とアクセス (プレビュー)] を選択してアクセスすることが可能です。

![ID ページ - 従来のエクスペリエンスの廃止][9]

## セキュリティ イベント マップ<a name="menu_securityeventsmap"></a>
Security Center では、その環境に対するセキュリティ上の脅威を特定するのに役立つ[マップ](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)を提供しています。 該当のマップの [セキュリティ イベント マップに移動] ボタンから、選択したワークスペース上に未加工のセキュリティ イベントを表示できるダッシュボードへ移動できます。
非推奨になった後は、ボタンはワークスペースごとのダッシュボードと共に削除されます。

![[セキュリティ アラート マップ] - ボタン][10]

現時点では、[セキュリティ イベント マップに移動] をクリックすると、脅威インテリジェンスのダッシュボードが開かれます。 脅威インテリジェンスのダッシュボードは廃止されます。  

![[脅威インテリジェンス] ダッシュボード][11]

[脅威インテリジェンス] ダッシュ ボードを表示するワークスペースを選択すると、*Log Analytics* に [セキュリテ アラート マップ] ( プレビュー) の画面が開きます。 この画面は廃止されます。

![Log Analytics にある [セキュリティ アラート マップ]][12]

既存のデータは、ログ分析のセキュリティ ソリューションで引き続き使用でき、変更または削除されることはありません。
[脅威インテリジェンス] ダッシュボードの設定に使用していた元の Log Analytics クエリは、Security Center の [GitHub リポジトリ](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)で確認できます。

### <a name="security-events-map---new-experience"></a>セキュリティ イベント マップ - 新しいエクスペリエンス
お客様は、Security Center に組み込まれたアラート マップ機能である [セキュリティ アラート マップ (プレビュー)] を使用することをお勧めします。 これは最適化されたエクスペリエンスを提供し、すべてのサブスクリプションと関連するワークスペース全体に有効です。単一のワークスペースに重点を置かない、お使いの環境全体でのマクロ ビューを実現します。

## カスタムのアラート ルール (プレビュー)<a name="menu_customalerts"></a>
カスタムのアラート エクスペリエンスは、構築されていた基になるインフラストラクチャが廃止されることから、2019 年 6 月 30 日に[廃止](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)される予定です。 非推奨となるまでの期間、ユーザーは、既存のカスタム アラート ルールを編集することはできますが、新しいものを追加することはできません。 ユーザーは、ワンクリック オンボードで、[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) を有効にし、既存のアラートを自動的に移行し、新しいアラートを作成するか、または Azure Monitor ログ アラートでアラートを再作成することをお勧めします。

既存のアラートを保持し、それらを Azure Sentinel に移行するには、Azure Sentinel を起動してください。 最初の手順で、カスタム アラートが格納されているワークスペースを選択し、[分析] メニュー項目を選択して、アラートを自動的に移行します。

![カスタム アラート][13]

Azure Sentinel へのオンボードに関心のないお客様は、Azure Monitor のログ アラートを使ってアラートを再作成することをお勧めします。 詳しくは、「 [Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)」をご覧ください。 ログ アラートの作成方法の手順については、次をご覧ください:「[Azure Monitor でのログ アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)」。

カスタム アラートの廃止について詳しくは、[Security Center のカスタム アラートのドキュメント](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)をご覧ください。

## セキュリティ アラートの調査<a name="menu_investigate"></a>
Security Center にある[調査機能](https://docs.microsoft.com/azure/security-center/security-center-investigation)を使用すると、潜在的なセキュリティ インシデントについて、トリアージ、範囲の把握、根本原因の特定を行うことできます。 この機能は、[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) において強化されたエクスペリエンスに置き換えられるため、Security Center からは削除されます。

![セキュリティ インシデント][14]

上記の [調査] ボタンをクリックすると、Log Analytics にある [調査のダッシュボード (プレビュー)] が開きます。 調査のダッシュボードは廃止されます。  
既存のデータは、Log Analytics のセキュリティ ソリューションで引き続き使用でき、変更または削除されることはありません。

![Log Analytics にある調査のダッシュボード][15]

### <a name="investigation---new-experience"></a>調査 - 新しいエクスペリエンス

お客様は、豊富な調査エクスペリエンスのために、アラート検出機能が強化されている [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) をオンボードすることをお勧めします。 Azure Sentinel では、組織のデータ ソースにわたってセキュリティ上の脅威を検出するために、検出機能を強化した検索とクエリのツールを提供しています。  

## セキュリティ ソリューションのサブセット<a name="menu_solutions"></a>

Security Center には、[Azure 内の統合されたセキュリティ ソリューション](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)を有効にする機能があります。 次のパートナー ソリューションは削除され、さらに多くのデータ ソースと共に [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) でサポートされる予定です。

- 次世代ファイアウォールおよび Web アプリケーション ファイアウォールのソリューション (Azure Sentinel の[ドキュメント](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- 共通イベント形式 (CEF) をサポートするセキュリティ ソリューションの統合 (Azure Sentinel の[ドキュメント](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure Sentinel の[ドキュメント](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel の[ドキュメント](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

非推奨になった後は、ユーザーは UI および API のどちらからも、上記に説明した種類の接続済みソリューションを新しく追加したり、既存のものを変更したりできなくなります。
既存の接続済みソリューションは、この期間が終わる前に Azure Sentinel に移行することをお勧めします。

![Security Center ソリューション][16]

## セキュリティ ポリシーに対するセキュリティ構成の編集<a name="menu_securityconfigurations"></a>
Azure Security Center は、ファイアウォール、監査、パスワード ポリシーなどに関連する規則を含め、OS を強化するための [150 を超える推奨の規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)のセットを適用して、セキュリティ構成を監視します。 脆弱な構成を持つマシンが見つかった場合、Security Center はセキュリティ推奨事項を生成します。 お客様は、[[セキュリティ構成の編集] 画面](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)を利用して、Security Center にある既定の OS セキュリティ構成をカスタマイズできます。

この機能はプレビュー段階であり、廃止が予定されています。

![セキュリティ構成の編集][17]

### <a name="edit-security-configurations---new-experience"></a>セキュリティ構成の編集 - 新しいエクスペリエンス

Security Center では近い将来、[ゲスト構成エージェント](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)をサポートし、Azure ポリシーによるゲスト構成 (ゲスト内ポリシー) を利用した追加のオペレーティング システムと統合のサポートなど、これまでよりもさらに豊富な機能を実現する予定です。 また、これによって大規模な制御機能を提供して、新しいリソースに自動的に適用する予定です。

## <a name="menu_securityomsdashboard">Log Analytics ワークスペースに対するセキュリティおよび監査のダッシュボード (元は OMS ポータルで使用されていた)</a>

Log Analytics にあるセキュリティ ダッシュボードでは、注目すべきセキュリティ イベントと脅威、脅威インテリジェンス マップ、およびワークスペースに保存されたセキュリティ イベントの ID とアクセスの評価の概要を、ワークスペースごとに提供します。 今後は、ダッシュボードは削除されます。 ダッシュボードの UI で既にお勧めしたように、ユーザーは今後、Azure Security Center の使用を促されます。

![Log Analytics にあるセキュリティ ダッシュ ボード][18]

### <a name="security--audit-dashboard---new-experience"></a>セキュリティおよび監査のダッシュボード - 新しいエクスペリエンス
お客様は、Azure Security Center を使用することをお勧めします。豊富な機能セットと共に、複数のサブスクリプションとそれに関連付けられたワークスペース全体に同じセキュリティ概要が提供されます。

[セキュリティおよび監査] ダッシュボードの設定に使用していた元の Log Analytics クエリは、Security Center の [GitHub リポジトリ](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)で確認できます。

## <a name="next-steps"></a>次の手順
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/) の詳細について参照してください
- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel) の詳細について参照してください

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
