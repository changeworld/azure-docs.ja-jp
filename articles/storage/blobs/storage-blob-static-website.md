---
title: Azure Storage での静的 Web サイト ホスティング
description: Azure Storage 静的 Web サイト ホスティングは、最新の Web アプリケーションをホストするための、費用対効果の高いスケーラブルなソリューションを提供します。
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 11/04/2021
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: a8bf63007b15446c92adc5419466cb6acdc87f89
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719227"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage での静的 Web サイト ホスティング

*$web* という名前のストレージ コンテナーから直接に、静的コンテンツ (HTML、CSS、JavaScript、画像ファイル) を提供できます。 Azure Storage でコンテンツをホスティングすることで、[Azure Functions](../../azure-functions/functions-overview.md) やその他のサービスとしてのプラットフォーム (PaaS) サービスなど、サーバーレス アーキテクチャを使用できます。 Web サーバーでコンテンツをレンダリングする必要がない場合、Azure Storage の静的 Web サイト ホスティングは優れた選択肢です。 

[!INCLUDE [static websites vs static web apps](../../../includes/storage-blob-static-website-vs-static-web-app.md)]

Web サーバーでコンテンツのレンダリングが必要な場合は、[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用できます。

## <a name="setting-up-a-static-website"></a>静的 Web サイトの設定

静的 Web サイトのホスティングは、ストレージ アカウントで有効にする必要がある機能です。

静的 Web サイトのホスティングを有効にするには、既定のファイルの名前を選択してから、必要に応じて、カスタム 404 ページへのパスを指定します。 アカウントに **$web** という名前の BLOB ストレージ コンテナーがまだ存在しない場合は、自動的に作成されます。 このコンテナーに、サイトのファイルを追加します。

手順を追ったガイダンスについては、「[Host a static website in Azure Storage](storage-blob-static-website-how-to.md)」 (Azure Storage で静的 Web サイトをホストする) を参照してください。

![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** コンテナー内のファイルは大文字と小文字が区別され、匿名アクセス要求によって処理され、読み取り操作によってのみ使用できます。

## <a name="uploading-content"></a>コンテンツのアップロード

これらの任意のツールを使用して、コンテンツを **$web** コンテナーにアップロードできます。

> [!div class="checklist"]
<<<<<<< HEAD
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell モジュール](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 拡張機能](/azure/developer/javascript/tutorial-vscode-static-website-node-01)
=======
> - [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> - [Azure PowerShell モジュール](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> - [AzCopy](../common/storage-use-azcopy-v10.md)
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> - [Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)と [Channel 9 ビデオ デモ](https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player)
>>>>>>> repo_sync_working_branch

## <a name="viewing-content"></a>コンテンツの表示

ユーザーは、Web サイトのパブリック URL を使用して、ブラウザーからサイトのコンテンツを表示できます。 URL を見つけるには、Azure portal、Azure CLI、または PowerShell を使用します。 「[Web サイトの URL を検索する](storage-blob-static-website-how-to.md#portal-find-url)」を参照してください。

ユーザーがサイトを開き、特定のファイル (例: `https://contosoblobaccount.z22.web.core.windows.net`) を指定しない場合、静的 Web サイトのホスティングを有効にしたときに指定したインデックス ドキュメントが表示されます。

サーバーが 404 エラーを返し、Web サイトを有効にしたときに、エラー ドキュメントを指定していない場合、既定の 404 ページがユーザーに返されます。

> [!NOTE]
> [Azure Storage でのクロス オリジン リソース共有 (CORS) のサポート](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)は、静的な Web サイトではサポートされていません。

### <a name="secondary-endpoints"></a>セカンダリ エンドポイント

[セカンダリ リージョンでの冗長性](../common/storage-redundancy.md#redundancy-in-a-secondary-region)を設定した場合は、セカンダリ エンドポイントを使用して Web サイトのコンテンツにアクセスすることもできます。 データはセカンダリ リージョンに非同期にレプリケートされるため、セカンダリ エンドポイントで使用できるファイルが、プライマリ エンドポイントで使用できるファイルと常に同期されているとは限りません。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Web コンテナーのパブリック アクセス レベルの設定の影響

**$web** コンテナーのパブリック アクセス レベルを変更できますが、プライマリ静的 Web サイト エンドポイントに対しては、これらのファイルが匿名アクセス要求によって処理されるため、この変更が無効になります。 つまり、すべてのファイルへのパブリック (読み取り専用) アクセスです。

次のスクリーンショットは、Azure Portal のパブリック アクセス レベル設定を示しています。

![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/anonymous-read-access-configure/configure-public-access-container.png)

プライマリ静的 Web サイト エンドポイントは影響を受けませんが、パブリック アクセス レベルの変更は、プライマリ BLOB サービス エンドポイントには有効です。

**$web** コンテナーのパブリック アクセス レベルを **プライベート (匿名アクセスなし)** から **BLOB (BLOB 専用の匿名読み取りアクセス)** に変更した場合、プライマリ静的 Web サイト エンドポイント `https://contosoblobaccount.z22.web.core.windows.net/index.html` へのパブリック アクセスのレベルは変更されません。

しかし、プライマリ BLOB サービス エンドポイント `https://contosoblobaccount.blob.core.windows.net/$web/index.html` へのパブリック アクセスは、プライベートからパブリックに変更されます。 ユーザーはこれらの 2 つのエンドポイントのいずれかを使用して、このファイルを開けるようになりました。

ストレージ アカウントでパブリック アクセスを無効にしても、そのストレージ アカウントでホストされている静的な Web サイトには影響しません。 詳細については、[コンテナーと BLOB の匿名パブリック読み取りアクセスの構成](anonymous-read-access-configure.md)に関するページを参照してください。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>カスタム ドメインを静的 Web サイトの URL にマップする

静的 Web サイトをカスタム ドメイン経由で利用できるようにすることができます。

カスタム ドメインの HTTP アクセスは、Azure Storage でネイティブにサポートされているため、簡単に有効にすることができます。 HTTPS を有効にするには、Azure CDN を使用する必要があります。これは、Azure Storage がカスタム ドメインを使用した HTTPS をまだネイティブにサポートしていないためです。 手順を追ったガイダンスについては、「[カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)」を参照してください。

ストレージ アカウントが HTTPS 経由での[安全な転送を必要とする](../common/storage-require-secure-transfer.md)ように構成されている場合、ユーザーは HTTPS エンドポイントを使用する必要があります。

> [!TIP]
> Azure でドメインをホストすることを検討してください。 詳しくは、「[Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)」を参照してください。

## <a name="adding-http-headers"></a>HTTP ヘッダーの追加

静的 Web サイト機能の一部としてヘッダーを構成する方法はありません。 ただし、Azure CDN を使用してヘッダーを追加したり、ヘッダー値を追加 (または上書き) したりすることができます。 「[Azure CDN の Standard ルール エンジン リファレンス](../../cdn/cdn-standard-rules-engine-reference.md)」を参照してください。

ヘッダーを使用してキャッシュを制御する場合は、「[キャッシュ規則で Azure CDN キャッシュの動作を制御する](../../cdn/cdn-caching-rules.md)」を参照してください。

## <a name="multi-region-website-hosting"></a>複数リージョンでの Web サイトのホスティング

複数の地域で Web サイトをホストすることを計画している場合は、リージョンのキャッシュに [Content Delivery Network](../../cdn/index.yml) を使用することをお勧めします。 各リージョンで異なるコンテンツを提供する場合は、[Azure Front Door](../../frontdoor/index.yml) を使用します。 これにより、フェールオーバー機能も提供されます。 カスタム ドメインを使用する予定の場合、[Azure Traffic Manager](../../traffic-manager/index.yml) はお勧めしません。 Azure Storage でのカスタム ドメイン名の確認方法が原因で問題が発生する可能性があります。

## <a name="permissions"></a>アクセス許可

静的 Web サイトを有効にできるようになるためのアクセス許可は、Microsoft.Storage/storageAccounts/blobServices/write または共有キーです。  このアクセスを提供する組み込みのロールに、ストレージ アカウント共同作成者が含まれます。

## <a name="pricing"></a>価格

静的 Web サイトのホスティングは無料で有効にできます。 サイトで利用した BLOB ストレージと、運用コストに対してのみ課金されます。 Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

## <a name="metrics"></a>メトリック

静的 Web サイトのページでメトリックを有効にできます。 メトリックを有効にすると、 **$web** コンテナーのファイルに関するトラフィック統計情報が、メトリック ダッシュボードでレポートされます。

静的 Web サイトのページでメトリックを有効にするには、「[Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics)」 (静的 Web サイト ページでメトリックを有効にする) を参照してください。

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png)|![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="faq"></a>よく寄せられる質問

##### <a name="does-the-azure-storage-firewall-work-with-a-static-website"></a>Azure Storage のファイアウォールは静的 Web サイトで動作しますか?

はい。 ストレージ アカウントの[ネットワーク セキュリティ規則](../common/storage-network-security.md) (IP ベースおよび VNET ファイアウォールを含む) は、静的 Web サイトのエンドポイントでサポートされており、Web サイトを保護するために使用できます。

##### <a name="do-static-websites-support-azure-active-directory-azure-ad"></a>静的 Web サイトでは、Azure Active Directory (Azure AD) はサポートされますか?

いいえ。 静的 Web サイトでサポートされるのは、 **$web** コンテナー内のファイルに対する匿名パブリック読み取りアクセスのみです。

##### <a name="how-do-i-use-a-custom-domain-with-a-static-website"></a>静的 Web サイトでのカスタム ドメインの使用方法を教えてください

[Azure Content Delivery Network (Azure CDN)](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled) を使用して、静的 Web サイトで[カスタム ドメイン](./static-website-content-delivery-network.md)を構成できます。 Azure CDN によって、世界中のどこからアクセスする場合でも Web サイトの待ち時間が一貫して短くなります。

##### <a name="how-do-i-use-a-custom-ssl-certificate-with-a-static-website"></a>静的 Web サイトでカスタム SSL 証明書をどのように使用しますか?

[Azure CDN](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled) を使用して、静的 Web サイトで[カスタム SSL](./static-website-content-delivery-network.md) 証明書を構成できます。 Azure CDN によって、世界中のどこからアクセスする場合でも Web サイトの待ち時間が一貫して短くなります。

##### <a name="how-do-i-add-custom-headers-and-rules-with-a-static-website"></a>静的 Web サイトでカスタム ヘッダーとルールをどのように追加しますか?

静的 Web サイトのホスト ヘッダーを構成するには、[Azure CDN - Verizon Premium](../../cdn/cdn-verizon-premium-rules-engine.md) を使用します。 [こちら](https://feedback.azure.com/d365community/idea/694b08ef-3525-ec11-b6e6-000d3a4f0f84)でフィードバックをお寄せください。

##### <a name="why-am-i-getting-an-http-404-error-from-a-static-website"></a>静的 Web サイトで HTTP 404 エラーが発生するのはなぜですか?

これは、正しくない大文字と小文字を使用してファイル名を参照した場合に発生する可能性があります。 たとえば、`index.html` ではなく `Index.html` になります。 静的 Web サイトの URL にあるファイル名と拡張子は、HTTP を介して提供される場合でも大文字と小文字が区別されます。 これは、Azure CDN エンドポイントがまだプロビジョニングされていない場合にも発生することがあります。 新しい Azure CDN をプロビジョニングした後、伝達が完了するまで、最大 90 分間待ってください。

##### <a name="why-isnt-the-root-directory-of-the-website-not-redirecting-to-the-default-index-page"></a>Web サイトのルート ディレクトリが既定のインデックス ページにリダイレクトされないのはなぜですか?

Azure portal で、お使いのアカウントの静的 Web サイト構成ページを開き、 **[インデックス ドキュメント名]** フィールドに設定されている名前と拡張子を見つけてください。 この名前が、ストレージ アカウントの **$web** コンテナーにあるファイルの名前と完全に同じであることを確認します。 静的 Web サイトの URL にあるファイル名と拡張子は、HTTP を介して提供される場合でも大文字と小文字が区別されます。

## <a name="next-steps"></a>次のステップ

<<<<<<< HEAD
* [Azure Storage で静的 Web サイトをホストする](storage-blob-static-website-how-to.md)
* [カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)
* [Azure Functions](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [最初のサーバーレス Web アプリを作成する](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [チュートリアル:Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)
=======
- [Azure Storage で静的 Web サイトをホストする](storage-blob-static-website-how-to.md)
- [カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)
- [Azure Functions](../../azure-functions/functions-overview.md)
- [Azure App Service](../../app-service/overview.md)
- [最初のサーバーレス Web アプリを作成する](/azure/functions/tutorial-static-website-serverless-api-with-database)
- [チュートリアル:Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)
>>>>>>> repo_sync_working_branch
