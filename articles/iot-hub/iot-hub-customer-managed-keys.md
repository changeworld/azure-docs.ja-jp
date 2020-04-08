---
title: カスタマー マネージド キーを使用した Azure IoT Hub 保存データの暗号化 |Microsoft Docs
description: IoT Hub でのカスタマー マネージド キーを使用した保存データの暗号化
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370578"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>IoT Hub でのカスタマー マネージド キーを使用した保存データの暗号化

IoT Hub では、Azure IoT Hub 向けのサポートである Bring Your Own Key (BYOK) とも呼ばれているカスタマー マネージド キー (CMK) を使用した保存データの暗号化がサポートされています。 Azure IoT Hub は、保存データと転送中のデータの暗号化を提供します。 既定では、IoT Hub は Microsoft マネージド キーを使用してデータを暗号化します。 CMK のサポートにより、お客様は [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用して、お客様が管理するキー暗号化キーで保存データを暗号化することを選択できるようになりました。

この機能を利用するには、次のいずれかのリージョンで新しい IoT Hub (Basic または Standard レベル) を作成する必要があります:米国東部、米国西部 2、米国中南部、US Gov。 この機能を試用するには、[Microsoft サポート](https://azure.microsoft.com/support/create-ticket/)経由でお問い合わせください。 Microsoft サポートへのお問い合わせの際は、お客様の会社名とサブスクリプション ID をお知らせください。

## <a name="next-steps"></a>次のステップ

* [IoT Hub の詳細を確認する](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
