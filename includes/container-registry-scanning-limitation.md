---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/19/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 535641244dbdaa4ffa3f5e2fe5c3663d7f009b74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353961"
---
> [!IMPORTANT]
> プライベート エンドポイント、選択されたサブネット、または IP アドレスへのアクセスを制限するコンテナー レジストリでは、一部の機能が使用できないか、または追加の構成が必要になることがあります。
>
> * レジストリへのパブリック ネットワーク アクセスが無効になっている場合、Azure Security Center を含む特定の[信頼されたサービス](../articles/container-registry/allow-access-trusted-services.md)によるレジストリ アクセスのためには、ネットワーク設定でのネットワーク ルールのバイパスを有効にする必要があります。
> * 現在、Azure DevOps Services を含む一部の Azure サービスのインスタンスからコンテナー レジストリにアクセスすることはできません。
> * レジストリに承認されたプライベート エンドポイントが含まれ、かつパブリック ネットワーク アクセスが無効になっている場合、Azure portal、Azure CLI、またはその他のツールを使用してリポジトリやタグを仮想ネットワークの外部で一覧表示することはできません。
