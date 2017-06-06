---
title: "Azure Analysis Services とは | Microsoft Docs"
description: "Azure での Analysis Services の概要を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: cc85ae1c6642e763e57327c151ec68845f7673f7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services とは
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Microsoft SQL Server Analysis Services の実績のある分析エンジン上に構築された Azure Analysis Services は、クラウド上でエンタープライズ級のデータ モデリングを提供します。 

このビデオでは、Azure Analysis Services がマイクロソフトの BI 機能全体にどのように組み込まれ、データ モデルのクラウドへの移行によりどのようなメリットが得られるのかを説明しています。


>
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services が基礎
Azure Analysis Services は SQL Server Analysis Services Enterprise Edition と互換性があります。 Azure Analysis Services は、互換性レベル 1200 以降の表形式モデルをサポートしています。 DirectQuery、パーティション、行レベル セキュリティ、双方向リレーションシップ、翻訳をすべてサポートします。

## <a name="use-the-tools-you-already-know"></a>既に知っているツールの使用
![BI 開発者用ツール](./media/analysis-services-overview/aas-overview-dev-tools.png)

Azure Analysis Services のデータ モデルを作成するときは、SQL Server Analysis Services と同じツールを使います。 モデルの作成とデプロイには、[SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)、または [Azure Powershell](/powershell/azureps-cmdlets-docs) と [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) の [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) テンプレートを使います。

## <a name="supports-the-latest-features"></a>最新機能のサポート
Azure Analysis Services は、互換性レベル 1200 および 1400 プレビューの表形式モデルをサポートしています。

