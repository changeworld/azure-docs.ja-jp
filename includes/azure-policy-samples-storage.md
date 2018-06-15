---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664629"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [ストレージ アカウントおよび仮想マシンに対して許可された SKU](../articles/azure-policy/scripts/allowed-skus-storage.md) | 承認された SKU がストレージ アカウントと仮想マシンで使用される必要があります。 組み込みのポリシーを使用して、承認された SKU を確認します。 承認された仮想マシンの SKU の配列と、承認されたストレージ アカウントの SKU の配列を指定します。 |
| [許可されるストレージ アカウントの SKU](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | ストレージ アカウントが適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [ストレージ アカウントのクール アクセス層の拒否](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | BLOB ストレージ アカウントでのクール アクセス層の使用を禁止します。  |
| [ストレージ アカウントのみに対する https トラフィックの確認](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | HTTPS トラフィックを使用するストレージ アカウントが必要です。  |
| [ストレージ ファイルの暗号化の確認](../articles/azure-policy/scripts/ensure-store-file-enc.md) | ファイルの暗号化がストレージ アカウントに対して有効になっている必要があります。  |
| [ストレージ アカウントの暗号化が必要](../articles/azure-policy/scripts/req-store-acct-enc.md) | ストレージ アカウントで BLOB 暗号化を使用する必要があります。  |