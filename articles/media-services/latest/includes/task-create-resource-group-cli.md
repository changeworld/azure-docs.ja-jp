---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602497"
---
<!-- Create a resource group -->

次のコマンドを使用して、リソース グループを作成します。 Media Services アカウントのメディアとメタデータのレコードを保存する地理的リージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。

```azurecli
az group create --name amsResourceGroup --location westus2
```
