---
title: Azure Firewall の インフラストラクチャ FQDN
description: Azure Firewall のインフラストラクチャ FQDN について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61066327"
---
# <a name="infrastructure-fqdns"></a>インフラストラクチャ FQDN

Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 

次のサービスは、組み込みのルール コレクションに含まれています。

- ストレージのプラットフォーム イメージ リポジトリ (PIR) への Compute アクセス
- マネージド ディスク状態ストレージ アクセス
- Azure Diagnostics とログ記録 (MDS)
- Azure Active Directory

## <a name="overriding"></a>オーバーライド 

この組み込みのインフラストラクチャ ルール コレクションをオーバーライドするには、最後に処理される "すべて拒否" アプリケーション ルール コレクションを作成します。 このコレクションは、常にインフラストラクチャ ルール コレクションより先に処理されます。 インフラストラクチャ ルール コレクションに含まれていないものは既定で拒否されます。

## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。