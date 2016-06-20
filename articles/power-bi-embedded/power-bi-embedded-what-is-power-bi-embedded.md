<properties
   pageTitle="Microsoft Power BI Embedded とは"
   description="Power BI Embedded を利用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合できるため、ユーザー向けにデータを視覚化するカスタム ソリューションを作成する必要はありません"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/01/2016"
   ms.author="jocaplan"/>


# Microsoft Power BI Embedded とは

**Microsoft Power BI Embedded** を利用して Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合することができますので、ユーザーのためにデータを視覚化するカスタム ソリューションを作成する必要はありません。

![](media\powerbi-embedded-whats-is\what-is.png)

**Microsoft Power BI Embedded** は、独立系ソフトウェア ベンダー (ISV) がアプリケーション内で Power BI データ エクスペリエンスを実現することを可能にする Azure サービスです。ISV はアプリケーションを構築しました。これらのアプリケーションには、それぞれに固有のユーザーと固有の機能があります。これらのアプリには **Microsoft Power BI Embedded** に対応できるようになったチャートやレポートといったデータ要素が組み込まれていることもあります。これらのアプリケーションのユーザーがアプリを使用するには、Power BI のアカウントは必要ありません。これらのユーザーは、従来と同じようにアプリケーションにサインインして、Power BI のレポートおよびタイルを表示して操作できます。それには追加のライセンスを必要としません。

## Microsoft Power BI Embedded のライセンス

**Microsoft Power BI Embedded** の使用モデルでは、Power BI のライセンスはエンドユーザーの責任ではありません。代わりに、ビジュアルを消費するアプリの開発者が**レンダー**を購入します。レンダーは、それらのリソースを所有するサブスクリプションに課金されます。

## Microsoft Power BI Embedded の概念モデル

![](media\powerbi-embedded-whats-is\model.png)

Azure のほかのサービスと同様に [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx) を使用して、**Microsoft Power BI Embedded** のリソースのプロビジョニングが行われます。この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

## ワークスペース コレクション

**ワークスペース コレクション** は 0 または複数の**ワークスペース**を含むリソースのトップレベル Azure コンテナーです。**ワークスペース** **コレクション** は、すべての標準 Azure プロパティだけでなく、以下のものを含みます。

-	**アクセス キー** – (後のセクションで説明されているとおり) Power BI API を安全に呼び出すときに使用されるキーです。
-	**ユーザー** – Azure ポータルまたは ARM API から Power BI ワークスペース コレクションを管理する管理者権限を持つ Azure Active Directory (AAD) ユーザーです。
-	**リージョン** – **ワークスペース コレクション**のプロビジョニングの一部として、プロビジョニング先のリージョンを選択することができます。詳細については、「[Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

## ワークスペース

**ワークスペース** はデータセット、レポート、ダッシュ ボードなど Power BI コンテナーのコンテンツです。**ワークスペース**は最初に作成されたときに空です。プレビュー時に Power BI Desktop ですべてのコンテンツを作成して、[Power BI REST API](http://docs.powerbi.apiary.io/reference) でそのコンテンツをワークスペースのいずれかにアップロードします。

## ワークスペース コレクションとワークスペースの使用
**ワークスペース コレクション**と**ワークスペース**は、ご自分が作成しているアプリケーションの設計に最適なように使用および構成されるコンテンツのコンテナーです。ワークスペース コレクションとワークスペースの中では、多様な方法でコンテンツを構成できます。1 つのワークスペースにすべてのコンテンツを配置してから、後でアプリ トークンを使用して、顧客の間でコンテンツをさらに細かく分割することができます。顧客が分離されるように、複数の個別ワークスペースにすべての顧客を配置することもできます。または、顧客別ではなくリージョン別にユーザーを構成することもできます。この柔軟な設計により、コンテンツの最適な構成方法を選択できます。
## プレビューでのデータ ソース

次のように限定されたデータ ソースをプレビューに利用できます。

### 直接クエリ

プレビューのクラウド ソースに対して直接クエリ接続がサポートされます。つまり、最新のデータを表示するには、そのデータ ソースに接続することができます。これらのデータ ソースにクラウドから接続できる必要があります。これらのデータ ソースでは基本認証を使用する必要があります。理想的な候補は次のとおりです。

-	SQL Azure
-	SQL Azure DW
-	HD Insight Spark

## キャッシュされたデータセット

キャッシュされたデータセットをプレビューに使用できます。ただし、キャッシュされたデータは **Microsoft Power BI Embedded** への読み込み後に更新することはできません。

## アプリ トークンを使用した認証と承認

**Microsoft Power BI Embedded** は、必要なすべての認証および承認の実行をアプリケーションに委ねます。エンドユーザーは Azure Active Directory (Azure AD) の顧客でなければならないという明示的な条件はありません。代わりに、アプリケーションは Power BI レポートのレンダリングの承認を**アプリケーション認証トークン (アプリ トークン)**を使用して **Microsoft Power BI Embedded** に伝えます。これらの**アプリ トークン**は、アプリによるレポートのレンダリングのときに必要に応じて生成されます。 [アプリ トークン](power-bi-embedded-get-started-sample.md#key-flow)をご覧ください。

![](media\powerbi-embedded-whats-is\app-tokens.png)

### アプリケーション認証トークン

**アプリケーション認証トークン (アプリ トークン)** は **Microsoft Power BI Embedded** に対する認証を行うために使用されます。3 種類の**アプリ トークン**があります。

1.	プロビジョニング用トークン - **ワークスペース コレクション**への新しい**ワークスペース**のプロビジョニングを行うときに使用されます。
2.	開発用トークン - **Power BI REST API** への直接の呼び出しを行うときに使用されます。
3.	埋め込み用トークン - 埋め込まれた iframe でのレポートのレンダリングのために呼び出しを行うときに使用されます。

これらのトークンは、**Microsoft Power BI Embedded** との対話のさまざまなフェーズに使用されます。トークンは、許可をアプリから Power BI に委任できるように設計されています。

### アプリ トークンの生成

プレビュー用に提供されている SDK を使用して、トークンを生成できます。まず、Create\_\_\_Token() メソッドのいずれかを呼び出します。次に、**ワークスペース コレクション**から取得したアクセス キーを使用して、Generate() を呼び出します。トークンの基本的な Create メソッドは Microsoft.PowerBI.Security.PowerBIToken クラスで次のように定義されています。

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) と [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx) の使用方法の例については、「[Get started with Microsoft Power BI Embedded sample code (Microsoft Power BI Embedded の使用を開始するためのサンプル コード)](power-bi-embedded-get-started-sample.md)」をご覧ください。


## 関連項目
- [Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューの使用を開始する)](power-bi-embedded-get-started.md)
- [アプリ トークン](power-bi-embedded-get-started-sample.md#key-flow)
- [Power BI REST API](http://docs.powerbi.apiary.io/reference)
- [Azure リージョン](https://azure.microsoft.com/regions/)

<!---HONumber=AcomDC_0608_2016-->