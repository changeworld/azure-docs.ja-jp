<properties 
	pageTitle="CDN を使用する方法 | Microsoft Azure" 
	description="Azure Content Delivery Network (CDN) を使用して、BLOB と静的コンテンツをキャッシュすることにより、高帯域幅コンテンツを配信する方法について説明します。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Azure 用 CDN の使用

Azure Content Delivery Network (CDN) は、Azure で任意の HTTP アプリケーションを拡張するための基本的要素です。CDN は、Azure のお客様にグローバル ソリューションを提供するもので、エンド ユーザーに近い場所にコンテンツをキャッシュして配信します。そのため、ユーザーからの要求は、毎回オリジンを参照する代わりに、最適なパフォーマンスが得られる CDN エッジ POP にインテリジェントにルーティングされます。これにより、パフォーマンスとユーザー エクスペリエンスが大幅に向上します。現在の CDN ノードの場所の一覧については、「[Azure Content Delivery Network (CDN) POP 場所](cdn-pop-locations.md)」を参照してください。

CDN を使用して Azure データをキャッシュすることには、次のような利点があります。

- コンテンツ ソースから遠く離れた場所にいる、コンテンツの読み込みに数多くの HTTP 要求が必要なアプリケーションを使用するエンド ユーザーに対する、パフォーマンスとユーザー エクスペリエンスの向上。
- 製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型の配信スケール。
- ユーザー要求を分散させ、コンテンツをグローバル エッジ POP から配信することによる、配信元へのトラフィックの削減。

>[AZURE.TIP]Azure CDN では、さまざまな配信元からコンテンツを配信できます。Azure 内で統合された配信元には、App Service、Cloud Services、Blob Storage、Media Services が含まれます。パブリックにアクセスできる Web アドレスを使用して、カスタム配信元を定義することもできます。

##CDN を有効にする方法

1. 配信元を指すエンドポイントで CDN プロファイルを作成します。

	CDN プロファイルは、CDN エンドポイントのコレクションです。各プロファイルには、1 つ以上の CDN エンドポイントが含まれます。CDN プロファイルを作成すると、選択した配信元を使用して、新しい CDN エンドポイントを作成できます。
	
	>[AZURE.NOTE]1 つの Azure サブスクリプションは、4 つの CDN プロファイルに限定されます。各 CDN プロファイルは、4 つの CDN エンドポイントに制限されます。
	>
	> CDN の価格は、CDN プロファイル レベルで適用されます。Standard と Premium の CDN 機能を組み合わせて使用する場合は、複数の CDN プロファイルが必要になります。
	
	CDN のプロファイルとエンドポイントの作成に関する詳細なチュートリアルについては、「[Azure の Content Delivery Network (CDN) を有効にする方法](cdn-create-new-endpoint.md)」を参照してください。
	
2. CDN 構成を設定する

	CDN エンドポイントでは、[キャッシュ ポリシー](cdn-caching-policy.md)、[クエリ文字列のキャッシュ](cdn-query-string.md)、[ルール エンジン](cdn-rules-engine.md)など、さまざまな機能を有効にすることができます。詳細については、左側の **[管理]** メニューを参照してください。

3. CDN コンテンツにアクセスする

	CDN にキャッシュされたコンテンツにアクセスするには、ポータルで提供される CDN URL を使用します。たとえば、キャッシュされた BLOB のアドレスは、次のようになります。`http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Azure Storage からのコンテンツのキャッシュ

CDN が Azure ストレージ アカウントで有効になると、パブリック コンテナーに含まれる、匿名アクセスで利用できる BLOB が CDN によってキャッシュされます。Azure CDN では、パブリックで利用可能な BLOB だけをキャッシュできます。BLOB を匿名アクセスでパブリックに利用できるようにするには、コンテナーをパブリックとして指定する必要があります。そうすると、そのコンテナー内のすべての BLOB が匿名読み取りアクセスで利用可能になります。コンテナー データをパブリックにすることや、アクセスをコンテナー内の BLOB だけに制限することもできます。コンテナーと BLOB のアクセス制御を管理する方法については、[Azure Storage のリソースに対するアクセスの管理](../storage/storage-manage-access-to-resources.md)に関するページを参照してください。

最適なパフォーマンスを実現するには、10 GB より小さいサイズの BLOB の配信に CDN のエッジ キャッシュを使用します。

ストレージ アカウントに対して CDN アクセスを有効にすると、管理ポータルでは、次の形式で CDN ドメイン名が提供されます。`http://<identifier>.azureedge.net/`このドメイン名を使ってパブリック コンテナー内の BLOB にアクセスできます。たとえば、myaccount というストレージ アカウントに music というパブリック コンテナーがある場合は、次の URL のどちらかを使えば、そのコンテナー内の BLOB にアクセスできます。

- **Azure BLOB サービスの URL**: `http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN の URL**: `http://<identifier>.azureedge.net/music/` 

## Azure Websites からのコンテンツのキャッシュ

Web サイトから CDN を有効にして、画像、スクリプト、スタイルシートなどの Web コンテンツをキャッシュすることができます。「[Azure CDN と Azure Websites の統合](../app-service-web/cdn-websites-with-cdn.md)」を参照してください。

Web サイトに対して CDN アクセスを有効にすると、管理ポータルでは、次の形式で CDN ドメイン名が提供されます。`http://<identifier>.azureedge.net/`このドメイン名を使用して、Web サイトからオブジェクトを取得できます。たとえば、パブリック コンテナー名が cdn で、画像ファイルの名前が music.png の場合、ユーザーは次の 2 つのどちらかの URL でオブジェクトにアクセスできます。

