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
ms.openlocfilehash: b1013b261e9449aef45f629c729579f4c87c1f6b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378454"
---
## <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure リソースを作成する

[Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル マシン上に Personalizer のリソースを作成します。 

リソースからキーを取得した後、[環境変数](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)を 2 つ作成します。

* `PERSONALIZER_RESOURCE_KEY` (リソース キー)。
* `PERSONALIZER_RESOURCE_ENDPOINT` (リソース エンドポイント)。

Azure portal では、キーとエンドポイントのどちらの値も **[クイック スタート]** ページで取得できます。
