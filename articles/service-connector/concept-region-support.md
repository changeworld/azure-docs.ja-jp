---
title: Service Connector のリージョンのサポート
description: Service Connector のリージョンの可用性とリージョンのサポート一覧
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021, references_regions
ms.openlocfilehash: 75678da43f400eb3ee56f956dcd307207b501e91
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853056"
---
# <a name="service-connector-region-support"></a>Service Connector のリージョンのサポート

Service Connector を使用してサービス接続を作成すると、既定では、概念接続リソースがコンピューティング サービス インスタンスと同じリージョンにプロビジョニングされます。 このページには、リージョンのサポート情報と、Service Connector のパブリック プレビューの対応する動作が示されます。

## <a name="supported-regions-with-regional-endpoint"></a>リージョン エンドポイントでサポートされるリージョン

コンピューティング サービス インスタンスが、Service Connector が以下でサポートするリージョンのいずれかに配置されている場合は、Service Connector を使用してサービス接続を作成および管理できます。

- 米国中西部
- 西ヨーロッパ
- 北ヨーロッパ
- 米国東部
- 米国西部 2

## <a name="supported-regions-with-geographical-endpoint"></a>地理的エンドポイントでサポートされるリージョン

コンピューティング サービス インスタンスは、Service Connector が地理的リージョンをサポートしているリージョンで作成される場合があります。 これは、サービス接続がコンピューティング インスタンスとは別のリージョンに作成されることを意味します。 サービス接続を作成すると、この情報に関するバナーが表示されます。 リージョンの違いは、コンプライアンス、データ所在地、データの待機時間に影響を与える可能性があります。

- 米国東部 2
- 米国西部 3
- 米国中南部

## <a name="not-supported-regions-in-public-preview"></a>パブリック プレビューでサポートされていないリージョン

Service Connector がサポートしているリージョンには、Service Connector CLI コマンドまたはポータル ノードが引き続き表示されます。 ただし、これらのリージョンでサービス接続を作成または管理することはできません。 製品チームは、より多くのリージョンを有効にするために積極的に取り組んでいます。
