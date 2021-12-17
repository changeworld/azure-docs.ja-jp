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
ms.openlocfilehash: 974e3bdf99cb2fe1adb075d03105b68e28632b3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446594"
---
> [!IMPORTANT]
> プライベート エンドポイント、選択されたサブネット、または IP アドレスへのアクセスを制限するコンテナー レジストリでは、一部の機能が使用できないか、または追加の構成が必要になることがあります。 
> * レジストリへのパブリック ネットワーク アクセスが無効になっている場合、Azure Security Center を含む特定の[信頼されたサービス](../articles/container-registry/allow-access-trusted-services.md)によるレジストリ アクセスのためには、ネットワーク設定でのネットワーク ルールのバイパスを有効にする必要があります。
> * 現在、Azure DevOps Services を含む一部の Azure サービスのインスタンスからコンテナー レジストリにアクセスすることはできません。
> * レジストリに承認されたプライベート エンドポイントが含まれ、かつパブリック ネットワーク アクセスが無効になっている場合、Azure portal、Azure CLI、またはその他のツールを使用してリポジトリやタグを仮想ネットワークの外部で一覧表示することはできません。
