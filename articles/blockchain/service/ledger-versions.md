---
title: Azure Blockchain Service でサポートされる台帳のバージョン、修正プログラム適用、アップグレード
description: システム修正プログラムの適用やシステム管理およびユーザー管理のアップグレードに関するポリシーなど、Azure Blockchain Service でサポートされている台帳のバージョンの概要です。
services: azure-blockchain
keywords: ブロックチェーン
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027615"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>サポートされている Azure Blockchain Service の台帳のバージョン

Azure Blockchain Service では、既知の参加者のグループ (Azure Blockchain Service ではコンソーシアムと呼ばれます) 内のプライベート トランザクションの処理用に設計されている Ethereum ベースの [Quorum](https://github.com/jpmorganchase/quorum/wiki) 台帳が使用されます。

現在、Azure Blockchain Service では [Quorum バージョン 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) と [Tessera トランザクション マネージャー](https://github.com/jpmorganchase/tessera)がサポートされています。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理

Quorum でのバージョン管理は、メジャー リリース、メジャー ポイント リリース、およびマイナー ポイント リリースで行われます。 たとえば、Quorum バージョン 2.0.1 の場合は、2 がメジャー リリース、0 がメジャー ポイント リリース、1 がマイナー ポイント リリースです。 サービスでは、台帳のマイナー ポイント リリースに自動的に修正プログラムが適用されます。 現在、メジャー リリースおよびメジャー ポイント リリースのアップグレードはサポートされていません。

## <a name="availability-of-new-ledger-versions"></a>台帳の新しいバージョンの使用可能性

Azure Blockchain Service では、台帳の最新バージョンは、台帳の製造元で使用可能になってから 60 日以内に提供されます。 新しいメンバーとコンソーシアムをプロビジョニングするとき、コンソーシアムでは最大 4 つのメジャー ポイント リリースから選択することができます。

## <a name="next-steps"></a>次の手順

[Azure Blockchain Service での制限事項](limits.md)
