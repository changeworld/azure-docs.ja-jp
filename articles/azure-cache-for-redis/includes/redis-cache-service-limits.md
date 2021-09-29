---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 4fa712e8cf19bf4deb9b536777520a3ffec7261c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645482"
---
| リソース | 制限 |
| --- | --- |
| キャッシュ サイズ |1.2 TB |
| データベース |64 |
| 接続されている最大クライアント数 |40,000 |
| Azure Cache for Redis のレプリカ (高可用性向け) |1 |
| クラスタリングを使用した Premium キャッシュ内のシャード |10 |

Azure Cache for Redis の制限とサイズは価格レベルによって異なります。 価格レベルと関連付けられたサイズについては、「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」を参照してください。

Azure Cache for Redis 構成の制限について詳しくは、「[Default Redis server configuration (既定の Redis サーバー構成)](../cache-configure.md#default-redis-server-configuration)」を参照してください。

Azure Cache for Redis インスタンスの構成と管理は Microsoft によって行われるため、すべての Redis コマンドが Azure Cache for Redis でサポートされるわけではありません。 詳細については、「[Redis commands not supported in Azure Cache for Redis (Azure Cache for Redis でサポートされない Redis コマンド)](../cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)」を参照してください。
