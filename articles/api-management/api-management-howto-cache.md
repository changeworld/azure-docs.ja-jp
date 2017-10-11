---
title: "Azure API Management でパフォーマンスを向上させるためにキャッシュを追加 |Microsoft ドキュメント"
description: "待機時間、帯域幅の消費量、および API Management サービスの呼び出しのための web サービスの負荷を向上させる方法を説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 59c595f0d5ce849f44c46fdb6cab0b44d35fffa0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Azure API Management でパフォーマンスを向上させるためにキャッシュを追加します。
API Management での操作は、応答のキャッシュ用に構成できます。 応答のキャッシュできますが大幅に API の待機時間、帯域幅の消費量を減らすし、web サービスの負荷が頻繁に変化しないデータのです。

このガイドでは、API の応答のキャッシュを追加し、サンプルのエコー API 操作のポリシーを構成する方法を示します。 キャッシュの動作を確認する開発者ポータルから、操作を呼び出すことができますし、します。

> [!NOTE]
> ポリシー式を使用してキーによって項目をキャッシュする方法については、次を参照してください。 [Azure API Management でキャッシュ カスタム](api-management-sample-cache-by-key.md)です。
> 
> 

## <a name="prerequisites"></a>前提条件
このガイドの手順の前に、API を使用して、API Management サービス インスタンスが必要し、製品を構成します。 API Management サービス インスタンスを作成がない場合は、次を参照してください。 [API Management サービス インスタンスを作成する][ Create an API Management service instance]で、 [Azure API Management の概要][ Get started with Azure API Management]チュートリアルです。

## <a name="configure-caching"></a>キャッシュの操作を構成します。
この手順では、キャッシュの設定を確認します、 **(キャッシュ済み) を取得リソース**サンプル エコー API の操作です。

> [!NOTE]
> 各 API Management サービス インスタンスは、API 管理の詳細をテストに使用できるエコー API を使用してあらかじめ構成されたものです。 詳細については、次を参照してください。 [Azure API Management の概要][Get started with Azure API Management]です。
> 
> 

クリックして、作業を開始する**パブリッシャー ポータル**API Management サービス用の Azure ポータルでします。 API Management パブリッシャー ポータルに移動します。

![パブリッシャー ポータル][api-management-management-console]

をクリックして**Api**から、 **API Management** 、左側のメニューをクリックし、**エコー API**です。

![エコー API][api-management-echo-api]

をクリックして、**操作** タブをクリックして、 **(キャッシュ済み) を取得リソース**から操作、**操作** ボックスの一覧です。

![エコー API 操作][api-management-echo-api-operations]

クリックして、**キャッシュ**タブにはこの操作のキャッシュ設定を表示します。

![[キャッシュ] タブ][api-management-caching-tab]

操作のキャッシュを有効にするには選択、**を有効にする**チェック ボックスをオンします。 この例ではキャッシュが有効になっています。

各操作の応答がキーの値に基づいて、**クエリ文字列パラメーターによって変わる場合があります**と**Vary ヘッダーによって**フィールドです。 クエリ文字列パラメーターまたはヘッダーに基づいて複数の応答をキャッシュする場合は、これら 2 つのフィールドで構成できます。

**期間**キャッシュされた応答の有効期限の間隔を指定します。 間隔は、この例では**3600** (秒単位) は 1 時間に相当します。

この例では、最初の要求では、キャッシュの構成を使用して、 **(キャッシュ済み) を取得リソース**操作がバックエンド サービスから応答を返します。 指定されたヘッダーとクエリ文字列パラメーターをキーと、この応答がキャッシュされます。 一致するパラメーターを使用して、操作を後続の呼び出しは、返される、キャッシュの存続期間の間隔が経過するまでキャッシュされた応答があります。

## <a name="caching-policies"></a>キャッシュ ポリシーの確認
このステップでのキャッシュの設定を確認する、 **(キャッシュ済み) を取得リソース**サンプル エコー API の操作です。

キャッシュの設定が構成されている場合、操作で、**キャッシュ**] タブの [キャッシュ操作のポリシーが追加されます。 これらのポリシーを表示し、ポリシー エディターで編集できます。

をクリックして**ポリシー**から、 **API Management** 、左側のメニューを選択し、**エコー API (キャッシュ済み) のリソースを取得/**から、**操作**ドロップダウン リスト。

![ポリシーの範囲の操作][api-management-operation-dropdown]

これには、ポリシー エディターでこの操作のポリシーが表示されます。

![API Management ポリシー エディター][api-management-policy-editor]

この操作のポリシー定義には使用して確認されたキャッシュの構成を定義するポリシーが含まれています、**キャッシュ** タブで、前の手順です。

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> ポリシー エディターでのキャッシュ ポリシーに加えられた変更に反映する、**キャッシュ**操作、およびその逆のタブです。
> 
> 

## <a name="test-operation"></a>操作の呼び出しし、キャッシュをテスト
アクションでのキャッシュを表示するには、開発者ポータルから、操作を呼び出すおことができます。 をクリックして**開発者ポータル**右上のメニューにします。

![開発者ポータル][api-management-developer-portal-menu]

をクリックして**Api**クリックし、上部のメニューで**エコー API**です。

![エコー API][api-management-apis-echo-api]

> 自分のアカウントを表示または構成されている 1 つだけの API があれば、Api をクリックしを実行する api を操作します。
> 
> 

選択、 **(キャッシュ済み) を取得リソース**クリックして、操作**コンソールを開く**です。

![コンソールを開く][api-management-open-console]

コンソールでは、呼び出し、開発者ポータルから直接操作することができます。

![コンソール][api-management-console]

既定値をそのまま**param1**と**param2**です。

任意のキーを選択して、**サブスクリプション キー**ドロップダウン リスト。 自分のアカウントに 1 つのサブスクリプションがある場合は、既に選択されています。

入力**sampleheader:value1**で、**要求ヘッダー**テキスト ボックス。

をクリックして**HTTP Get**応答ヘッダーを書き留めます。

入力**sampleheader:value2**で、**要求ヘッダー**テキスト ボックス、およびクリック**HTTP Get**です。

なおの値**sampleheader**が**value1**応答でします。 いくつかの異なる値との最初の呼び出しからキャッシュされた応答が返されることに注意してください。

入力**25**に、 **param2**フィールドをクリックして**HTTP Get**です。

なおの値**sampleheader**応答では、今すぐ**value2**です。 操作の結果はクエリ文字列によってキー指定され、ため、前のキャッシュされた応答が返されませんでした。

## <a name="next-steps"></a>次の手順
* キャッシュ ポリシーの詳細については、次を参照してください。[キャッシュ ポリシー] [ Caching policies]で、 [API Management ポリシー リファレンス][API Management policy reference]です。
* ポリシー式を使用してキーによって項目をキャッシュする方法については、次を参照してください。 [Azure API Management でキャッシュ カスタム](api-management-sample-cache-by-key.md)です。

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