**表形式 1200** - SQL Server 2016 Analysis Services に初めて含められた 1200 では、表、列、リレーションシップのようなモデル オブジェクトを表現するために、表形式オブジェクト モデル (TOM) が導入されました。 表形式のオブジェクト モデルは、[Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) を通じて JSON で公開され、[Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 名前空間を通じて AMO データ定義言語で公開されています。

**表形式 1400 プレビュー** - 詳細行、オブジェクト レベルのセキュリティ、不規則階層、データ接続用の最新の Get Data エクスペリエンス、その他の多くの強化機能のサポートが導入されています。 すべての最新機能を活用するには、最新の [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) を使用する必要があります。 表形式 1400 はまだプレビュー段階であるため、頻繁に変更されています。 最新版を取得するために、[ブログの投稿](https://azure.microsoft.com/blog/1400-models-in-azure-as/)を確認してください。

## <a name="data-sources"></a>データ ソース
Azure のサーバーにデプロイされるデータ モデルは、組織のオンプレミス データ ソースまたはクラウドのデータ ソースへの接続をサポートします。 ハイブリッド BI ソリューションでは、オンプレミスとクラウド両方のデータ ソースのデータを組み合わせます。

サーバーはクラウド内にあるため、クラウドのデータ ソースへの接続はシームレスです。 オンプレミス データ ソースに接続するときは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)によってクラウドのサーバーとの高速で安全な接続が保証されます。

サポートされるオンプレミス データ ソースについて詳しくは、「[Data sources supported in Azure Analysis Services (Azure Analysis Services でサポートされるデータ ソース)](analysis-services-datasource.md)」を参照してください。


## <a name="explore-your-data-from-anywhere"></a>任意の場所からデータを取得
ほとんどどこからでもサーバーに接続してデータを取得します。 Azure Analysis Services は、Power BI Desktop、Excel、カスタム アプリ、ブラウザー ベースのツールからの接続をサポートします。

![データ可視化](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>セキュリティ保護
#### <a name="user-authentication"></a>ユーザー認証
Azure Analysis Services のユーザー認証は、[Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) によって処理されます。 ユーザーが Azure Analysis Services データベースにログインするときは、対象のデータベースへのアクセス権がある組織のアカウント ID を使います。 これらのユーザー ID は、Azure Analysis Services サーバーが存在しているサブスクリプションの既定の Azure Active Directory のメンバーである必要があります。 AAD とオンプレミスの Active Directory の間の[ディレクトリ統合](https://technet.microsoft.com/library/jj573653.aspx)は、ユーザーが Azure Analysis Services データベースにアクセスできるようにするための優れた方法ですが、すべてのシナリオに必要なわけではありません。

ユーザーは、アカウントのユーザー プリンシパル名 (UPN) とパスワードを使ってログインします。 オンプレミスの Active Directory と同期されている場合は通常、ユーザーの UPN は組織のメール アドレスです。

Azure Analysis Services サーバーのリソースを管理するためのアクセス許可は、ユーザーを Azure サブスクリプション内のロールに割り当てることによって処理されます。 既定では、サブスクリプションの管理者は Azure のサーバー リソースに対する所有者アクセス許可を持っています。 Azure Resource Manager を使って他のユーザーを追加できます。

#### <a name="data-security"></a>データのセキュリティ
Azure Analysis Services は、Azure Blob Storage を利用して、Analysis Services データベースのストレージとメタデータを保持します。 BLOB 内のデータ ファイルは、Azure Blob Server Side Encryption (SSE) を使って暗号化されます。 直接クエリ モードを使用している場合は、メタデータのみが格納されます。 実際のデータは、クエリ時にデータ ソースからアクセスされます。

#### <a name="on-premises-data-sources"></a>オンプレミス データ ソース
組織のオンプレミス データへの安全なアクセスは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)をインストールして構成することにより実現できます。 ゲートウェイは、直接クエリ モードとメモリ内モード両方のデータへのアクセスを提供します。 Azure Analysis Services モデルがオンプレミス データ ソースに接続すると、オンプレミス データ ソースに対する暗号化された資格情報を含むクエリが作成されます。 ゲートウェイ クラウド サービスはクエリを分析して、Azure Service Bus に要求をプッシュします。 オンプレミス ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。 その後、ゲートウェイはクエリを取得して、資格情報を復号化し、データ ソースに接続して実行します。 結果は、データ ソースからゲートウェイ経由で Azure Analysis Services データベースに返送されます。

Azure Analysis Services は、[Microsoft オンライン サービス使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)および[Microsoft オンライン サービス プライバシー表明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)によって管理されます。
Azure のセキュリティについて詳しくは、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/Security/AzureSecurity)をご覧ください。

## <a name="get-help"></a>問い合わせ
Azure Analysis Services は簡単にセットアップして管理できます。 サーバーの作成と管理に必要なすべての情報はこちらで確認できます。 データ モデルの作成とサーバーへのデプロイは、オンプレミスのサーバーにデプロイするデータ モデルを作成する場合とほぼ同じです。 概念、手順、チュートリアル、リファレンスの記事の広範なライブラリが、[Analysis Services に関する MSDN](https://msdn.microsoft.com/library/bb522607.aspx) にあります。

また、[Azure Analysis Services に関する Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) には役に立つビデオが多数あります。

内容は急速に変化しています。 最新情報は [Azure Analysis Services のブログ](https://go.microsoft.com/fwlink/?linkid=830920)で常に入手できます。

## <a name="community"></a>コミュニティ
Analysis Services には活気のあるユーザー コミュニティがあります。 [Azure Analysis Services フォーラム](https://aka.ms/azureanalysisservicesforum)の会話に参加してください。

## <a name="feedback"></a>フィードバック
提案や機能の要求がある場合は、 [Azure Analysis Services のフィードバック](https://aka.ms/azureanalysisservicesfeedback)にコメントをお送りください。

ドキュメントに関する指摘がある場合は、 各記事の下部にある Livefyre を使ってコメントを追加できます。

## <a name="next-steps"></a>次のステップ
Azure Analysis Services について理解できたら、実際に始めてみてください。 Azure で[サーバーを作成する](analysis-services-create-server.md)方法およびサーバーに[表形式モデルをデプロイする](analysis-services-deploy.md)方法を学習してください。


