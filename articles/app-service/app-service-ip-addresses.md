---
title: Azure App Service における IP アドレス | Microsoft Docs
description: App Service での送信 IP アドレスと受信 IP アドレスの使用方法と、アプリ用の IP アドレスに関する情報の検索方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.openlocfilehash: 752f9d82afafaf7324c0c63c0d7377b952fe0716
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003148"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service における受信 IP アドレスと送信 IP アドレス

[Azure App Service](app-service-web-overview.md) は、[App Service Environment](environment/intro.md) 以外はマルチテナント サービスです。 App Service 環境 ([分離レベル](https://azure.microsoft.com/pricing/details/app-service/)) に含まれていないアプリは、他のアプリとネットワーク インフラストラクチャを共有します。 その結果、アプリの受信 IP アドレスと送信 IP アドレスが異なる可能性があり、特定の状況では変更される可能性もあります。 

[App Service Environment](environment/intro.md) は専用のネットワーク インフラストラクチャを使用するため、App Service 環境で実行されるアプリは、受信接続と送信接続の両方で、静的な専用 IP アドレスを取得します。

## <a name="when-inbound-ip-changes"></a>受信 IP はいつ変更されるか

スケールアウトされたインスタンスの数に関係なく、各アプリは、1 つの受信 IP アドレスを持ちます。 受信 IP アドレスは、次の操作のいずれかを実行したときに変更される可能性があります。

- アプリを削除した後、別のリソース グループ内に再作成する。
- リソース グループ_と_リージョンの組み合わせに含まれる最後のアプリケーションを削除した後、再作成する。
- 証明書の更新中などに既存の SSL バインドを削除する ([証明書の更新](app-service-web-tutorial-custom-ssl.md#renew-certificates)に関する記事を参照してください)。

## <a name="get-static-inbound-ip"></a>静的な受信 IP を取得する

専用の静的な IP アドレスをアプリで使用したい場合があります。 静的な受信 IP アドレスを取得するには、[IP ベースの SSL バインド](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate)を構成する必要があります。 アプリをセキュリティで保護するための SSL 機能を実質的に必要としない場合は、このバインドのために自己署名証明書をアップロードすることもできます。 IP ベースの SSL バインドでは、証明書は IP アドレス自体にバインドされるため、App Service は、静的な IP アドレスをプロビジョニングすることで、これを実現できます。 

## <a name="when-outbound-ips-change"></a>送信 IP はいつ変更されるか

スケールアウトされたインスタンスの数に関係なく、各アプリは、特定の時点で所定の数の送信 IP アドレスを持ちます。 App Service アプリからのすべての送信接続 (バックエンド データベースへの接続など) では、いずれかの送信 IP アドレスを送信元 IP アドレスとして使用します。 特定のアプリ インスタンスが送信接続を行うときに使用する IP アドレスを事前に知ることはできないため、バックエンド サービスは、アプリのすべての送信 IP アドレスに対してファイアウォールを開く必要があります。

アプリの送信 IP アドレス セットは、アプリを下位レベル (**Basic**、**Standard**、および **Premium**) と **Premium V2**レベルの間でスケーリングするときに変更されます。

アプリが使用できるすべての可能な送信 IP アドレスは、価格レベルに関係なく、`possibleOutboundIPAddresses` プロパティを調べることで見つけることができます。 「[IP アドレスを見つける](#find-outbound-ips)」を参照してください。

## <a name="find-outbound-ips"></a>IP アドレスを見つける

Azure ポータルで、現在アプリで使用されている送信 IP アドレスを見つけるには、アプリの左側のナビゲーションで **[プロパティ]** をクリックします。 

[Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行することで、同じ情報を見つけることができます。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

価格レベルに関係なく、アプリのすべての可能な送信 IP アドレスを見つけるには、[Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>次の手順

ソース IP アドレスによって受信トラフィックを制限する方法を確認します。

> [!div class="nextstepaction"]
> [静的 IP の制限事項](app-service-ip-restrictions.md)
