---
title: Fluid コンテナーの管理
description: Azure Fluid Relay サービスでコンテナーを管理する方法の概要。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710377"
---
# <a name="managing-fluid-containers"></a>Fluid コンテナーの管理

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

コンテナーは、Azure Fluid Relay サービスのストレージのアトミック単位であり、操作とスナップショットを含む、Fluid セッションから格納されたデータを表します。 ユーザーが初めて参加したときと、離脱後に再参加したときに、Fluid セッションの状態をリハイドレートするために、Fluid ランタイムにはコンテナーが使用されます。

Fluid Framework を使用してアプリケーションを構築するときは、コンテナーの作成と管理に関して、次の図のようにいくつかの点を考慮する必要があります。

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="Fluid サービスのアーキテクチャと、どの部分を開発者または Microsoft が所有しているかを示す図。":::

## <a name="key-concepts"></a>主要な概念

### <a name="container-permissions"></a>コンテナーのアクセス許可 

ほとんどの場合、開発者にはコンテナーのインベントリとコンテナーのアクセス許可を管理することをお勧めします。 これには、コンテナーへのアクセス権を持っているユーザーに関する情報と、コンテナーのフレンドリ名などのメタデータが含まれます。

### <a name="accessing-containers"></a>コンテナーへのアクセス

コンテナーはコンテナー ID を使用して参照されます。 ユーザーがコンテナーを作成する、または開く前に、Fluid ランタイムが Azure Fluid Relay サービスと通信するときに使用される JWT を要求する必要があります。 有効な JWT を持つ任意のプロセスからコンテナーにアクセスできます。 コンテナー アクセス用の JWT の生成は開発者側の担当です。こうすることで、ビジネス ロジックの管理下に置き、シナリオに応じて適切にアクセスを制御できるようになります。 Azure Fluid Relay サービス側では、コンテナーにアクセスできるユーザーを認識していません。 このトピックの詳細については、「[Azure Fluid Relay トークン コントラクト](../how-tos/fluid-json-web-token.md)」を参照してください。

> [!NOTE]
> JWT フィールド **documentID** は、Fluid コンテナー ID に対応します。

### <a name="container-naming"></a>コンテナーの名前付け

コンテナーは、コンテナーの作成時に Azure Fluid Relay サービスによって名前が付けられます。 作成アクションにより、後でコンテナーを開くために使用する必要がある GUID の形式でコンテナー名が返されます。 ほとんどの場合、コンテナー検出フローを容易にするために、開発者にはこのコンテナー ID GUID をフレンドリ名と共に独自のデータ ストアに格納することをお勧めします。 

### <a name="container-discovery"></a>コンテナーの検出

ユーザーが既存のコンテナーを検出するためのエクスペリエンスとビジネス ロジックは、開発者側の担当です。 これは、Fluid セッションへのユーザーの参加に基づいた閲覧可能なコンテナー一覧、ユーザー間でのコンテナーの直接共有、既存の成果物またはプロセスにプログラムでコンテナーを割り当てるという形式の場合があります。

## <a name="see-also"></a>こちらもご覧ください

- [Azure Fluid Relay アーキテクチャの概要](architecture.md)
- [分散データ構造](data-structures.md)
- [Azure Fluid Relay トークン コントラクト](../how-tos/fluid-json-web-token.md)
