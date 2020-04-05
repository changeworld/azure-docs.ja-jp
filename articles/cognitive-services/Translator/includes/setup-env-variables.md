---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393838"
---
## <a name="set-up"></a>設定

### <a name="create-a-translator-text-resource"></a>Translator Text リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に Translator Text のリソースを作成します。 次のこともできます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services)を無料で入手する。 これは、サインアップ後に Azure Web サイトで入手できます。
* [Azure portal](https://portal.azure.com/) で既存のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - Translator Text リソースのサブスクリプション キー。
* `TRANSLATOR_TEXT_ENDPOINT` - Translator Text のグローバル エンドポイント。 `https://api.cognitive.microsofttranslator.com/`を使用します。
