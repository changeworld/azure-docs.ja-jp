---
title: Azure Migrate の検出および評価ツールでの Azure App Service の評価
description: Azure Migrate の検出および評価ツールでの Azure App Service の評価について説明します
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 3328fb0fb20a9b0e492c3cd4281ee35dcf12dda0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792032"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>評価の概要 (Azure App Service への移行)

この記事では、[Azure Migrate: 検出および評価ツール](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)を使用して、VMware 環境から Azure App Service にオンプレミスの ASP.NET Web アプリを移行するための評価の概要について説明します。

## <a name="whats-an-assessment"></a>評価とは
検出および評価ツールを使用した評価は、データのポイントインタイム スナップショットで、Azure に対してオンプレミスのサーバー、データベース、Web アプリをホストするためのコストの詳細を提供します。

## <a name="types-of-assessments"></a>評価の種類

Azure Migrate: 検出および評価ツールを使用して作成できる評価には、次の 4 種類があります。

**評価の種類** | **詳細**
--- | ---
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> この評価の種類を使用すると、[VMware](how-to-set-up-appliance-vmware.md) と [Hyper-V](how-to-set-up-appliance-hyper-v.md) の環境にあるオンプレミスのサーバー、および[物理サーバー](how-to-set-up-appliance-physical.md)を Azure VM に移行するために評価できます。
**Azure SQL** | オンプレミスの SQL サーバーを VMware 環境から Azure SQL Database または Azure SQL Managed Instance に移行するための評価。
**Azure App Service** | IIS Web サーバー上で実行されているオンプレミスの ASP.NET Web アプリを、VMware 環境から Azure App Service に移行するための評価。
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。 [詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

Azure App Service 評価には、サイズ変更の設定基準が 1 つ用意されています。

**サイズ変更の設定基準** | **詳細** | **データ**
--- | --- | ---
**構成ベース** | 収集された構成データに基づいて推奨を行う評価 | Azure App Service の評価では、構成データのみを考慮に入れて評価計算が行われます。 Web アプリのパフォーマンス データは収集されません。

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>オンプレミスの ASP.NET Web アプリを評価する方法

オンプレミスの Web アプリは、軽量の Azure Migrate アプライアンスによって収集された構成データを使用して評価できます。 このアプライアンスは、オンプレミスの Web アプリを検出し、構成データを Azure Migrate に送信します。 [詳細情報](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>アプライアンスで評価する方法

オンプレミスのサーバーを検出するために Azure Migrate アプライアンスをデプロイする場合は、次の手順を行います。

1. Azure Migrate と連携するように Azure およびオンプレミス環境を設定します。
2. はじめて評価を行う場合は、Azure Migrate プロジェクトを作成します。 Azure Migrate: 検出および評価ツールは、既定でプロジェクトに追加されます。
3. 軽量の Azure Migrate アプライアンスをデプロイします。 アプライアンスでオンプレミス サーバーが継続的に検出され、構成およびパフォーマンス データが Azure Migrate に送信されます。 アプライアンスは VM または物理サーバーとしてデプロイします。 評価対象のサーバーには何もインストールする必要はありません。

アプライアンスで検出が開始されたら、評価するサーバー (Web アプリをホストしているもの) をグループにまとめ、評価の種類として **Azure App Service** を使用してそのグループに対する評価を実行できます。

[ASP.NET Web アプリ](tutorial-assess-webapps.md)の評価に関するチュートリアルに従って、これらの手順を試してみてください。

## <a name="what-properties-are-used-to-customize-the-assessment"></a>評価をカスタマイズするために使用されるプロパティ

Azure App Service 評価のプロパティに含まれるものは次のとおりです。

**プロパティ** | **詳細**
--- | ---
**ターゲットの場所** | 移行先となる Azure リージョン。 Azure App Service の構成とコストに関する推奨事項は、ユーザーが指定した場所に基づいています。
**分離が必要** | Standard プランと比較してより高速なプロセッサ、SSD ストレージ、コア比 2 倍のメモリを備えた Dv2 シリーズ VM を使用して、Azure データセンター内のプライベートな専用環境で Web アプリを実行したい場合は、[はい] を選択します。
**予約インスタンス** | 予約インスタンスを指定します。これにより、評価でのコスト見積もりで考慮されます。<br/><br/> 予約インスタンス オプションを選択した場合、[割引 (%)] を指定することはできません。
**プラン** | 自分が登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 評価によって、そのプランのコストが見積もられます。
**通貨** | アカウントの請求通貨。
**割引率 (%)** | Azure プランに適用される任意のサブスクリプション固有の割引。 既定の設定は 0% です。
**EA サブスクリプション** | Enterprise Agreement (EA) サブスクリプションをコスト見積もりに使用することを指定します。 このサブスクリプションに適用される割引が考慮されます。 <br/><br/> 予約インスタンスの設定 (割引率 (%) および VM のアップタイムの各プロパティ) は、既定の設定のままにします。

Azure Migrate で評価を作成するための[ベスト プラクティスを確認](best-practices-assessment.md)してください。

## <a name="calculate-readiness"></a>対応性を計算する

### <a name="azure-app-service-readiness"></a>Azure App Service の対応性

Web アプリに対する Azure App Service の対応性は、Web アプリのオンプレミスの構成と Azure App Service との間の機能の互換性チェックに基づいています。

1. Azure App Service の評価では、Web アプリの構成データを考慮して、互換性の問題を特定します。
1. 互換性の問題が見つからなかった場合は、ターゲットのデプロイの種類に対する対応性が **[対応]** とマークされます。
1. 機能低下やサポートされていない機能など、ターゲットとする特定のデプロイの種類への移行をブロックしない、重大でない互換性の問題がある場合は、対応性は **[条件付きで対応]** (ハイパーリンク付き) とマークされ、**警告** の詳細と推奨される修復のガイダンスが表示されます。
1. ターゲットとする特定のデプロイの種類への移行を妨げる可能性がある互換性の問題がある場合は、対応性は **[準備不完了]** とマークされ、**問題** の詳細と推奨される修復のガイダンスが表示されます。
1. 検出がまだ進行中であるか、Web アプリの検出で何らかの問題がある場合、対応性は **[不明]** としてマークされます。これは、評価でその Web アプリに対する対応性を計算できなかったためです。

## <a name="calculate-sizing"></a>サイズ設定の計算

### <a name="azure-app-service-sku"></a>Azure App Service SKU

評価では、構成データに基づいて対応性を判断した後、Azure App Service でアプリを実行するのに適した Azure App Service SKU が判断されます。
Premium プランは運用ワークロード向けで、専用の仮想マシン インスタンスで稼働します。 各インスタンスでは、複数のアプリケーションとドメインがサポートされます。 Isolated プランは、プライベートな専用の Azure 環境内でアプリをホストするため、オンプレミスのネットワークとのセキュリティ保護された接続が必要なアプリに最適です。

> [!NOTE]
> 現在、Azure Migrate では、I1、P1v2、および P1v3 の SKU のみを推奨しています。 Azure アプリ サービスでは、他にも使用できる SKU が用意されています。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows/)。

### <a name="azure-app-service-plan"></a>Azure App Service プラン

App Service では、アプリは常に [App Service プラン](../app-service/overview-hosting-plans.md)で実行されます。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。 大まかに言えば、プランと SKU は次の表に示すように決定されます。

**分離が必要** | **予約インスタンス** | **App Service プラン/SKU**
--- | --- | ---
はい  | はい | I1
はい  | いいえ  | I1
いいえ  | はい | P1v3
いいえ  | いいえ | P1v2

### <a name="azure-app-service-cost-details"></a>Azure App Service のコストの詳細

[App Service プラン](../app-service/overview-hosting-plans.md)では、使用するコンピューティング リソースに対して[課金](https://azure.microsoft.com/pricing/details/app-service/windows/)されます。 App Service では、Web アプリごとにではなく、App Service プランごとに料金を支払います。 1 つまたは複数のアプリを同じコンピューティング リソース (または、同じ App Service プラン) で実行するように構成することができます。 この App Service プランに入れたアプリは、App Service プランで定義されたとおりにこれらのコンピューティング リソースで実行されます。
コストを最適化するため、Azure Migrate の評価では、複数の Web アプリをそれぞれに推奨されている App Service プランに割り当てます。 各プランのインスタンスに割り当てられる Web アプリの数は、次の表のようになります。

**App Service プラン** | **App Service プランごとの Web アプリ**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> App Service プランは、いつでもスケールアップまたはスケールダウンできます。 [詳細については、こちらを参照してください](../app-service/overview-hosting-plans.md#what-if-my-app-needs-more-capabilities-or-features)。

## <a name="next-steps"></a>次のステップ
- 評価作成のベストプラクティスを[確認](best-practices-assessment.md)します。 
- [Azure App Service の評価](how-to-create-azure-app-service-assessment.md)を実行する方法を確認します。