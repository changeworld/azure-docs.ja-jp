---
title: Azure CLI を使用した Azure Cache for Redis の管理
description: Azure Cache for Redis を管理するための Azure CLI サンプル:キャッシュの作成、キャッシュの削除、キャッシュの詳細、ホスト名、ポート、およびキーの取得、Web アプリの接続。
author: curib
ms.author: cauribeg
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: f6a3b1701a73c4b71ee07b0110761566a4b2e93d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535807"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure CLI を使用した Azure Cache for Redis の管理

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| キャッシュの作成 | 説明 |
| ------------ | ----------- |
| [キャッシュを作成する](./scripts/create-cache.md) | リソース グループと Basic レベルの Azure Cache for Redis を作成します。 |
| [クラスタリングを使用した Premium キャッシュの作成](./scripts/create-premium-cache-cluster.md) | リソース グループとクラスタリングが有効になっている Premium レベルのキャッシュを作成します。|
| [キャッシュの詳細を取得する](./scripts/show-cache.md) | プロビジョニングの状態を含む、Azure Cache for Redis インスタンスの詳細を取得します。 |
| [ホスト名、ポート、およびキーを取得する](./scripts/cache-keys-ports.md) | Azure Cache for Redis インスタンスのホスト名、ポート、およびキーを取得します。 |
|**Web アプリとキャッシュ**| **説明**|
| [Azure Cache for Redis に Web アプリを接続する](./../app-service/scripts/cli-connect-to-redis.md) | Azure Web アプリと Azure Cache for Redis を作成し、Redis の接続の詳細をアプリケーション設定に追加します。 |
|**キャッシュの削除**| **説明** |
| [キャッシュを削除する](./scripts/delete-cache.md) | Azure Cache for Redis インスタンスを削除する  |

Azure CLI の詳細については、[Azure CLI のインストール](/cli/azure/install-azure-cli)と[Azure CLI の概要](/cli/azure/get-started-with-azure-cli)に関するぺージを参照してください。