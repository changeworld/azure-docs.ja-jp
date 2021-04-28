---
author: baanders
description: Azure Digital Twins を使用してテナント間の制限に対するトークン ソリューションを記述するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589357"
---
これを実行する方法の 1 つとして、次の CLI コマンドを使用します。ここで、`<home-tenant-ID>` は、Azure Digital Twins インスタンスを含む Azure AD テナントの ID です。

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

これを要求すると、ID は *https://digitaltwins.azure.net* Azure AD リソースに対して発行されたトークンを受け取ります。このトークンには、Azure Digital Twins インスタンスと一致するテナント ID 要求が含まれます。 API 要求または `Azure.Identity` コードでこのトークンを使用すると、フェデレーション ID が Azure Digital Twins リソースにアクセスできるようになります。