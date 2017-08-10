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
ms.date: 07/03/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bf56aa4bafd929010bd916d09f7025ad76823d1f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services とは
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services は、クラウドにおけるエンタープライズ レベルのデータ モデリングを実現します。 これは、完全管理型のサービスとしてのプラットフォーム (PaaS) であり、Azure のデータ プラットフォーム サービスと緊密に統合されています。 Analysis Services は、高度に最適化された OLAP データ分析エンジンを使用することで、大規模で複雑なデータ ソース (多くの場合は異なる) 間で優れたセマンティック モデル レイヤーを提供します。 データをまとめてプルすることで、セマンティック データ モデルを定義し、高度にカスタマイズされた高性能の分析を作成できます。これにより、最新のクライアント ツールで、リッチでインタラクティブな分析エクスペリエンスが実現します。

![データ ソース](./media/analysis-services-overview/aas-overview-data-sources.png)


[このビデオ](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4)では、Azure Analysis Services が Microsoft の BI 機能全体にどのように組み込まれ、データ モデルのクラウドへの移行によりどのようなメリットが得られるのかを説明しています。

<!--
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>
-->

## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services が基礎
Azure Analysis Services は SQL Server Analysis Services Enterprise Edition と互換性があります。 Azure Analysis Services は、互換性レベル 1200 と 1400 の表形式モデルをサポートしています。 パーティション、行レベルセキュリティ、双方向リレーションシップ、翻訳はすべてサポートされています。 インメモリ モードと DirectQuery モードは、大規模かつ複雑なデータセットに対する高速なクエリを意味します。

