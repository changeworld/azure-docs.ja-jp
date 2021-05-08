---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017682"
---
| メカニズム | Scope |制限 | サポートされているアクセス許可のレベル |
|---|---|---|---|
| Azure RBAC | ストレージ アカウント、コンテナー。 <br>サブスクリプション レベルまたはリソース グループ レベルでのクロス リソース Azure ロールの割り当て。 | サブスクリプションで 2,000 個の Azure ロールの割り当て | Azure ロール (組み込みまたはカスタム) |
| ACL| ディレクトリ、ファイル |ファイルごと、およびディレクトリごとに 32 個の ACL エントリ (実質的に 28 個の ACL エントリ)。 アクセス ACL と既定の ACL それぞれに、独自の 32 個の ACL エントリの制限があります。 |ACL アクセス許可|
