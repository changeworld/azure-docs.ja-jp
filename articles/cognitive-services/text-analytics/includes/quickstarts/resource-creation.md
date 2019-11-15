---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750126"
---
アプリケーションを認証するためのキーとエンドポイントを取得します。 [Azure portal](../../../cognitive-services-apis-create-account.md) または [Azure CLI](../../../cognitive-services-apis-create-account-cli.md) を使用して、ローカル コンピューター上に Text Analytics のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* お使いのリソースを [Azure portal](https://portal.azure.com/) で表示する

試用版のサブスクリプションまたはリソースからキーとエンドポイントを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。 キー用の `TEXT_ANALYTICS_SUBSCRIPTION_KEY` という名前のものと、エンドポイント用の `TEXT_ANALYTICS_ENDPOINT` という名前のものです。