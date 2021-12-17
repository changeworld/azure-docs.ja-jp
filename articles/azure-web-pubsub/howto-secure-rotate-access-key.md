---
title: Azure Web PubSub サービスのアクセス キーのローテーション方法
description: 顧客がアクセス キーを定期的にローテーションする必要がある理由と、その実行方法の概要。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: e89366e924ae7f079816980e6fcbefd6366eb3b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997763"
---
# <a name="how-to-rotate-access-key-for-azure-web-pubsub-service"></a>Azure Web PubSub サービスのアクセス キーのローテーション方法

各 Azure Web PubSub サービス インスタンスには、プライマリ キーとセカンダリ キーというアクセス キーのペアがあります。 サービスに対する要求が行われたときに、それらを使用して、クライアントが認証されます。 キーは、インスタンス エンドポイントの URL に関連付けられます。 キーは安全に保ち、定期的にローテーションしてください。 アクセス キーは 2 つ提供されるため、1 つのキーを使用して接続を保ったまま、もう 1 つのキーを再生成できます。

## <a name="why-rotate-access-keys"></a>アクセス キーをローテーションする理由

セキュリティ上の理由とコンプライアンス要件により、定期的にアクセス キーをローテーションしてください。

## <a name="regenerate-access-keys"></a>アクセス キーを再生成する

1. [Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。

1. 再生成するキーがある Azure Web PubSub サービス インスタンスで **[キー]** セクションを見つけます。

1. ナビゲーション メニューで **[キー]** を選択します。

1. **[プライマリ キーの再生成]** または **[セカンダリ キーの再生成]** を選択します。

   新しいキーとそれに対応する接続文字列が作成されて表示されます。

Azure Web PubSub サービスが一般公開されたら、Azure CLI を使用して、キーを再生成することもできます。

## <a name="update-configurations-with-new-connection-strings"></a>新しい接続文字列を使用した構成の更新

1. 新しく生成された接続文字列をコピーします。

1. 新しい接続文字列を使用するようにすべての構成を更新します。

1. 必要に応じて、アプリケーションを再起動します。

## <a name="forced-access-key-regeneration"></a>強制的なアクセス キーの再生成

Azure Web PubSub サービスでは、特定の状況下で必須のアクセス キーの再生成が強制的に実行されることがあります。 このサービスでは、電子メールとポータル通知を使用して顧客への通知が行われます。 この通知を受信した場合や、アクセス キーを原因とするサービス エラーが発生した場合は、このガイドの手順に従ってキーをローテーションしてください。