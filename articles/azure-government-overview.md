<properties 
	pageTitle="Azure の政府機関の概要" 
	description="この記事では、Azure の政府のクラウドの機能と trustworthy の設計とコンプライアンスの連邦法に適用される、状態、およびローカルの政府機関および、パートナーをサポートするために使用されるセキュリティの概要を示します。 " 
	services="azure-government" 
	documentationCenter="" 
	authors="joharve2" 
	manager="required"/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="1/9/2014" 
	ms.author="jharve"/>

#  Microsoft Azure の政府機関の概要 

<p> Microsoft Azure Government は、Microsoft Azure の物理的、ネットワーク的に独立したインスタンスです。この開発者向けガイドには、アプリケーション開発者と管理者が、Azure のこれらの異なるリージョンとやり取りし、操作する必要がある場合の相違点についての詳しい説明が記載されています。


## このトピックの内容


+ [概要](#Overview)
+ [Microsoft Azure Government で現在使用できる機能](#Features)
+ [次のステップ](#next)


## <a name="Overview"></a>概要

Azure の政府は米国政府機関の速度、小数点以下桁数、セキュリティ、コンプライアンス、経済性を必要とする戦略的な政府機関のシナリオをサポートするため、政府コミュニティ クラウド (GCC) です。   マイクロソフトの豊富な経験をパブリック Azure、Office 365、O365 GCC、Microsoft CRM Online などの他の Microsoft のクラウド サービスで、ソフトウェア、セキュリティ、法令遵守、およびコントロールを実現する際に基づいたが開発されました。 

さらに、Azure 政府をより高いレベルのセキュリティを満たすために設計されていますおり、コンプライアンス必要がある機密性の高い、専用、米国のワークロードの米国連邦政府のリスクと承認の管理プログラム (FedRAMP)、部門の防御 Enterprise クラウド サービス ブローカー (ECSB)、犯罪司法機関情報サービス (CJIS) などの法令でセキュリティ ポリシーと正常性の保険の携行性と責任に関する法律 (HIPAA)。     

Azure 政府のクラウド インフラストラクチャ、ファブリック、サービスおよび政府機関が、目標を達成するハイブリッド クラウド ソリューションの構築のために使用できるはフレームワークの概要を以下に示します。次のいくつかのサービスはプレビューで使用可能なだけを参照してください、[領域ページ](http://azure.microsoft.com/regions/#services)一般的に使用可能なサービスに一覧表示される、最も最新です。

![][2]

Azure の政府には、サービスとしてのインフラストラクチャ-、-(IaaS) とプラットフォームとしてのサービス (PaaS) のコア コンポーネントが含まれています。これには、インフラストラクチャ、ネットワーク、記憶域、データ管理、id 管理およびその他の多くのサービスが含まれます。Azure の政府機関は、パブリックの Azure のお客様を活用して、データの同期では地理的レプリケーションや自動スケーリングのような優れた機能をサポートします。Microsoft が両方のリーダーとして識別された<a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a>と<a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/>先頭業界のアナリストです。

加え、信頼性の高いサービスとパブリックの Azure の機能を提供する、Azure 政府は、多くの米国政府のエンティティを提供することで、そのデータがセキュリティで保護されたを保証する機能を提供します。

- **インスタンスの物理的およびネットワーク分離**- 修飾の米国の政府機関やソリューション プロバイダで使用されるパブリックおよび専用の Microsoft Azure から完全に独立したインスタンスは、Azure の政府の環境です。

- **セキュリティ、プライバシー キーを押しながら対応**Microsoft が、堅牢なセキュリティ、プライバシー、およびコンプライアンス対応のコントロールのフレームワークを実装およびを高くするほどに満たすために厳格なその他のコントロール レベルの要件の ECSB への影響のレベルや CJIS - します。

- **データ ストレージ**-、Azure の政府管理 2 データ センター 500 マイルを超える区別します。大陸 United States (CONUS) のデータ センターのすべてのお客様が管理データの格納します。

- **U. s. 担当者**- すべての Azure 政府オペレータや管理者がスクリーンの米国人です。

- **Id 管理**- Azure 政府の環境内での Id 管理は、Azure Active Directory の個別のインスタンス。

- **コンプライアンス**- Microsoft を満たすためと維持、厳密な状態にある連邦法を変更する場合に継続的に投資を行ってと米国政府 FedRAMP、CJIS、ECSB、HIPAA などのローカルの準拠要件にクラウド ソリューションです。

- **クラウドの統合**- Azure 政府はクラウド サービスと 1 TB の記憶域スペースの OneDrive などの強化されたサービスの間でのシングル サインオンできるようにの O365 政府との統合環境を提供します。

Azure の政府機関では、組織の既存のテクノロジへの投資を維持し、クラウド サービスの利点を実現することもできます。Azure の政府機関は、相互運用可能なクラウド プラットフォーム、製品とテクノロジの組織がされているアプリケーションを構築できますのでを一から開きます。政府機関は、ツール、サービス、オペレーティング システム、アーキテクチャ、およびクラウド ソリューションは、Windows、Linux、Oracle、SharePoint、.NET、Java、PHP、Node.js などのフレームワークを選択できます。政府の Azure プラットフォームの柔軟性により、新しい形のクロス エージェンシーとのコラボレーション、アプリケーションの開発、および統合を使用できます。  

米国政府機関のクラウド サービスに関心が確実に Azure 政府が膨大な小数点以下桁数と進化し続けるニーズに合わせての厳格なセキュリティ プラクティスを提供することができます。 







## <a name="Features"></a>Microsoft Azure の政府機関で現在使用できる機能
現在、Azure Government では、米国政府アイオワ州、米国政府バージニア州の両方のリージョンで、次のサービスを使用できます。

- 仮想マシン
- クラウド サービス
- ストレージ
- Active Directory
- Scheduler
- 仮想ネットワーク
- SQL Database

その他のサービスを利用でき、より多くのサービスが継続的に追加される予定です。サービスの最新の一覧、次を参照してください、[領域ページ](http://azure.microsoft.com/regions/#services)使用可能な各領域とそのサービスが強調表示します。  

現時点では、Azure Government をサポートするデータ センターは、米国政府アイオワ州と米国政府バージニア州です。現在のデータ センターと利用可能なサービスについては、上記リージョン ページを参照してください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>次のステップ

詳細および Azure 政府の学習に興味のある場合は、以下のリンクの一部を利用してください。

- **<A href="http://azure.com/gov">取得と Azure の政府機関へのアクセス</a>**

- **<A href="/azure-government-developer-guide">政府の azure 開発者ガイド</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[別の azure.microsoft.com ドキュメントのトピックへのリンク 1]: ../virtual-machines-windows-tutorial/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 2]: ../web-sites-custom-domain-name/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 3]: ../storage-whatis-account/

<!--HONumber=47-->
