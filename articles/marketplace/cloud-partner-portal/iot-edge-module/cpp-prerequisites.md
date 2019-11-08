---
title: Azure IoT Edge モジュールの前提条件 | Azure Marketplace
description: IoT Edge モジュールを発行するための前提条件です。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813875"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge モジュールの発行の前提条件

この記事では、IoT Edge モジュール オファーを発行するための前提条件について説明します。  まだお読みになっていない場合は、[IoT Edge モジュールの発行ガイド](../..//iot-edge-module.md)をご確認ください。


## <a name="publishing-prerequisites"></a>発行の前提条件

IoT Edge モジュールを Azure Marketplace に発行するには、次の前提条件を満たしている必要があります。

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にアクセスできること。 詳細については、 [Azure Marketplace と AppSource の発行ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)を参照してください。
- [Azure Marketplace の使用条件](https://azure.microsoft.com/support/legal/marketplace-terms/)への同意
- IoT Edge モジュールの技術資産が Azure Container registry でホストされていること。  詳しくは、[IoT Edge モジュールの技術資産を準備する方法](./cpp-create-technical-assets.md)に関する記事をご覧ください
- IoT Edge モジュールのメタデータが使用できる状態になっていること。 たとえば、以下の資産を準備します。
    - タイトル
    - (HTML 形式) での説明
    - ロゴのイメージ (40x40px、90x90px、115x115px 255x115px を含むPNG 形式および固定イメージのサイズ)
    - 使用条件とプライバシー ポリシー
    - 既定のモジュール構成: ルート、ツインの必要なプロパティ、createOptions、環境変数など。
    - モジュールのドキュメント
    - サポートの連絡先


## <a name="next-steps"></a>次の手順

[IoT Edge モジュールの技術資産を準備](./cpp-create-technical-assets.md)したら、[IoT Edge モジュール オファーを作成](./cpp-create-offer.md)する準備が整います。 
