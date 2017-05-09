---
title: "Operations Management Suite のセキュリティと監査ソリューションの Web ベースライン | Microsoft Docs"
description: "このドキュメントでは、OMS のセキュリティと監査ソリューションを使用して、コンプライアンスとセキュリティの目的で監視対象のすべての Web サーバーの Web ベースライン評価を実行する方法を説明します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite のセキュリティと監査ソリューションでの Web ベースライン評価
このドキュメントは、[Operations Management Suite (OMS) のセキュリティと監査ソリューション](operations-management-suite-overview.md)の Web ベースライン評価機能を使用して、監視対象のリソースのセキュリティで保護された状態にアクセスする際に役立ちます。

## <a name="what-is-web-baseline-assessment"></a>Web ベースライン評価とは
現在、OMS Security は、オペレーティング システムのセキュリティ ベースライン評価を提供します。 24 時間ごとにサーバーのオペレーティング システムの設定をスキャンし、潜在的に脆弱な設定に関する情報を提供します。 詳細については、「[Baseline Assessment in Operations Management Suite Security and Audit Solution (Operations Management Suite のセキュリティと監査ソリューションでのベースライン評価)](oms-security-baseline.md)」を参照してください。

Web ベースライン評価の目的は、Web サーバーの潜在的に脆弱な設定を見つけることです。 Web ベースラインの構成の 3 つの主要ソースは、.NET、ASP.NET、および IIS の構成です。  オペレーティング システムのベースライン評価と同じように、OMS Security は、24 時間ごとに Web サーバーをスキャンし、セキュリティの状態に関する情報を提供します。  インターネット インフォメーション サービス (IIS) では、構成を高度にカスタマイズできるため、さまざまなサイトやアプリケーション レベルを上書きできます。 スキャナーは、既定のルート レベルだけでなく、各アプリケーションおよびサイト レベルでも設定を確認します。 そのため、潜在的に脆弱な設定の場所を特定し、すぐに修復できます。


## <a name="web-security-baseline-assessment"></a>Web セキュリティ ベースライン評価
このプレビューでは、OMS 検索オプションを使用してこの機能にアクセスします。 適切なクエリを実行するには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、**[セキュリティおよび監査]** タイルをクリックします。
2. **[セキュリティおよび監査]** ダッシュボードで、**[ログ検索]** をクリックします。
3. 使用できる最初のクエリは、**Web ベースライン評価の概要**です。 **[Begin search here (ここから検索を開始)]** フィールドに、Type*=SecurityBaselineSummary BaselineType=web* というクエリを入力します。 次の画面は、出力のサンプルです。

![Web ベースライン評価の概要](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> このクエリでは、各レコードが 1 つのサーバーの評価の概要を示しています。

**[ログ検索]** では、さまざまなクエリを入力して、Web ベースライン評価に関する詳細情報を入手できます。 このプレビューでは、前のクエリだけでなく、次のクエリを使用することもできます。

**Web ベースライン ルール評価**: 各レコードは、1 つのサーバーの 1 つの Web ベースライン ルール評価を表します。 ルール、場所、予想される結果、実際の結果に関するすべてのデータが含まれています。

**クエリ**: Type*=SecurityBaseline BaselineType=web*

![Web ベースライン ルール評価](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**特定のサーバーの結果をすべて表示**: このクエリでは、特定のサーバーの結果を表示する方法が示されます。

**クエリ**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![すべての結果](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

これらのレコード/クエリを使用して、独自のダッシュボード、レポート、またはアラートを作成することもできます。 次の画面には、ダッシュボードに追加できるサンプルの UI コントロールが含まれています。 OMS ビュー デザイナーを使用して、データを視覚化する方法については、[こちら](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)を参照してください。 次の画面は、カスタマイズするとタイルがどのように表示されるかを示す例です。

![サンプル UI](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> ベースライン評価の確認対象となる設定については、それらの設定が記載されている[この Excel スプレッドシート](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690)をダウンロードしてください。

## <a name="see-also"></a>関連項目
このドキュメントでは、OMS のセキュリティと監査の Web ベースラインの評価について説明しました。 OMS セキュリティの詳細については、次の記事を参照してください。

* [Operations Management Suite (OMS) overview (Operations Management Suite (OMS) の概要)](operations-management-suite-overview.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と対応](oms-security-responding-alerts.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視](oms-security-monitoring-resources.md)