表形式モデルは開発に要する時間が短く、高度なカスタマイズも可能です。 開発者向けに、表形式モデルには、モデル オブジェクトを記述するための表形式オブジェクト モデル (TOM) が用意されています。 TOM は、[Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) を通じて JSON 形式で公開され、[Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 名前空間を通じて AMO データ定義言語で公開されています。

互換性レベル 1400 の表形式モデルの新機能では、詳細行、オブジェクト レベルのセキュリティ、不規則階層、データ接続用の最新の SSDT の Get Data エクスペリエンスをはじめ、その他多くの強化機能がサポートされています。 また、基になるモデル メタデータが同じであるため、既存のオンプレミスの表形式モデル ソリューションを簡単にクラウドに移行することができます。


## <a name="better-with-azure"></a>Azure との連携強化
Azure Analysis Services とさまざまな Azure データ サービスを統合することにより、高度な分析ソリューションを構築できます。

Azure Analysis Services は、Azure SQL Database、Azure SQL Data Warehouse、Azure Blob Storage のデータを使用できます。 SQL データ ウェアハウスを中央に据え、さまざまな部門や分野を対象とする複数の BI モデルを接続したハブ アンド スポーク モデルを使用して、Azure でエンタープライズ データ ウェアハウス ソリューションを構築できます。

Azure Data Factory を使用すると、エンタープライズ向けの BI/分析ソリューションの中核的な機能となる、データの移動と変換のオーケストレーションを実行できます。 モデルにデータを読み込むアクティビティを含めることで、Azure Analysis Services を任意の Azure Data Factory パイプラインに統合できます。 また、Azure Automation や Azure Functions を使用すると、カスタム コードによるモデルの軽量オーケストレーションを行うこともできます。

Azure Analysis Services は、Azure Active Directory とも緊密に統合され、重要なデータへのセキュリティで保護されたロールベースのアクセスを実現します。

## <a name="pricing"></a>価格
Azure Analysis Services は、Developer レベル、Basic レベル、Standard レベルでご利用いただけます。 各レベルのプランのコストは、処理能力、QPU、メモリ サイズによって異なります。 サーバーの作成時に、レベル内のプランを選択します。 プランは、同一レベル内で変更することも、上位レベルにアップグレードすることもできますが、上位レベルから下位レベルにダウングレードすることはできません。

![レベルのアップグレード](./media/analysis-services-overview/aas-overview-tier-up.png)

レベルは、Azure Portal で即時に変更するか、Set-AzureRmAnalysisServicesServer PowerShell コマンドレットを使用して変更します。 各種プランとレベルの詳細については、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」を参照してください。価格計算ツールを使用して、ご自身に合ったプランを判断することができます。

## <a name="scale-resources"></a>リソースのスケール
サーバーをスケールアップ、スケールダウン、または一時停止します。 Azure Portal を使用するか、PowerShell を使用して即座に全体を制御します。 料金は使用した分だけになります。

新しいサーバーを作成する際は、[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドレットを使用してプランを設定します。 既存のサーバーの場合は、[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) コマンドレットを使用してプランを変更します。 サービスを常に使用するわけではない場合は、 ポータルまたは [Suspend-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver) コマンドレットを使用して、一時停止することができます。 再開するには、[Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver) コマンドレットを使用します。 料金は、サーバーがアクティブになっている時間に対して発生します。


## <a name="regions"></a>地域
Azure Analysis Services サーバーは、次の [Azure リージョン](https://azure.microsoft.com/regions/)で作成できます。

| アメリカ | ヨーロッパ | アジア太平洋 |
|----------|--------|--------------|
|  ブラジル南部<br> カナダ中部<br> 米国東部 2<br> 米国中北部<br> 米国中南部<br> 米国中西部<br> 米国西部 | 北ヨーロッパ<br> 英国南部<br> 西ヨーロッパ |   オーストラリア南西部<br> 東日本<br> 東南アジア<br> インド西部  |

新しいリージョンが随時追加されるため、この一覧は不十分である可能性があります。 場所は、Azure Portal または Azure Resource Manager テンプレートでサーバーを作成するときに選択します。 パフォーマンスを最大限に高めるには、最大のユーザー ベースに最も近い場所を選びます。 [高可用性](analysis-services-bcdr.md)を確保するには、モデルを複数リージョンの冗長サーバーにデプロイします。

## <a name="get-up-and-running-quickly"></a>スピーディな起動と実行
Azure Portal を使用すると、数分で[サーバーを作成する](analysis-services-create-server.md)ことができます。 また、Azure Resource Manager テンプレートと PowerShell により、宣言型のテンプレートを使用してサーバーをプロビジョニングすることができます。 1 つのテンプレートを使用して、ストレージ アカウントなどの他の Azure コンポーネントと共に複数のサービスをデプロイできます。  詳細については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」を参照してください。

サーバーが作成されたら、Azure Portal ですぐに表形式モデルを作成できます。 新しい (プレビュー) Web デザイナー機能を使用して、Azure SQL Database や Azure SQL Data Warehouse データ ソースに接続したり、Power BI Desktop の .pbix ファイルをインポートしたりできます。 テーブル間のリレーションシップは自動的に作成され、ご利用のブラウザーから直接、メジャーを作成したり、JSON 形式の model.bim ファイルを編集したりできます。

## <a name="migrate-existing-tabular-models"></a>既存の表形式モデルの移行
既存のオンプレミスの SQL Server Analysis Services モデル ソリューションがある場合は、大幅な変更をしなくても Azure Analysis Services に移行できます。 移行するには、SSDT を使用してモデルをサーバーにデプロイします。 または SSMS で、バックアップと復元か、TMSL を使用できます。

オンプレミスのデータ ソースがある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールして構成する必要があります。 ロールとロール メンバーを既に構成済みの場合、ロールは移行されますが、ロール メンバーは SSMS または PowerShell を使用して追加し直す必要があります。


## <a name="data-sources"></a>データ ソース
Azure Analysis Services では、組織のオンプレミス データ ソースとクラウドのデータ ソースへの接続がサポートされています。 ハイブリッド ソリューションでは、オンプレミスとクラウド両方のデータ ソースのデータを組み合わせます。 

互換性レベル 1400 の新しい表形式モデルでは、M 数式クエリ言語に基づいた、SSDT の最新の Get Data 機能を使用します。 Get Data を使用すると、より多くのデータ変換機能やデータ マッシュアップ機能に加え、独自の高度な M 数式言語クエリの作成および編集機能も利用できます。 たとえば、互換性レベル 1400 の表形式モデルを使用すると、Azure Blob Storage 内のデータ ファイルをモデル化できます。

Azure Analysis Services は、Azure SQL Database、Azure SQL Data Warehouse、SQL Server、SQL Server Data Warehouse、Oracle、Teradata のリレーショナル データベースに直接接続する場合に [DirectQuery](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular) の使用をサポートしています。

詳細については、「[Azure Analysis Services でサポートされるデータ ソース](analysis-services-datasource.md)」を参照してください。

## <a name="use-the-tools-you-already-know"></a>既に知っているツールの使用

![BI 開発者用ツール](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) for Visual Studio
無料の [SQL Server Data Tools (SSDT) for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) を使用して、モデルを開発してデプロイします。 SSDT には、スピーディに開発を行うための Analysis Services プロジェクト テンプレートが用意されています。 現在、SSDT には、互換性レベル 1400 の表形式モデルに対応した最新の Get Data のデータ ソース クエリとマッシュアップ機能が含まれています。 Power BI Desktop と Excel 2016 の Get Data に慣れていれば、高度にカスタマイズされたデータ ソース クエリの作成が非常に簡単であることは既にわかっています。

新しい SSDT と互換性レベル 1400 の表形式モデルを使用すると、ワークスペース データベースをホストするために Analysis Services のローカル インスタンスをインストールする必要はなくなります。 現在、SSDT には、統合された独自の Analysis Services エンジンとデータベースが含まれています。 準備ができたら、SSDT から直接、Azure 内のサーバーにデプロイします。 さらに、SSDT は毎月更新されるので、最新の機能をすぐに使用することができます。

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) を使って、サーバーとモデル データベースを管理します。 クラウド内のサーバーに接続します。 XMLA クエリ ウィンドウから直接 TMSL スクリプトを実行したり、TMSL スクリプトを使用してタスクを自動化したりできます。 新しい機能が次々と登場するため、SSMS は毎月更新されます。

#### <a name="powershell"></a>PowerShell
サーバーの作成、サーバー操作の中断/再開、サービス レベルの変更などのサーバー リソース管理タスクでは、Azure Resource Manager (AzureRM) コマンドレットが使用されます。 ロール メンバーの追加と削除、処理、TMSL スクリプトの実行など、その他のデータベース管理タスクでは、SqlServer モジュールのコマンドレットが使用されます。 AzureRM モジュールと SQLServer モジュールはどちらも、[PowerShell ギャラリー](https://www.powershellgallery.com/)で入手できます。


## <a name="secure"></a>セキュリティ保護
![データ可視化](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>認証
Azure Analysis Services のユーザー認証は、[Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) によって処理されます。 ユーザーが Azure Analysis Services データベースにログインするときは、対象のデータベースへのアクセス権がある組織のアカウント ID を使います。 これらのユーザー ID は、Azure Analysis Services サーバーが存在しているサブスクリプションの既定の Azure Active Directory のメンバーである必要があります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。

#### <a name="data-security"></a>データのセキュリティ
Azure Analysis Services は、Azure Blob Storage を利用して、Analysis Services データベースのストレージとメタデータを保持します。 BLOB 内のデータ ファイルは、Azure Blob Server Side Encryption (SSE) を使って暗号化されます。 直接クエリ モードを使用している場合は、メタデータのみが格納されます。 実際のデータは、クエリ時にデータ ソースからアクセスされます。

#### <a name="on-premises-data-sources"></a>オンプレミス データ ソース
組織のオンプレミス データへの安全なアクセスは、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールして構成することにより実現します。 ゲートウェイは、直接クエリ モードとメモリ内モード両方のデータへのアクセスを提供します。 Azure Analysis Services モデルがオンプレミス データ ソースに接続すると、オンプレミス データ ソースに対する暗号化された資格情報を含むクエリが作成されます。 ゲートウェイ クラウド サービスはクエリを分析して、Azure Service Bus に要求をプッシュします。 オンプレミス ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。 その後、ゲートウェイはクエリを取得して、資格情報を復号化し、データ ソースに接続して実行します。 結果は、データ ソースからゲートウェイ経由で Azure Analysis Services データベースに返送されます。

Azure Analysis Services は、[Microsoft オンライン サービス使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)および[Microsoft オンライン サービス プライバシー表明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)によって管理されます。
Azure のセキュリティについて詳しくは、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/Security/AzureSecurity)をご覧ください。

## <a name="client-connections"></a>クライアント接続
![データ可視化](./media/analysis-services-overview/aas-overview-clients.png)

Power BI、Excel、その他のサードパーティ製ツールなど、データの探索と視覚化用の最新ツールにより、エンド ユーザーは、モデル データに対して非常にインタラクティブで視覚的に優れた洞察を得ることができます。

クライアントは MSOLAP、AMO、または ADOMD の[クライアント ライブラリ](analysis-services-data-providers.md)を使用して、Analysis Services サーバーに接続します。 Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされます。 ただし、更新のバージョンや頻度によっては、クライアント ライブラリが Azure Analysis Services で必要な最新バージョンでない可能性があることに注意してください。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 通常、これらのアプリケーションでは、パッケージの一部としてライブラリを手動でインストールする必要があります。


## <a name="get-help"></a>問い合わせ

#### <a name="documentation"></a>ドキュメント
Azure Analysis Services は簡単にセットアップして管理できます。 サーバーのサービスの作成と管理に必要なすべての情報はこちらで確認できます。 データ モデルの作成とサーバーへのデプロイは、オンプレミスのサーバーにデプロイするデータ モデルを作成する場合とほぼ同じです。 概念、手順、チュートリアル、リファレンスの記事の広範なライブラリが、[Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) にあります。

#### <a name="videos"></a>ビデオ
[Azure Analysis Services に関する Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) にある、役立つビデオをご確認ください。

#### <a name="blogs"></a>ブログ
内容は急速に変化しています。 最新情報は、[Analysis Services チーム ブログ](https://blogs.msdn.microsoft.com/analysisservices/)および [Azure ブログ](https://azure.microsoft.com/blog/)で常に入手できます。

#### <a name="community"></a>コミュニティ
Analysis Services には活気のあるユーザー コミュニティがあります。 [Azure Analysis Services フォーラム](https://aka.ms/azureanalysisservicesforum)の会話に参加してください。

## <a name="feedback"></a>フィードバック
提案や機能の要求がある場合は、 [Azure Analysis Services のフィードバック](https://aka.ms/azureanalysisservicesfeedback)にコメントをお送りください。

ドキュメントに関する指摘がある場合は、 各記事の下部にある Livefyre を使ってコメントを追加できます。

## <a name="next-steps"></a>次のステップ
Azure Analysis Services について理解できたら、実際に始めてみてください。 Azure で[サーバーを作成する](analysis-services-create-server.md)方法について説明します。 サーバーの準備ができたら、[Adventure Works チュートリアル](tutorials/aas-adventure-works-tutorial.md)を進めて、完全に機能する表形式モデルを作成してサーバーにデプロイする方法を学習してください。

