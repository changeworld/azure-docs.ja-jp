<properties
   pageTitle="Microsoft Power BI Embedded とは何ですか?"
   description="Power BI Embedded を利用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合できるため、ユーザー向けにデータを視覚化するカスタム ソリューションを作成する必要はありません"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="owend"/>

# Microsoft Power BI Embedded とは何ですか?

**Power BI Embedded** を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートを統合することができます。

![](media\powerbi-embedded-whats-is\what-is.png)

**Power BI Embedded** は、ISV やアプリケーション開発者がアプリケーション内で Power BI データ エクスペリエンスを実現することを可能にする Azure サービスです。開発者が作成したアプリケーションは、それぞれにユーザーが存在し、異なる機能セットを備えています。これらのアプリケーションには、Microsoft Power BI Embedded に対応できるようになったグラフやレポートなどのデータ要素が組み込まれていることもあります。ユーザーは、アプリケーションを使用するために Power BI アカウントを必要としません。これまでと同様にアプリケーションにサインインし、Power BI のレポートを表示して操作できます。追加のライセンスは不要です。

## Microsoft Power BI Embedded のライセンス

**Microsoft Power BI Embedded** の使用モデルでは、Power BI のライセンスはエンドユーザーの責任ではありません。代わりに、ビジュアルを使用するアプリケーションの開発者が**レンダー**を購入します。レンダーは、それらのリソースを所有するサブスクリプションに課金されます。

## Microsoft Power BI Embedded の概念モデル

![](media\powerbi-embedded-whats-is\model.png)

Azure の他のサービスと同様に、[Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx) を使用して Power BI Embedded のリソースがプロビジョニングされます。この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

## ワークスペース コレクション

**ワークスペース コレクション**は、0 個以上の**ワークスペース**が含まれた、リソースの最上位 Azure コンテナーです。**ワークスペース** **コレクション**には、すべての標準 Azure プロパティだけでなく、次の要素があります。

-	**アクセス キー** – Power BI API を安全に呼び出すときに使用されるキーです (後述)。
-	**ユーザー** – Azure ポータルまたは ARM API を使用して Power BI ワークスペース コレクションを管理する管理者権限を持つ Azure Active Directory (AAD) ユーザーです。
-	**リージョン** – **ワークスペース コレクション**のプロビジョニングの一環として、プロビジョニング先のリージョンを選択できます。詳細については、「[Azure のリージョン](https://azure.microsoft.com/regions/)」をご覧ください。

## ワークスペース

**ワークスペース**は、Power BI コンテンツのコンテナーであり、データセット、レポート、ダッシュボードを含めることができます。**ワークスペース**は、最初に作成された時点では空です。プレビュー期間中は、Power BI Desktop を使用してすべてのコンテンツを作成し、[Power BI REST API](http://docs.powerbi.apiary.io/reference) を使用してコンテンツをワークスペースのいずれかにアップロードします。

## ワークスペース コレクションとワークスペースの使用
**ワークスペース コレクション**と**ワークスペース**は、作成するアプリケーションの設計に最適な方法で使用および構成されるコンテンツのコンテナーです。ワークスペース コレクションとワークスペースの中では、多様な方法でコンテンツを構成できます。1 つのワークスペースにすべてのコンテンツを配置してから、後でアプリ トークンを使用して、顧客の間でコンテンツをさらに細かく分割することができます。顧客が分離されるように、複数の個別ワークスペースにすべての顧客を配置することもできます。または、顧客別ではなくリージョン別にユーザーを構成することもできます。この柔軟な設計により、コンテンツの最適な構成方法を選択できます。

## キャッシュされたデータセット

キャッシュされたデータセットをプレビューに使用できます。ただし、キャッシュされたデータは、**Microsoft Power BI Embedded** への読み込み後に更新することはできません。

## アプリ トークンを使用した認証と承認

**Microsoft Power BI Embedded** では、必要なユーザーの認証と承認の実行をすべてアプリケーションに委ねます。エンドユーザーは Azure Active Directory (Azure AD) の顧客でなければならないという明示的な条件はありません。代わりに、アプリケーションが**アプリケーション認証トークン (アプリ トークン)** を使用して、Power BI レポートのレンダリングの承認を **Microsoft Power BI Embedded** に伝えます。これらの**アプリ トークン**は、アプリケーションがレポートをレンダリングするときに必要に応じて生成されます。[アプリ トークン](power-bi-embedded-get-started-sample.md#key-flow)に関する記事をご覧ください。

![](media\powerbi-embedded-whats-is\app-tokens.png)

### アプリケーション認証トークン

**アプリケーション認証トークン (アプリ トークン)** は、**Microsoft Power BI Embedded** に対する認証に使用されます。**アプリ トークン**には、次の 3 種類があります。

1.	プロビジョニング用トークン - **ワークスペース コレクション**に新しい**ワークスペース**をプロビジョニングするときに使用されます。
2.	開発用トークン - **Power BI REST API** を直接呼び出すときに使用されます。
3.	埋め込み用トークン - 埋め込まれた iframe でのレポートのレンダリングのために呼び出しを行うときに使用されます。

これらのトークンは、**Microsoft Power BI Embedded** とのやり取りのさまざまなフェーズに使用されます。トークンは、許可をアプリから Power BI に委任できるように設計されています。

### アプリ トークンの生成

プレビュー用に提供されている SDK を使用して、トークンを生成できます。まず、Create\_\_\_Token() メソッドのいずれかを呼び出します。次に、**ワークスペース コレクション**から取得したアクセス キーを使用して、Generate() を呼び出します。トークンの基本的な Create メソッドは Microsoft.PowerBI.Security.PowerBIToken クラスで次のように定義されています。

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) と [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx) の使用方法の例については、[Microsoft Power BI Embedded の使用を開始するためのサンプル コード](power-bi-embedded-get-started-sample.md)に関する記事をご覧ください。


## 関連項目
- [Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)
- [Microsoft Power BI Embedded の概要](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0713_2016-->