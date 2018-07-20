---
title: Log Analytics のサービス プロバイダー向け機能 | Microsoft Docs
description: Log Analytics はマネージド サービス プロバイダー (MSP)、大企業、独立系ソフトウェア ベンダー (ISV) に貢献し、ホスティング サービス プロバイダーが、顧客のオンプレミス型またはクラウド型インフラストラクチャのサーバーを管理および監視する上で役立ちます。
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: meirm
ms.component: na
ms.openlocfilehash: ad0a3b8e0ee5f1114ea1db95cfe2f4176b8e2ddb
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931992"
---
# <a name="log-analytics-for-service-providers"></a>サービス プロバイダー向け Log Analytics
Log Analytics はマネージド サービス プロバイダー (MSP)、大企業、独立系ソフトウェア ベンダー (ISV) に貢献し、ホスティング サービス プロバイダーが、顧客のオンプレミス型またはクラウド型インフラストラクチャのサーバーを管理および監視する上で役立ちます。 

特に多くの異なる部署の IT 管理を担当する一元的な IT チームがある大企業では、サービス プロバイダーと多くの類似点を共有します。 このドキュメントでは簡単にするために*サービス プロバイダー* という言葉を使用していますが、企業およびその他のお客様にも同じ機能をご利用いただけます。

[クラウド ソリューション プロバイダー (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) プログラムに参加しているパートナーやサービス プロバイダーにとって、Log Analytics は [Azure CSP サブスクリプション](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)で使用できる Azure サービスの 1 つです。 

## <a name="architectures-for-service-providers"></a>サービス プロバイダー向けアーキテクチャ

Log Analytics ワークスペースは、ログのフローと分離を制御し、特定のビジネス ニーズに対処するログ アーキテクチャを作成するための方法を管理者にもたらします。 [この記事](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access)では、ワークスペースの管理に関する一般的な考慮事項について説明します。 サービス プロバイダーには追加の考慮事項があります。

Log Analytics ワークスペースに関するサービス プロバイダー向けのアーキテクチャは次の 3 つ考えられます。

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1.分散 - ログは、顧客のテナントに置かれたワークスペースに格納されます 

このアーキテクチャでは、ワークスペースは、顧客のテナント (その顧客のすべてのログに使用) にデプロイされます。 サービス プロバイダーの管理者は、[Azure Active Directory ゲスト ユーザー (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b) を使用したこのワークスペースへのアクセスが認められています。 サービス プロバイダーの管理者は、これらのワークスペースにアクセスするために、Microsoft Azure portal でその顧客のディレクトリに切り替える必要があります。

このアーキテクチャの利点は次のとおりです。
* 顧客は、自身の[ロールベース アクセス](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview)を使用してログへのアクセスを管理できます。
* 保有期間やデータ キャッピングなど、顧客ごとに異なるワークスペース設定が可能です。
* 規制やコンプライアンスについて顧客間で分離します。
* 各ワークスペースの料金は、顧客のサブスクリプションにまとめられます。
* ログは、リソースのすべてのタイプから収集でき、単なるエージェントベースではできません。 たとえば、Azure 監査です。

このアーキテクチャの短所は次のとおりです。
* 一度に多数の顧客のテナントをサービス プロバイダーが管理することが困難になります。
* サービス プロバイダーの管理者は、顧客のディレクトリでプロビジョニングされている必要があります。
* サービス プロバイダーは、顧客間でデータを分析することはできません。

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2.中央 - ログは、サービス プロバイダーのテナントに置かれているワークスペースに格納されます

このアーキテクチャでは、ログは、顧客のテナントに格納されませんが、サービス プロバイダーのいずれかのサブスクリプション内にある中央の場所にのみ格納されます。 顧客の VM 上にインストールされたエージェントは、ワークスペース ID と秘密鍵を使用してこのワークスペースにログを送信するように構成されています。

このアーキテクチャの利点は次のとおりです。
* 多数の顧客を管理して、さまざまなバックエンド システムに統合するときに簡単に行えます。
* サービス プロバイダーは、ログと、関数や保存クエリなどの成果物に対する完全な所有権を持ちます。
* サービス プロバイダーは、すべての顧客の分析を実行できます。

このアーキテクチャの短所は次のとおりです。
* このアーキテクチャは、エージェント ベースの VM データにのみ適用可能で、PaaS、SaaS、Azure ファブリック データ ソースには対応しません。
* 顧客間のデータは、単一のワークスペースにマージされると、分離することが困難になる可能性があります。 これを行う唯一の適切な方法は、コンピューターの完全修飾ドメイン名 (FQDN) を使用するか、または Azure サブスクリプション ID を介するというものです。 
* すべてのお客様からのすべてのデータは、単一の請求書と、同じ保有期間および構成設定を伴う同じリージョンに格納されます。
* Microsoft Azure 診断や Azure 監査などの Azure のファブリックおよび PaaS サービスは、ワークスペースがリソースと同じテナントにあることを必要とするため、中央のワークスペースにログを送信することができません。
* すべての顧客からのすべての VM エージェントは、同じワークスペース ID とキーを使用して中央のワークスペースで認証されます。 他の顧客を中断せずに特定の顧客からのログをブロックする方法はありません。


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>手順 3.ハイブリッド - ログは、顧客のテナントに置かれているワークスペースに格納され、その一部は中央の場所にプルされます。

3 番目のアーキテクチャは、2 つのオプションを組み合わせたものです。 これは、ログが各顧客の元でローカルに格納されるという最初の分散アーキテクチャに基づきながら、何らかのメカニズムを使用して、ログの中央のリポジトリを作成します。 ログの一部はレポートと分析のために中央の場所にプルされます。 この一部は、少数のデータ型であることも、日次統計などのアクティビティの概要であることもあります。

Log Analytics に中央の場所を実装するオプションは次の 2 つあります。

1. 中央ワークスペース: サービス プロバイダーは、そのテナント内にワークスペースを作成し、[クエリ API](https://dev.loganalytics.io/) を [データ収集 API](log-analytics-data-collector-api.md) とともに利用してさまざまなワークスペースからこの中央の場所にデータを移動させるスクリプトを使用できます。 スクリプト以外のもう 1 つのオプションは [Azure Logic App](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview) を使用するというものです。

2. 中央の場所としての Power BI: Power BI は、さまざまなワークスペースが Log Analytics と [Power BI](log-analytics-powerbi.md) との統合を使用してデータをエクスポートするときに、中央の場所として機能します。 


## <a name="next-steps"></a>次の手順
* [Resource Manager テンプレート](log-analytics-template-workspace-configuration.md)を使用してワークスペースの作成および構成を自動化する
* [PowerShell](log-analytics-powershell-workspace-configuration.md) を使用してワークスペースの作成を自動化する 
* [アラート](log-analytics-alerts.md) を使用して既存のシステムと統合する
* [Power BI](log-analytics-powerbi.md) を使用して概要レポートを作成する

