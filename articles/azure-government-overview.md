<properties 
   pageTitle="Azure Government の概要" 
   description="この記事では、Azure Government のクラウド機能の概要と、連邦、州、地方の各統治機関や関連組織に対して適用されるコンプライアンスをサポートする安定した設計やセキュリティの概要について説明します。" 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="10/28/2015"
   ms.author="jharve"/>

# Microsoft Azure Government の概要 

<p> Microsoft Azure Government は、Microsoft Azure の物理的、ネットワーク的に独立したインスタンスです。この概要は、環境およびサービスへの入門、追加情報へのリンクを提供しています。


## <a name="Overview"></a>概要

Azure Government は、スピード、規模、セキュリティ、コンプライアンス、経済性を必要とする、米国政府機関の戦略的なシナリオをサポートする政府コミュニティ クラウド (GCC) です。Microsoft が他のクラウド (Azure パブリック、Office 365、O365 GCC、Microsoft CRM オンラインなど) で培ったソフトウェア、セキュリティ、コンプライアンス、コントロールについての豊富な経験をもとに開発されています。

加えて、Azure Government ではさらに高いレベルのセキュリティやコンプライアンスに関する要求水準を満たしており、米国連邦政府のリスクと認証管理プログラム (FedRAMP)、米国国防総省エンタープライズ クラウド サービス ブローカー (ECSB)、犯罪司法情報サービス部 (CJIS) のセキュリティ ポリシー、医療保険の相互運用性と説明責任に関する法律 (HIPAA) など、秘密性が高く、専用の、米国公的機関のワークロードで採用されています。

下の画像は Azure Government のインフラストラクチャ、ファブリック、サービス、フレームワークのサマリ ビューです。これによって、政府機関のニーズに応えてハイブリッドなクラウド ソリューションの構築が可能になります。一般公開されている最新のサービスの一覧については、[リージョンに関するページ](http://azure.microsoft.com/regions/#services)を参照してください。

![][2]

Azure Government には、Infrastructure-as-a-Service (IaaS) や Platform-as-a-Service (PaaS) の主要なコンポーネントが含まれています。主要コンポーネントには、インフラストラクチャ、ネットワーク、ストレージ、データ管理、ID 管理、その他多くのサービスが含まれます。Azure Government は、geo 同期データ複製や自動スケーリングなど、Azure で採用されているものと同じ機能をサポートしています。Microsoft は、<a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> や <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/> などの技術の最先端に立っており、業界をリードしています。

Azure で信頼性の高いサービスや機能を公的に提供しているだけでなく、Azure Government で米国政府機関に対して多くの機能を提供し、データの安全性を保証しています。

- **物理的、ネットワーク的に分離したインスタンス** - Azure Government の環境は Microsoft Azure パブリックとは完全に切り離されたインスタンスであり、認証された米国政府機関やソリューション プロバイダーのみが使用できます。

- **セキュリティ、プライバシー、コンプライアンス** - Microsoft は堅固なセキュリティ、プライバシー、コンプライアンスを制御するフレームワークを備え、加えて ECSB のインパクト レベルや CJIS の非常に高い要求水準を満たすための厳しい制御を保っています。

- **データ ストレージ** - Azure Government 環境は、800 マイル (1,280 km) 離れた 2 つのデータセンターを管理します。すべてのユーザーのデータは米国本土 (CONUS) のデータセンターに保存されます。

- **米国の担当者** - Azure Government のオペレーターや管理者はすべて、米国国民であることが確認されています。

- **ID 管理** - Azure Government 環境における ID 管理は、Azure Active Directory の別のインスタンスで行われます。

- **コンプライアンス** - Microsoft は継続的に、FedRAMP、CJIS、ECSB、HIPAA、IRS 1075 などの米国政府クラウド ソリューションに対して連邦政府、州政府、地方自治体が要求する、厳格で変更のあるコンプライアンス水準を満たし、それを維持し続けています。

- **クラウドの統合** - Azure Government は、Office 365 Government との統合環境を提供し、クラウド サービスや 1 TB (テラバイト) の OneDrive ストレージ容量などの拡張サービスをシングル サインオンで使うことができます。

また Azure Government を使うことで、組織が既存のテクノロジーへの投資を継続しながらクラウド サービスのメリットも享受できるようになります。Azure Government は相互運用が可能なクラウド プラットフォームであるため、さまざまな製品やテクノロジーと組み合わせることで、組織がさらにオープンななアプリケーションをゼロから構築できるようにもなります。クラウド ソリューションとして、Windows、Linux、Oracle、SharePoint、.NET、Java、PHP、Node.js などのさまざまなツール、サービス、オペレーティング システム、アーキテクチャ、フレームワークなどを選択できます。Azure Government プラットフォームは非常に柔軟に使うことができるため、複数の機関が協力して新しい形で運用したり、アプリケーションを開発したり、統合したりできます。

クラウド サービスに関心を示す米国政府機関によって、Azure Government がその発展していくニーズにあわせて非常に大きな規模に対応し、厳格なセキュリティを持っていることが認識されています。







## <a name="Features"></a> Microsoft Azure Government で現在使用できる機能
米国政府アイオワ州と米国政府バージニア州の両方のリージョンで、次の Azure Government サービスを使用できます。

- Virtual Machines
- Cloud Services
- Storage
- Active Directory
- Scheduler
- 仮想ネットワーク
- SQL Database
- Azure Files
- Media Services
- Traffic Manager
- Service Bus
- StorSimple
- Redis Cache
- Azure Backup
- Automation
- ExpressRoute
- など

その他のサービスを利用でき、より多くのサービスが継続的に追加される予定です。最新のサービスのリストについては、各リージョンで提供されているサービスを紹介している[リージョンに関するページ](http://azure.microsoft.com/regions/#services)を参照してください。

現時点では、Azure Government をサポートするデータ センターは、米国政府アイオワ州と米国政府バージニア州です。現在のデータ センターと利用可能なサービスについては、上記リージョン ページをご覧ください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>次のステップ

Azure Government のさらに詳細については、次のページをご覧ください。

- **[Sign up for a trial (サインアップして試用)](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Azure Government の入手とアクセス](http://azure.com/gov)**

- **[Azure Government 開発者向けガイド](/azureazure-government-developer-guide)**

- **[Azure Government のブログ](http://blogs.msdn.com/b/azuregov/)**

- **[Microsoft Azure セキュリティ センター: コンプライアンス](http://azure.microsoft.com/support/trust-center/compliance/)**



<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=Nov15_HO1-->