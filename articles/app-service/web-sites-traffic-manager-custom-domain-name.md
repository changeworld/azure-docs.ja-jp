---
title: Traffic Manager を使用した DNS 名の構成
description: 負荷分散のために Traffic Manager と統合する Azure App Service アプリのカスタムドメインを構成する方法について学びます。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 08/17/2016
ms.custom: seodec18
ms.openlocfilehash: 9139b83f1f2920da47b4a0d440f622626d41c938
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689273"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、負荷分散のために [Traffic Manager](../traffic-manager/traffic-manager-overview.md) と統合されている [App Service](overview.md) アプリでカスタム ドメイン名を使用する一般的な手順を示します。

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS レコードについて
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Web アプリの標準モード用の構成
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>カスタム ドメインの DNS レコードの追加
> [!NOTE]
> Azure App Service Web Apps からドメインを購入した場合は、次の手順をスキップして、「 [Web Apps 用のドメインの購入](manage-custom-dns-buy-domain.md) 」の記事の最後の手順をご覧ください。
> 
> 

カスタム ドメインを Azure App Service の Web アプリに関連付けるには、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。 これは、ドメイン プロバイダーからの管理ツールを使用して行います。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

ドメイン プロバイダーによって仕様が異なりますが、カスタム ドメイン名 (**contoso.com** など) "*から*"、Web アプリと統合されている Traffic Manager のドメイン名 (**contoso.trafficmanager.net**) "*に*" マッピングします。

> [!NOTE]
> レコードが既に使用されており、事前にアプリをバインドする必要がある場合は、追加の CNAME レコードを作成できます。 たとえば、**www\.contoso.com** を Web アプリに事前にバインドするには、**awverify.www** から **contoso.trafficmanager.net** への CNAME レコードを作成します。 その後、"www" CNAME レコードに変更を加えることなく、"www\.contoso.com" を Web アプリに追加できます。 詳細については、「[カスタム ドメインにおける Web アプリの DNS レコードの作成][CREATEDNS]」をご覧ください。

ドメイン プロバイダーで DNS レコードの追加または変更が完了したら、変更を保存します。

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager を有効にする
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>次の手順
詳細については、 [Node.js デベロッパー センター](https://azure.microsoft.com/develop/nodejs/)を参照してください。

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
