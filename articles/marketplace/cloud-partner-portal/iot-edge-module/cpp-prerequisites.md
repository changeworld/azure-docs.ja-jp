---
title: Azure IoT Edge モジュールの前提条件 | Azure Marketplace
description: IoT Edge モジュールを発行するための前提条件です。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142365"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge モジュールの発行の前提条件

>[!Important]
>2020 年 4 月 13 日より、IoT Edge モジュール オファーの管理はパートナー センターに移行されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 移行後のオファーは、「[IoT Edge モジュール オファーの作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)」の手順に従って管理します。

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


## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールの技術資産を準備](./cpp-create-technical-assets.md)したら、[IoT Edge モジュール オファーを作成](./cpp-create-offer.md)する準備が整います。 
