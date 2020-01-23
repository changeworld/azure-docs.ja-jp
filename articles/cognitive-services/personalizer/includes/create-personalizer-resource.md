---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122754"
---
## <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure リソースを作成する

[Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル マシン上に Personalizer のリソースを作成します。 次のこともできます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。

* `PERSONALIZER_RESOURCE_KEY` (リソース キー)。
* `PERSONALIZER_RESOURCE_ENDPOINT` (リソース エンドポイント)。

Azure portal では、キーとエンドポイントのどちらの値も **[クイック スタート]** ページで取得できます。
