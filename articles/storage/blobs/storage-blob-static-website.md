---
title: Azure Storage での静的 Web サイト ホスティング (プレビュー) | Microsoft Docs
description: Azure Storage では、最新の Web アプリケーションをホストするための、コスト効果の高いスケーラブルなソリューションを提供するため、静的 Web サイト ホスティング (プレビュー) が新たにサポートされました。
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: e53b573a27f0b1462ccf1170bbde2f8af01d0d3a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397477"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Azure Storage での静的な Web サイトのホスティング (プレビュー)
Azure Storage では、静的 Web サイト ホスティング (プレビュー) が新たにサポートされ、コスト効果とスケーラビリティに優れた、最新の Web アプリケーションをデプロイできるようになりました。 静的 Web サイト上の Web ページには、静的コンテンツと JavaScript、またはその他のクライアント側コードが含まれます。 これに対し、動的 Web サイトはサーバー側コードに依存し、[Azure Web Apps](/app-service/app-service-web-overview.md) を使用してホストされます。

デプロイのあり方がエラスティックでコスト効果の高いモデルへとシフトしていくなか、サーバー管理を必要とせずに Web コンテンツを提供できるかどうかは、きわめて重要な要件となっています。 Azure Storage では、静的 Web サイト ホスティングを導入することでこれを可能にし、[Azure Functions](/azure-functions/functions-overview.md) やその他の PaaS サービスを活用したサーバーレス アーキテクチャによって、優れたバックエンド機能を提供可能にしています。

## <a name="how-does-it-work"></a>それはどのように機能しますか?
ストレージ アカウントで静的 Web サイトを有効にすると、`<account-name>.<zone-name>.web.core.windows.net` という形式で新しい Web サービス エンドポイントが作成されます。

Web サービス エンドポイントは、常に匿名の読み取りアクセスを許可し、サービス エラーへの応答として書式設定済みの HTML ページを返して、オブジェクトの読み取り操作のみを許可します。 Web サービス エンドポイントは、ルート ディレクトリとすべてのサブディレクトリの両方について、要求されたディレクトリ内のインデックス ドキュメントを返します。 ストレージ サービスが 404 エラーを返した場合、Web エンドポイントはカスタム エラー ドキュメントを返します (そのように構成されている場合)。

静的 Web サイト用のコンテンツは、"$web" という特別なコンテナーでホストされます。 "$web" は有効化プロセスの際に作成されます (まだ作成されていない場合)。 "$Web" 内のコンテンツは、Web エンドポイントを使用してアカウント ルートからアクセスできます。 たとえば、`https://contoso.z4.web.core.windows.net/` は、Web サイト用に構成されたインデックス ドキュメントを返します (その名前のドキュメントが $web のルート ディレクトリに存在する場合)。

Web サイトにコンテンツをアップロードする際には、BLOB ストレージ エンドポイントを使用します。 アカウント ルートからアクセスできる、"image.jpg" という名前の BLOB をアップロードするには、 `https://contoso.blob.core.windows.net/$web/image.jpg` という URL を使用します。 アップロードされた画像は、対応する Web エンドポイント (`https://contoso.z4.web.core.windows.net/image.jpg`) から、Web ブラウザーを使って表示できます。


## <a name="custom-domain-names"></a>カスタム ドメイン名
Web コンテンツは、カスタム ドメインを使ってホストすることもできます。 これを行うには、[Azure Storage アカウント用のカスタム ドメイン名の構成](storage-custom-domain-name.md)に関するガイダンスに従ってください。 カスタム ドメイン名でホストされている Web サイトに HTTPS 経由でアクセスするには、「[カスタム ドメインを用いた BLOB に Azure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)」をご覧ください。

## <a name="pricing-and-billing"></a>価格と課金
静的 Web サイト ホスティングは、追加コストなしで提供されます。 Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

## <a name="quickstart"></a>クイック スタート
### <a name="azure-portal"></a>Azure ポータル
Azure Storage 上での Web アプリケーションのホスティングを開始するには、Azure Portal を使用して機能を構成し、左側のナビゲーション バーにある [設定] の [静的な Web サイト (プレビュー)] をクリックします。 [有効] をクリックし、インデックス ドキュメントの名前とカスタム エラー ドキュメントのパス (後者はオプション) を入力します。

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

静的 Web サイトを有効化する際に作成された "$web" コンテナーに、Web アセットをアップロードします。 これは、Azure Portal で直接行うこともできますし、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してディレクトリ構造全体をアップロードすることもできます。 インデックス ドキュメントには、構成済みの名前を使用するようにしてください。 この例では、ドキュメントの名前は、"index.html" です。

> [!NOTE]
> ドキュメント名は大文字と小文字が区別されるため、ストレージのファイル名と正確に一致する必要があります。

最後に、Web エンドポイントに移動して Web サイトをテストします。

## <a name="faq"></a>FAQ
**静的 Web サイトは、すべての種類のストレージ アカウントで使用できますか?**  
いいえ、静的 Web サイト ホスティングは、GPv2 標準ストレージ アカウントでのみ使用できます。

**Storage VNET とファイアウォールのルールは、新しい Web エンドポイントでサポートされますか?**  
はい、新しい Web エンドポイントは、ストレージ アカウント用に構成された VNET とファイアウォールのルールに従って機能します。

## <a name="next-steps"></a>次の手順
* [カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)
* [BLOB または Web エンドポイントのカスタム ドメイン名の構成](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [最初のサーバーレス Web アプリを作成する](https://aka.ms/static-serverless-webapp)
