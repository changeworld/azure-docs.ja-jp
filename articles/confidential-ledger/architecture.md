---
title: Azure Confidential Ledger のアーキテクチャ
description: Azure Confidential Ledger のアーキテクチャ
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 855ad26422eb91c5c971d4525d419e5cee8b6606
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476416"
---
# <a name="architecture"></a>アーキテクチャ

Azure Confidential Ledger (REST API サービス) を使用すると、ユーザーは、管理および機能の API 呼び出しを通じて台帳を操作できます。  データが台帳に記録されると、そのデータは、許可されたブロックチェーン ノードに送信されます。これは、セキュア エンクレーブ支援レプリカです。 このレプリカは、コンセンサスの概念に従います。 ユーザーは、台帳にコミットされたデータのレシートを取得することもできます。

また、将来マルチパーティ コラボレーションをサポートする、オプションのコンソーシアムの概念もあります。

## <a name="architecture-diagram"></a>アーキテクチャの図

この図は、Azure Confidential Ledger のアーキテクチャの概要を示しており、作成された台帳用のクラウド API を操作する Azure Confidential Ledger ユーザーを示しています。

:::image type="content" source="./media/architecture-overview.png" alt-text="アーキテクチャの概要":::

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)
- [Azure Confidential Ledger ノードの認証](authenticate-ledger-nodes.md)
