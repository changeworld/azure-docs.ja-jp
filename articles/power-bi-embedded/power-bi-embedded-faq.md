<properties
   pageTitle="FAQ"
   description="Power BI Embedded FAQ"
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
   ms.author="derrickv"/>

# Power BI Embedded FAQ

1. **Build では Power BI について何がアナウンスされましたか?**

    マイクロソフトは、Microsoft Power BI Embedded サービスのパブリック プレビュー版が利用できるようになったことを発表しました。

2.	**Microsoft Power BI Embedded とは何ですか?**

    Microsoft Power BI Embedded は、時間とコストをかけて独自のコントロールを構築することなく、アプリケーション開発者が魅力的で、完全にインタラクティブなレポートと視覚エフェクトを顧客向けアプリケーションへ埋め込むための Azure のサービスです。既定で付属しているさまざまなデータの視覚エフェクトから選択したり、アプリケーション固有のニーズに合わせてカスタムの視覚エフェクトを簡単に構築したりできます。Power BI Embedded は、大規模アプリケーションの一部として、顧客向けに BI を提供している ISV およびアプリケーションの開発者を対象としています。

3.	**誰が Microsoft Power BI Embedded を使用するのですか? また、その理由を教えてください。**

    Microsoft Power BI Embedded は、独自に視覚エフェクトを構築することなく、任意のデバイス間で、魅力的な対話型のデータの視覚エフェクト エクスペリエンスを提供したいと考えているアプリケーション開発者を対象としています。Power BI Embedded を使用することで、開発者は、常に最新のビューを**直接クエリ**で提供できます。また、開発者は、Azure ARM API や Power BI API を使用して、Power BI をプログラム的にデプロイ、管理、および自動化することもできます。その他のサービスと同様に、Power BI Embedded サービスは、使用状況とアプリケーションのニーズに応じて、自動的に拡張します。Power BI Embedded サービスは、従量課金ベースの価格モデルを特徴としています。価格については、[「このサービスはどのように課金されますか?」](#price) を参照してください。

4.	**Power BI Embedded は Power BI サービスとどのように関連していますか?**

    Power BI Embedded と Power BI サービスは別個のサービスです。Power BI Embedded は、Azure ポータルを通じてデプロイされる、使用量ベースの課金モデルを特徴としており、ISV がデータの視覚エフェクトを顧客向けのアプリケーションへ埋め込むことを可能にするよう設計されています。Power BI サービスは O365 ポータルを通して請求およびデプロイが行われ、主に企業による内部使用を対象とするスタンドアロンの汎用 BI サービスです。Power BI サービスの詳細については、[www.powerbi.com](www.powerbi.com) を参照してください。

5.	**Power BI Embedded はどのようにアプリを強化するのですか?**

    データの視覚エフェクトを活用し、直接ユーザーに決定事項を通知することができるよう、アプリケーションを大幅に強化します。Power BI Embedded を使用することで、対話形式の、常に最新かつ豊富なデータの視覚エフェクトを使用してアプリケーションを強化できます。これにより、アプリの有用性、ユーザーの満足度とロイヤルティを向上させ、容易に任意のデバイスへコンテキスト分析機能を提供します。

6.	**Power BI Embedded をアプリで使用するにあたって、規則または制限はありますか。**

    ユーザーが開発したアプリケーションが (1) Power BI Embedded サービスに重要かつ有意な機能を追加しており、主に Power BI サービスの代用ではなく、(2) 外部ユーザーに対して単体で提供されている場合にのみ、そのアプリケーションで Power BI Embedded サービスを利用できます。社内用のビジネス アプリケーション内では Power BI Embedded サービスを使用できません。

7.	**Power BI Embedded は社内アプリケーションの作成に使用できますか?**

    いいえ。Power BI Embedded は外部ユーザーによる使用のみを想定しており、社内のビジネス アプリケーション内では使用できません。社内のビジネス アプリケーションで使用する Power BI コンテンツを埋め込むには、Power BI サービスを使用し、そのコンテンツを使用するすべてのユーザーが、有効な Power BI Free または Power BI Pro のユーザー サブスクリプション ライセンスを保有している必要があります。Power BI サービスを使用した社内での埋め込みの詳細については、[https://dev.powerbi.com](https://dev.powerbi.com) を参照してください。

8.	**このサービスは世界中で使用できますか?**

    Power BI Embedded サービスは、BUILD 2016 (米国中南部データ センター) でお知らせしたように北米で使用できます。他の Azure データ センターにおいても近日サービスの提供を開始することを予定しています。

9. **サービスで利用可能な SLA は何ですか。**

    Power BI Embedded は、現在プレビューの Azure サービスとして利用できますが、正式な SLA は提供されておりません。サービスがプレビューから一般公開リリースへ移行する際に、SLA が提供されます。

<a name="price"/>
10.	**このサービスはどのように課金されますか?**

    Power BI Embedded は現在プレビュー中で、2016 年 5 月 1 日まで無料でご利用いただけます。2016 年 5 月 1 日以降は、レンダーごとに課金される予定です。このサービスは、マイクロソフト オンライン サブスクリプション プログラム (MOSP) またはエンタープライズ VL プログラムという 2 つの主要なライセンス体系で購入いただけます。

    ユーザーが開発したアプリケーションが (1) 当社のサービスに重要かつ有意な機能を追加しており、主に Power BI サービスの代用ではなく、(2) 外部ユーザーに対して単体で提供されている場合にのみ、そのアプリケーションで Power BI Embedded サービスを利用できます。社内用のビジネス アプリケーション内では Power BI Embedded サービスを使用できません。


   ![](media\power-bi-embedded-faq\price.png)

11.	**レンダーとは何ですか? また、レンダーはどのように請求されますか?**

    レンダーは、サービスに対するクエリを必要とするエンドユーザーに視覚エフェクトが表示されるときに発生します。Power BI サービスは、アプリケーションで課金されるレンダーの数を削減するため、可能な場合は、レンダリングされたコンテンツのキャッシュを試行します。ただし、フィルター処理などのユーザー アクションは、結果としてサービスに対するクエリを発生させ、新しいレンダーが構成されます。

    たとえば、ユーザーが 4 つの視覚エフェクトを含むレポートを表示すると、4 つのレンダーが発生します。そのユーザーがレポートの表示を更新し、クエリがさらにサービスに送信されると、レンダーはさらに 4 つ増えます。サービス所有者は、エンドユーザーが有料のレンダーにつながる新しいクエリを実行する範囲を制御し、コストの公開を限定し、静的データのシナリオにおけるコストを最小化することができます。

    レンダーは 1,000 レンダー単位で請求されます。1,000 未満のレンダー量の請求は按分計算されます。月々 1,000 レンダーは無料で使用できます。ボリューム ライセンス契約から購入された場合、料金情報については Microsoft パートナーまたは販売元にお問い合わせください。

12.	**レンダーの数を見積もるためのツールかガイダンスは提供されていますか? また、完了したレンダーの数を確認するにはどうすればよいですか?**

    Azure ポータルでは、サブスクリプションに対して実行されたレンダーの数についての課金の詳細が提供されます。

13.	**Power BI Embedded を使用してアプリケーションを開発するには、Power BI サブスクリプションが必要になりますか? 利用を開始するにはどうすればよいですか?**

    アプリケーション開発者としては、アプリケーションで使用するレポートおよび視覚エフェクトを作成するために、Power BI サブスクリプションを所有している必要はありません。Microsoft Azure サブスクリプションと無料の Power BI Desktop アプリケーションが必要になります。

    Power BI Embedded サービスを使用する方法の詳細については、サービスのドキュメントを参照してください。

14.	**Azure サブスクリプションを所有しています。既存のサブスクリプションを使用して Power BI Embedded を使用できますか?**

    はい。既存の Azure サブスクリプションを使用して、Microsoft Power BI Embedded サービスをプロビジョニングし、使用することができます。

15.	**アプリケーションのエンドユーザーに Power BI ライセンスは必要ですか。**

    いいえ。アプリケーションのエンドユーザーは、アプリ内のデータの視覚エフェクトを利用する際に、個別の Power BI サブスクリプションを購入する必要はありません。Power BI Embedded モデルでは、Azure の従量課金メーターにを通じて、アプリケーション プロバイダーが請求されます。詳細については、[料金とライセンス](http://go.microsoft.com/fwlink/?LinkId=760527)に関するページをご覧ください。

16.	**どのように Power BI Embedded のユーザー認証は処理されますか?**

    Power BI Embedded サービスでは、明示的なエンドユーザー認証ではなく、アプリケーション トークンを使用して認証と承認を行います。アプリケーション トークン モデルでは、アプリケーションがエンドユーザーの認証と承認を管理します。次に、アプリケーションは、必要に応じてアプリケーション トークンを作成して送信し、要求されたレポートをレンダリングするようサービスに伝えます。この設計では、アプリケーションでユーザーの認証と承認に Azure Active Directory を使用する必要はありません (ただし、Azure Active Directory を使用することもできます)。アプリ トークンの詳細については、[アプリ トークン](https://azure.microsoft.com/ja-JP/documentation/articles/power-bi-embedded-get-started-sample/#key-flow) ドキュメント ページをご覧ください。

17.	**Power BI Embedded では現在どのようなデータ ソースがサポートされていますか?**

    サービスのパブリック プレビュー中は、直接クエリを介した、基本的な資格情報を使用するクラウドのデータ ソースへのアクセスをサポートします。これは、Azure SQL DB、HDInsight Spark および Azure SQL DW などのソースが現在サポートされていることを意味します。今後数か月で、その他のデータ ソースのサポートおよびアクセスの種類を追加します。新しくサポートされるデータ ソースについては、Power BI の開発者サイト ([http://dev.powerbi.com](http://dev.powerbi.com/)) でお知らせします。

18.	**Power BI Embedded において、テナント モデルはどのように機能しますか?**

    Power BI Embedded モデルでは、Azure Active Directory (Azure AD) テナントを顧客向けに使用するための明示的な条件はありません。顧客向けに Azure AD を提供するかどうか選択することができます。結果として、アプリケーションとインフラストラクチャのアーキテクチャによって、Power BI Embedded でテナント モデルが必要となるかが決定されます。

    アプリケーションの開発に取り組んでいる開発者または従業員は、Azure ポータルを使用して Azure サブスクリプションとワークスペースのコレクションを管理できるよう、Azure AD ユーザー アカウントを所有している必要があります。開発者は、プログラムの API を使用し、レポートをインポートし、接続文字列を変更することができます。また、代わりに、埋め込み用 URL を取得し、認証用のアプリトークンを使用することも可能です。結果として、Azure AD は不要となります。API と Azure ポータルを使用する方法の詳細については、[Azure.com の Power BI Embedded ドキュメント ページ](https://azure.microsoft.com/ja-JP/documentation/services/power-bi-embedded/)をご覧ください。

19.	**詳細情報を得るには?**

    [Power BI Embedded ドキュメント ページ](http://go.microsoft.com/fwlink/?LinkId=760526)をご覧ください。このサービスの最新情報については、[Power BI の開発者向けブログ](http://blogs.msdn.com/powerbidev)か Power BI デベロッパー センター (dev.powerbi.com) をご覧ください。[Stackoverflow](http://stackoverflow.com/questions/tagged/powerbi) で質問することも可能です。

20.	**開始するには?**

    今すぐ無料で開始できます。 Azure サブスクリプションを所有している場合は、Azure ポータルから直接 Power BI Embedded をプロビジョニングできます。また、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成することも可能です。Power BI Embedded サービスをプロビジョニングしたら、簡単に Power BI REST API を直接使用したり、[GitHub](http://go.microsoft.com/fwlink/?LinkID=746472) で提供されている Developer SDK を使用したりすることができます。Developer SDK を活用する方法については、サンプルが提供されます。

## 関連項目

- [What is Microsoft Power BI Embedded (Microsoft Power BI Embedded とは)](power-bi-embedded-what-is-power-bi-embedded.md)
- [Get started with Microsoft Power BI Embedded Preview (Microsoft Power BI Embedded プレビューの使用を開始する)](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0608_2016-->