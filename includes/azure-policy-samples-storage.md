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
ms.openlocfilehash: b967558828c42331b3702fe90ce842fd1c0032bd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003823"
---
## <a name="storage"></a>Storage

|  |  |
|---------|---------|
| [ストレージ アカウントおよび仮想マシンに対して許可された SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 承認された SKU がストレージ アカウントと仮想マシンで使用される必要があります。 組み込みのポリシーを使用して、承認された SKU を確認します。 承認された仮想マシンの SKU の配列と、承認されたストレージ アカウントの SKU の配列を指定します。 |
| [許可されるストレージ アカウントの SKU](../articles/governance/policy/samples/allowed-stor-acct-skus.md) | ストレージ アカウントが適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [ストレージ アカウントのクール アクセス層の拒否](../articles/governance/policy/samples/deny-cool-access-tiering.md) | BLOB ストレージ アカウントでのクール アクセス層の使用を禁止します。  |
| [ストレージ アカウントのみに対する https トラフィックの確認](../articles/governance/policy/samples/ensure-https-stor-acct.md) | HTTPS トラフィックを使用するストレージ アカウントが必要です。  |
| [ストレージ ファイルの暗号化の確認](../articles/governance/policy/samples/ensure-store-file-enc.md) | ファイルの暗号化がストレージ アカウントに対して有効になっている必要があります。  |
| [ストレージ アカウントの暗号化が必要](../articles/governance/policy/samples/req-store-acct-enc.md) | ストレージ アカウントで BLOB 暗号化を使用する必要があります。  |