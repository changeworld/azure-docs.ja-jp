---
title: "Azure App Service にコンテンツ配信ネットワークを追加する | Microsoft Docs"
description: "Azure App Service でコンテンツ配信ネットワークを追加して、エッジ ノードから静的ファイルを配信します。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Azure App Service でコンテンツ配信ネットワークを追加する

このチュートリアルでは、Azure App Service にコンテンツ配信ネットワーク (CDN) を追加して、エッジ サーバーに存在する静的コンテンツを配信します。 その一環として、CDN プロファイルを作成することになります。CDN プロファイルとは、最大 10 個の CDN エンドポイントをまとめたものです。

コンテンツ配信ネットワークは、戦略的に配置された場所に静的 Web コンテンツをキャッシュすることによって、ユーザーに対するコンテンツ配信にスループットの最大化を実現できます。 CDN を使用して Web サイト資産をキャッシュすると、次のような利点があります。

* エンド ユーザーのパフォーマンスとユーザー エクスペリエンスの向上。コンテンツの読み込みに複数のラウンドトリップか必要なアプリケーションを使用する場合は特にそうです。
* 製品発表イベントの開始時のような、瞬間的高負荷を処理しやすくする大型のスケーリング。
* ユーザー要求を分散させ、コンテンツをエッジ サーバーから配信することによる、配信元へのトラフィックの削減。

> [!TIP]
> [Azure CDN の POP の場所](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)の最新の一覧を確認してください。
>

## <a name="deploy-the-sample"></a>サンプルのデプロイ

このチュートリアルでは、Web アプリにアプリケーションをデプロイしておく必要があります。 このチュートリアルを進める前に、[静的 HTML に関するクイックスタート](app-service-web-get-started-html.md)の手順に従って準備を整えてください。

## <a name="step-1---login-to-azure-portal"></a>手順 1 - Azure Portal にログインする

