---
title: Azure Network Watcher リソースを移動する |Microsoft Docs
description: Azure Network Watcher リソースをリージョン間で移動する
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 84764e73ec5b4ada8c204147def310326a3c7bdd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948427"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher リソースのリージョン間での移動

## <a name="moving-the-network-watcher-resource"></a>Network Watcher リソースの移動
Network Watcher リソースは、Network Watcher のバックエンドサービスを表しており、Azure によって完全に管理されています。 お客様はその管理を行う必要はありません。 このリソースに対する移動操作はサポートされていません。

## <a name="moving-child-resources-of-network-watcher"></a>Network Watcher の子リソースの移動
現在、リージョン間でのリソースの移動は、`*networkWatcher*` リソース タイプの子リソースではサポートされていません。

## <a name="next-steps"></a>次の手順
* [Network Watcher の概要](./network-watcher-monitoring-overview.md)に関するページを読む
* [Network Watcher のよくある質問](./frequently-asked-questions.md)に関するページを確認する