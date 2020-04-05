---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694416"
---
## <a name="set-up-authentication"></a>認証の設定

サービスにアクセスするには、アカウント キー、アクセス トークン、または Azure Active Directory 認証トークンを提供する必要があります。 この詳細については、[認証の概念に関するページ](/azure/spatial-anchors/concepts/authentication)も参照してください。

### <a name="account-keys"></a>アカウント キー

アカウント キーは、アプリケーションが Azure Spatial Anchors サービスで認証できるようにするための資格情報です。 アカウント キーの使用目的は、すぐに開始できるようにサポートすることです。 特にアプリケーションの Azure Spatial Anchors との統合の開発フェーズ時などがこれに該当します。 そのようなものとして、開発中にクライアント アプリケーションにアカウント キーを埋め込んで使用できます。 開発の先のフェーズに進むときには、実稼働レベルであるか、アクセス トークンによりサポートされるか、または Azure Active Directory ベースのユーザー認証である認証メカニズムに移行することを強く推奨します。 開発のためにアカウント キーを取得するには、Azure Spatial Anchors アカウントにアクセスし、[キー] タブに移動します。
