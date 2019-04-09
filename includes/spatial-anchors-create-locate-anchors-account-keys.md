---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: afa3fea0c172134afb06abc63c90805a32c89639
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907691"
---
## <a name="set-up-authentication"></a>認証の設定

サービスにアクセスするには、アカウント キー、アクセス トークン、または AAD 認証トークンを提供する必要があります。

### <a name="account-keys"></a>アカウント キー

アカウント キーは、アプリケーションが Azure Spatial Anchors サービスで認証できるようにするための資格情報です。 アカウント キーの使用目的は、すぐに開始できるようにサポートすることです。 特にアプリケーションの Azure Spatial Anchors との統合の開発フェーズ時などがこれに該当します。 そのようなものとして、開発中にクライアント アプリケーションにアカウント キーを埋め込んで使用できます。 開発の先のフェーズに進むときには、実稼働レベルであるか、アクセス トークンによりサポートされるか、または AAD ベースのユーザー認証である認証メカニズムに移行することを強く推奨します。 開発のためにアカウント キーを取得するには、Azure Spatial Anchors アカウントにアクセスし、[キー] タブに移動します。
