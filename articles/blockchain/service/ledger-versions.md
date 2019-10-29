---
title: Azure Blockchain Service の台帳のバージョン、修正プログラム適用、アップグレード
description: システム修正プログラムの適用やシステム管理およびユーザー管理のアップグレードに関するポリシーなど、Azure Blockchain Service でサポートされている台帳のバージョンの概要です。
services: azure-blockchain
keywords: ブロックチェーン
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 40719f1c353b8961e14815cb61e7a862b47fc6f5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329135"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>サポートされている Azure Blockchain Service の台帳のバージョン

Azure Blockchain Service では、既知の参加者のグループ (Azure Blockchain Service ではコンソーシアムと呼ばれます) 内のプライベート トランザクションの処理用に設計されている Ethereum ベースの [Quorum](https://www.goquorum.com/developers) 台帳が使用されます。

現在、Azure Blockchain Service は [Quorum バージョン 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) と [Tessera トランザクション マネージャー](https://github.com/jpmorganchase/tessera)をサポートしています。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理

Quorum でのバージョン管理は、メジャー リリース、マイナー リリース、パッチ リリースを通じて行われます。 たとえば、Quorum のバージョンが 2.0.1 の場合、リリースの種類は次のように分類されます。

|メジャー | Minor  | 修正プログラム  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service は、既存の実行中メンバーに対して、Quorum から入手可能になってから 30 日以内に、Quorum のパッチ リリースを自動的に更新します。

## <a name="availability-of-new-ledger-versions"></a>台帳の新しいバージョンの使用可能性

Azure Blockchain Service では、Quorum 台帳の最新のメジャーおよびマイナー バージョンは、Quorum の製造元で使用可能になってから 60 日以内に提供されます。 新しいメンバーとコンソーシアムをプロビジョニングするとき、コンソーシアムでは最大 4 つのマイナー リリースから選択することができます。 メジャーまたはマイナー リリースからの、またはそれらへのアップグレードは現在サポートされていません。 たとえば、バージョン 2.x を実行している場合、バージョン 3.x へのアップグレードは現在サポートされていません。 同様に、バージョン 2.2 を実行している場合、バージョン 2.3 へのアップグレードは現在サポートされていません。

## <a name="next-steps"></a>次の手順

[Azure Blockchain Service での制限事項](limits.md)
