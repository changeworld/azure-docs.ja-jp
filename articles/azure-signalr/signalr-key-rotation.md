---
title: Azure SignalR Service のアクセス キーのローテーション
description: 顧客がアクセス キーを定期的にローテーションする必要がある理由と、ポータルの GUI と CLI を使用してこれを実行する方法の概要。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636074"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Azure SignalR Service のアクセス キーのローテーション

それぞれの Azure SignalR Service インスタンスには、アクセス キーのペア (主キーとセカンダリ キー) が提供されます。 これらのキーは、サービスへの要求を行うときに SignalR クライアントを認証するために使用されます。 キーは、インスタンス エンドポイントの url に関連付けられます。 キーは安全に保ち、定期的にローテーションしてください。 アクセス キーは 2 つ提供されるため、1 つのキーを使用して接続を保ったままもう 1 つのキーを再生成できます。

## <a name="why-rotate-access-keys"></a>アクセス キーをローテーションする理由

セキュリティ上の理由とコンプライアンス要件により、開発者は定期的にアクセス キーをローテーションすることをお勧めします。

## <a name="how-to-regenerate-access-keys"></a>アクセス キーを再生成する方法

1. [Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。

1. キーを再生成する Azure SignalR Service インスタンスで **[キー]** セクションを見つけます。

1. ナビゲーション メニューで **[キー]** をクリックします。

1. **[プライマリ キーの再生成]** または **[セカンダリ キーの再生成]** をクリックします。

新しいキーとそれに対応する接続文字列が作成されて表示されます。

 ![キーの再生成](media/signalr-key-rotation/regenerate-keys.png)

キーは、[Azure CLI](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew) を使用して再生成することもできます。

## <a name="update-configurations-with-new-connection-strings"></a>新しい接続文字列を使用した構成の更新

1. 新しく生成された接続文字列をコピーします。

1. 新しい接続文字列を使用するようにすべての構成を更新します。

1. 必要に応じて、アプリケーションを再起動します。

## <a name="forced-access-key-regeneration"></a>強制的なアクセス キーの再生成

Azure SignalR Service では、特定の状況下で必須のアクセス キーの再生成が強制的に実行されることがあります。 このサービスでは、電子メールとポータル通知を使用して顧客への通知が行われます。 この通知を受信した場合やアクセス キーを原因とするサービス エラーが発生した場合は、このガイドの手順に従ってキーをローテーションしてください。

## <a name="next-steps"></a>次の手順

適切なセキュリティ プラクティスとして、アクセス キーを定期的にローテーションすることをお勧めします。

このガイドでは、アクセス キーを再生成する方法について学習しました。 OAuth または Azure Functions を使用した認証に関する次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ASP.NET Core Identity と統合する](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [認証を使用してサーバーレスのリアルタイム アプリを構築する](./signalr-authenticate-azure-functions.md)