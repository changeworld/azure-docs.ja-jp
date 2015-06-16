<properties 
	pageTitle="Azure CDN の概要" 
	description="Azure Content Delivery Network (CDN) の概要と、CDN を使用して BLOB と静的コンテンツをキャッシュして高帯域幅コンテンツを配信する方法について説明します。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="akucer" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="akucer"/>

#Azure コンテンツ配信ネットワーク (CDN) の概要

Azure Content Delivery Network (CDN) では、クラウド サービスで使用される Azure の BLOB と静的コンテンツを戦略的に配置された場所にキャッシュし、ユーザーへのコンテンツ配信に最大帯域幅を使用できます。 

既存の CDN 顧客は、[Microsoft Azure の管理ポータル](https://manage.windowsazure.com)から CDN エンドポイントを管理できるようになりました。 


>[AZURE.NOTE] Azure CDN には、Azure Storage または Azure Cloud Services の個別の[課金プラン](http://www.microsoft.com/windowsazure/pricing/)があります。
 

CDN では、世界各地の物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツを配信するためのグローバル ソリューションを開発者に提供します。現在の CDN ノードの場所の一覧については、「[Azure コンテンツ配信ネットワーク (CDN) POP 場所](http://msdn.microsoft.com/library/azure/gg680302.aspx)」を参照してください。

CDN を使用して Azure データをキャッシュすると、次のような利点があります。

- コンテンツ ソースから遠く離れた場所にいる、コンテンツの読み込みに数多くの "インターネット トリップ" が必要なアプリケーションを使用しているエンド ユーザーに対する、パフォーマンスとユーザー エクスペリエンスの向上
- 製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型の配信スケール 


>[AZURE.IMPORTANT] CDN エンドポイントを作成または有効にした場合、世界中に反映されるまでに最大で 60 分かかることがあります。
 
最初にオブジェクトに対する要求が CDN に行われると、オブジェクトは BLOB サービスまたはクラウド サービスから直接取得されます。CDN 構文を使用して要求が行われると、その要求は、要求が行われた場所から最も近い CDN エンドポイントにリダイレクトされ、オブジェクトへのアクセスが提供されます。オブジェクトがそのエンドポイントで見つからない場合は、サービスから取得され、そのエンドポイントにキャッシュされます。有効期間 (TTL) の設定はキャッシュされたオブジェクトでも維持されています。
 
##Azure Storage からのコンテンツのキャッシュ

CDN が Azure ストレージ アカウントで有効になると、パブリック コンテナーに含まれる、匿名アクセスで利用できる BLOB が CDN によってキャッシュされます。Azure CDN では、パブリックで利用可能な BLOB だけをキャッシュできます。BLOB を匿名アクセスでパブリックに利用できるようにするには、コンテナーをパブリックとして指定する必要があります。そうすると、そのコンテナー内のすべての BLOB が匿名読み取りアクセスで利用可能になります。コンテナー データをパブリックにすることや、アクセスをコンテナー内の BLOB だけに制限することもできます。コンテナーと BLOB のアクセス制御を管理する方法については、「[コンテナーと BLOB へのアクセスの制限](http://msdn.microsoft.com/library/azure/dd179354.aspx)」を参照してください。

最適なパフォーマンスを実現するには、10 GB より小さいサイズの BLOB の配信に CDN のエッジ キャッシュを使用します。

ストレージ アカウントに対して CDN アクセスを有効にすると、管理ポータルで、次の形式で CDN ドメイン名が提供されます。: http://<identifier>.vo.msecnd.net/. このドメイン名は、パブリック コンテナー内の blob へのアクセスに使用できます。 たとえば、自分のアカウント、ストレージ アカウント内の音楽をという名前のパブリック コンテナーを指定して、ユーザーにアクセスできます、次の 2 つの Url のいずれかを使用して、そのコンテナー内の blob。

- **Azure BLOB サービスの URL**: `http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN URL**: `http://<identifier>.vo.msecnd.net/music/` 

##Azure Websites からのコンテンツのキャッシュ

Web サイトから CDN を有効にして、画像、スクリプト、スタイルシートなどの Web コンテンツをキャッシュすることができます。「[Azure CDN と Azure Websites の統合](app-service-web/cdn-websites-with-cdn.md)」を参照してください。

Web サイトに対して CDN アクセスを有効にすると、管理ポータルで、http://<識別子>.vo.msecnd.net/ の形式で CDN ドメイン名が提供されます。このドメイン名を使用して、Web サイトからオブジェクトを取得できます。たとえば、パブリック コンテナー名が cdn で、画像ファイルの名前が music.png の場合、ユーザーは次の 2 つのどちらかの URL でオブジェクトにアクセスできます。

- **Azure Website URL**:  `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN URL**:  `http://<identifier>.vo.msecnd.net/cdn/music.png` 
##Azure Cloud Services からのコンテンツのキャッシュ

Azure Cloud Services によって提供される CDN にオブジェクトをキャッシュすることができます。 

Cloud Services のキャッシュには、次の制約があります。 


- 静的コンテンツをキャッシュする場合のみ CDN を使用する。

	>[AZURE.WARNING] 揮発性が高いコンテンツまたは動的コンテンツをキャッシュすると、パフォーマンスに悪影響を与えたり、コンテンツの問題が発生したりする可能性があり、コストの増加につながります。
- クラウド サービスが運用環境にデプロイされている必要がある。
- クラウド サービスが HTTP を使用してポート 80 でオブジェクトを提供する必要がある。
- クラウド サービスでは、キャッシュまたは配信するコンテンツをクラウド サービスの /cdn フォルダーに配置する必要がある。

クラウド サービスに対して CDN アクセスを有効にすると、管理ポータルで、http://<識別子>.vo.msecnd.net/ の形式で CDN ドメイン名が提供されます。このドメイン名を使用して、クラウド サービスからオブジェクトを取得できます。たとえば、クラウド サービス名が myHostedService で、コンテンツを配信する ASP.NET Web ページが music.aspx の場合、ユーザーは次の 2 つのどちらかの URL を使用してオブジェクトにアクセスできます。


- **Azure クラウド サービスの URL**:  `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **Azure CDN URL**:  `http://<identifier>.vo.msecnd.net/music.aspx` 


###クエリ文字列を含む特定のコンテンツのキャッシュ

クエリ文字列を使用して、クラウド サービスから取得したオブジェクトを区別できます。たとえば、クラウド サービスが別のグラフを表示した可能性がある場合、クエリ文字列を渡して必要な特定のグラフを取得することができます。次に例を示します。 

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

クエリ文字列は、文字列リテラルとして渡されます。`?area=2&item=1` など 2 つのパラメーターを受け取るサービスが存在し、`?item=1&area=2` を使用してそのサービスに対して後続の呼び出しを行うと、同じオブジェクトの 2 つのコピーがキャッシュされます。
 

##HTTPS 経由でのキャッシュされたコンテンツへのアクセス


Azure では、HTTPS 呼び出しを使用して CDN からコンテンツを取得できます。これにより、セキュリティ コンテンツの種類の混在に関する警告が発生することなく、CDN にキャッシュされたコンテンツをセキュリティで保護された Web ページに組み込むことができます。

HTTPS を使用してコンテンツにアクセスする場合、次の制約があります。


- CDN によって提供される証明書を使用する必要がある。サード パーティの証明書はサポートされません。
- CDN ドメインを使用して、コンテンツにアクセスする必要がある。現時点では CDN がカスタム証明書をサポートしていないために、HTTPS サポートではカスタム ドメイン名 (CNAME) を使用できません。



HTTPS が有効な場合でも、HTTP と HTTPS の両方を使用して CDN からコンテンツを取得できます。

CDN コンテンツに対して HTTPS を有効にする方法の詳細については、「[Azure のコンテンツ配信ネットワーク (CDN) を有効にする方法](http://msdn.microsoft.com/library/azure/gg680301.aspx)」を参照してください。


##カスタム ドメインを使用したキャッシュされたコンテンツへのアクセス

CDN の HTTP エンドポイントをカスタム ドメイン名にマップし、その名前を使用して CDN からオブジェクトを要求できます。

カスタム ドメインのマッピングの詳細については、「[カスタム ドメインに Content Delivery Network (CDN) コンテンツをマップする方法](http://msdn.microsoft.com/library/azure/gg680307.aspx)」を参照してください。


<!--HONumber=49-->