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
ms.openlocfilehash: 83a42f7a68dc34770106e258b4ed5949feb48c8d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471493"
---
> [!IMPORTANT]
> プライベート エンドポイント、選択されたサブネット、または IP アドレスへのアクセスを制限するコンテナー レジストリでは、一部の機能が使用できないか、または追加の構成が必要になることがあります。 
> * レジストリへのパブリック ネットワーク アクセスが無効になっている場合、Azure Security Center を含む特定の[信頼されたサービス](../articles/container-registry/allow-access-trusted-services.md)によるレジストリ アクセスのためには、ネットワーク設定でのネットワーク ルールのバイパスを有効にする必要があります。
> * Azure DevOps Services や Azure Container Instances を含む特定の Azure サービスのインスタンスは現在、コンテナー レジストリにアクセスできません。
> * レジストリに承認されたプライベート エンドポイントが含まれ、かつパブリック ネットワーク アクセスが無効になっている場合、Azure portal、Azure CLI、またはその他のツールを使用してリポジトリやタグを仮想ネットワークの外部で一覧表示することはできません。
