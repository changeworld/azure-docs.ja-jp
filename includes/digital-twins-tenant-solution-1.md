---
author: baanders
description: Azure Digital Twins を使用してテナント間の制限に対するトークン ソリューションを記述するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 08c48b3600a975f43a5ad94e2cc6efd88047d0d8
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205660"
---
これを実行する方法の 1 つとして、次の CLI コマンドを使用します。ここで、`<home-tenant-ID>` は、Azure Digital Twins インスタンスを含む Azure AD テナントの ID です。

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

これを要求すると、ID は `https://digitaltwins.azure.net` Azure AD リソースに対して発行されたトークンを受け取ります。このトークンには、Azure Digital Twins インスタンスと一致するテナント ID 要求が含まれます。 API 要求または `Azure.Identity` コードでこのトークンを使用すると、フェデレーション ID が Azure Digital Twins リソースにアクセスできるようになります。