- **Azure Web サイトの URL**: `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN の URL**: `http://<identifier>.azureedge.net/cdn/music.png`
 
## Azure クラウド サービスからのコンテンツのキャッシュ

Azure クラウド サービスによって提供される CDN にオブジェクトをキャッシュすることができます。

クラウド サービスのキャッシュには、次の制約があります。


- 静的コンテンツをキャッシュする場合のみ CDN を使用する。

	>[AZURE.WARNING]揮発性が高いコンテンツまたは動的コンテンツをキャッシュすると、パフォーマンスに悪影響を与えたり、コンテンツの問題が発生したりする可能性があり、コストの増加につながります。
- クラウド サービスが運用環境にデプロイされている必要がある。
- クラウド サービスが HTTP を使用してポート 80 でオブジェクトを提供する必要がある。
- クラウド サービスでは、キャッシュまたは配信するコンテンツをクラウド サービスの /cdn フォルダーに配置する必要がある。

クラウド サービスに対して CDN アクセスを有効にすると、管理ポータルでは、次の形式で CDN ドメイン名が提供されます。`http://<identifier>.azureedge.net/`このドメイン名を使用して、クラウド サービスからオブジェクトを取得できます。たとえば、クラウド サービス名が myHostedService で、コンテンツを配信する ASP.NET Web ページが music.aspx の場合、ユーザーは次の 2 つのどちらかの URL を使用してオブジェクトにアクセスできます。


- **Azure クラウド サービスの URL**: `http://myHostedService.cloudapp.net/music.aspx` 
- **Azure CDN の URL**: `http://<identifier>.azureedge.net/music.aspx` 

## カスタム配信元からコンテンツをキャッシュする

パブリックにアクセスできる Web アプリケーションから提供されるオブジェクトを CDN にキャッシュできます。

カスタム配信元のキャッシュには、次の制約があります。

- 静的コンテンツをキャッシュする場合のみ CDN を使用する。

	>[AZURE.WARNING]揮発性が高いコンテンツまたは動的コンテンツをキャッシュすると、パフォーマンスに悪影響を与えたり、コンテンツの問題が発生したりする可能性があり、コストの増加につながります。
- カスタム発信元のコンテンツは、パブリック IP アドレスを持つサーバーでホストする必要があります。CDN のエッジ ノードでは、ファイアウォールの内側にあるイントラネット サーバーから資産を取得できません。

カスタム配信元に対して CDN アクセスを有効にすると、Azure ポータルでは、次の形式で CDN ドメイン名が提供されます。`http://<identifier>.azureedge.net/`このドメイン名を使用して、カスタム配信元からオブジェクトを取得できます。たとえば、www.contoso.com にあるサイトで、コンテンツを配信する ASP.NET Web ページが music.aspx の場合、ユーザーは次の 2 つのどちらかの URL を使用してオブジェクトにアクセスできます。


- **カスタム発信元の URL**: `http://www.contoso.com/music.aspx` 
- **Azure CDN の URL**: `http://<identifier>.azureedge.net/music.aspx` 

## クエリ文字列を含む特定のコンテンツのキャッシュ

クエリ文字列を使用して、発信元から取得したオブジェクトを区別できます。たとえば、発信元が別のグラフを表示した可能性がある場合、クエリ文字列を渡して必要な特定のグラフを取得することができます。次に例を示します。

`http://<identifier>.azureedge.net/chart.aspx?item=1`

クエリ文字列は、文字列リテラルとして渡されます。`?area=2&item=1` など 2 つのパラメーターを受け取るサービスが存在し、`?item=1&area=2` を使用してその発信元に対して後続の呼び出しを行うと、同じオブジェクトの 2 つのコピーがキャッシュされます。

クエリ文字列のキャッシュの詳細については、[クエリ文字列による CDN 要求のキャッシュ動作の制御](cdn-query-string.md)に関するページを参照してください。

## HTTPS 経由でのキャッシュされたコンテンツへのアクセス

Azure では、HTTPS 呼び出しを使用して CDN からコンテンツを取得できます。これにより、セキュリティ コンテンツの種類の混在に関する警告が発生することなく、CDN にキャッシュされたコンテンツをセキュリティで保護された Web ページに組み込むことができます。

HTTPS を使用して CDN コンテンツにアクセスする場合、次の制約があります。


- CDN によって提供される証明書を使用する必要がある。サード パーティの証明書はサポートされません。
- CDN ドメインを使用して、コンテンツにアクセスする必要がある。現時点では CDN がカスタム証明書をサポートしていないために、HTTPS サポートではカスタム ドメイン名 (CNAME) を使用できません。

CDN コンテンツに対して HTTPS を有効にする方法の詳細については、「[Azure の Content Delivery Network (CDN) を有効にする方法](cdn-create-new-endpoint.md)」を参照してください。


## カスタム ドメインを使用したキャッシュされたコンテンツへのアクセス

CDN の HTTP エンドポイントをカスタム ドメイン名にマップし、その名前を使用して CDN からオブジェクトを要求できます。

カスタム ドメインのマッピングの詳細については、「[カスタム ドメインに Content Delivery Network (CDN) コンテンツをマップする方法](cdn-map-content-to-custom-domain.md)」を参照してください。

## 関連項目

- [Azure の Content Delivery Network を有効にする方法](cdn-create-new-endpoint.md)
- [Azure Content Delivery Network (CDN) の概要](cdn-overview.md)
- [Azure CDN エンドポイントの消去](cdn-purge-endpoint.md)
 

<!---HONumber=AcomDC_1210_2015-->