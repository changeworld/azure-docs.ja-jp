---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131634"
---
| メカニズム | Scope |制限 | サポートされているアクセス許可のレベル |
|---|---|---|---|
| RBAC | ストレージ アカウント、コンテナー。 <br>サブスクリプション レベルまたはリソース グループ レベルでのクロス リソース RBAC ロールの割り当て。 | サブスクリプションで 2,000 個の RBAC ロールの割り当て | RBAC ロール (組み込みまたはカスタム) |
| ACL| ディレクトリ、ファイル |ファイルごと、およびディレクトリごとに 32 個の ACL エントリ (実質的に 28 個の ACL エントリ)。 アクセス ACL と既定の ACL それぞれに、独自の 32 個の ACL エントリの制限があります。 |ACL アクセス許可|