最初に、お好きなブラウザーを開いて、Azure [Portal](https://portal.azure.com) にアクセスします。

## <a name="step-2---create-a-cdn-profile"></a>手順 2 - CDN プロファイルを作成する

左側のナビゲーションで、[`+ New`] ボタンをクリックし、**[Web + モバイル]** をクリックします。 [Web + モバイル] カテゴリで **[CDN]** を選択します。

次のフィールドを指定します。

| フィールド | 値の例 | Description |
|---|---|---|
| 名前 | myCDNProfile | CDN プロファイルの名前です。 |
| 場所 | 西ヨーロッパ | これは、CDN プロファイル情報が格納される Azure の場所です。 CDN エンドポイントの場所には影響しません。 |
| リソース グループ | myResourceGroup | リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」をご覧ください。 |
| [価格レベル]  | Standard Akamai | CDN の価格レベルの比較については、[CDN の概要](../cdn/cdn-overview.md#azure-cdn-features)に関する記事をご覧ください。 |

**[作成]**をクリックします。

左側のナビゲーションからリソース グループ ハブを開き、**[myResourceGroup]** を選択します。 リソースの一覧から **[myCDNProfile]** を選択します。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>手順 3 - CDN エンドポイントを作成する

検索ボックスの横にあるコマンドから **[+ エンドポイント]** をクリックします。エンドポイント作成ブレードが開きます。

次のフィールドを指定します。

| フィールド | 値の例 | 説明 |
|---|---|
| 名前 |  | この名前は、ドメイン `<endpointname>.azureedge.net` でキャッシュされたリソースにアクセスする際に使用します。 |
| 配信元の種類 | Web アプリ | 配信元の種類を選択すると、残りのフィールドのコンテキスト メニューが表示されます。 カスタムの配信元を選択した場合には、配信元のホスト名を入力するテキスト フィールドが表示されます。 |
| 配信元のホスト名 | |  ドロップダウンに、指定した種類の利用可能な配信元がすべて一覧表示されます。 [配信元の種類] として [カスタムの配信元]を選択した場合は、カスタムの配信元のドメインを入力します。  |

**[追加]**をクリックします。

エンドポイントが作成されます。Content Delivery Network エンドポイントが作成されると、状態が **[実行中]** に更新されます。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>手順 4 - Azure CDN から配信する

CDN エンドポイントが**[実行中]** になったので、CDN エンドポイントからコンテンツにアクセスできるようになりました。

このチュートリアルでは前提として[静的 HTML に関するクイックスタート](app-service-web-get-started-html.md)を使用しているため、CDNには `css`、`img`、`js` の 3 つのフォルダーがあるはずです。

コンテンツのパスは、Web アプリの URL `http://<app_name>.azurewebsites.net/img/`、CDN エンドポイントの URL `http://<endpointname>.azureedge.net/img/` とも同じです。このため、CDN から静的コンテンツを配信するときは、CDN エンドポイントのドメインを任意の静的コンテンツに置き換えるだけで済みます。

それでは、CDN エンドポイントから最初の画像を取得してみましょう。これには、ブラウザーで以下の URL に移動します。

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

CDN で静的コンテンツが利用できるようになったため、CDN エンドポイントを使ってエンド ユーザーにコンテンツを配信するようにアプリケーションを更新できます。

サイトの構築に使用している言語によっては、CDN のフォールバックに役立つフレームワークが多数存在します。 たとえば、ASP.NET では CDN フォールバック機能を実現するものとして[バンドルと縮小](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn)をサポートしています。

使用している言語が初めから CDN フォールバックをサポートしていない場合や、使用している言語に CDN フォールバックをサポートするライブラリがない場合でも、[FallbackJS](http://fallback.io/) などの JavaScript フレームワークを使えば、[スクリプト](https://github.com/dolox/fallback/tree/master/examples/loading-scripts)、[スタイルシート](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets)、[画像](https://github.com/dolox/fallback/tree/master/examples/loading-images)の読み込みをサポートできます。

## <a name="step-5---purge-the-cdn"></a>手順 5 - CDN のコンテンツを消去する

有効期間 (TTL) が満了する前にコンテンツを終了する場合など、CDN のコンテンツを強制的に消去する作業が必要になることもあります。

Azure CDN のコンテンツは手動で消去できます。これは、[CDN プロファイル] ブレードまたは [CDN エンドポイント] ブレードから実行します。 プロファイル ページから消去を選択した場合は、コンテンツを消去するエンドポイントを選択する必要があります。

コンテンツを消去するには、消去するコンテンツのパスを入力します。 個々のファイルを消去するには、ファイルの完全パスを渡します。特定のフォルダーからコンテンツを消去し、コンテンツを最新の情報に更新するには、パス セグメントを渡します。

消去するコンテンツのパスをすべて入力したら、**[消去]** をクリックします。

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>手順 6 - カスタム ドメインをマップする

カスタム ドメインを CDN エンドポイントにマップすると、Web アプリケーションに一貫したドメインを実現できます。

カスタム ドメインを CDN エンドポイントにマップするには、ドメイン レジストラーで CNAME レコードを作成します。

> [!NOTE]
> CNAME レコードは、`www.contosocdn.com` や `static.contosocdn.com` のようにソース ドメインを目的のドメインにマッピングする DNS 機能です。

今回の場合、ソース ドメイン `static.contosocdn.com` を追加し、これが対象のドメイン (CDN エンドポイント) を指すようにマップします。

| ソース ドメイン | 対象ドメイン |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

[CDN Endpoint overview (CDN エンドポイントの概要)] ブレードで、[`+ Custom domain`] ボタンをクリックします。

[カスタム ドメインの追加] ブレードで、サブドメインを含むカスタム ドメインをダイアログ ボックスに入力します。 たとえば、`static.contosocdn.com` という形式でドメイン名を入力します。

**[追加]**をクリックします。

## <a name="step-7---version-content"></a>手順 7 - コンテンツのバージョン管理

CDN エンドポイントの左側のナビゲーションで、[設定] という見出しの下にある **[キャッシュ]** を選択します。

**[キャッシュ]** ブレードでは、CDN が要求のクエリ文字列をどのように処理するかを構成できます。

> [!NOTE]
> クエリ文字列のキャッシュの動作に関するオプションについて詳しくは、トピック「[クエリ文字列による Azure CDN キャッシュ動作の制御](../cdn/cdn-query-string.md)」をご覧ください。

クエリ文字列のキャッシュ動作は、ドロップダウンから、**[一意の URL をすべてキャッシュ]** を選択します。

**[Save]**をクリックします。

## <a name="next-steps"></a>次のステップ

* [Azure CDN とは](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [Azure CDN カスタム ドメインの HTTPS の有効化](../cdn/cdn-custom-ssl.md)
* [Azure CDN でのファイル圧縮によるパフォーマンスの向上](../cdn/cdn-improve-performance.md)
* [Azure CDN エンドポイント上のアセットを事前に読み込む](../cdn/cdn-preload-endpoint.md)

