---
title: カスタマー マネージド キーを使用した Azure IoT Hub 保存データの暗号化| Microsoft Docs
description: カスタマー マネージド キーを使用した Azure IoT Hub 保存データの暗号化
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: asrastog
ms.openlocfilehash: 4dd3ee01504c2777ad72e32e11932b3b63d07448
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515534"
---
# <a name="encryption-of-azure-iot-hub-data-at-rest-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した Azure IoT Hub 保存データの暗号化

IoT Hub では、Bring Your Own Key (BYOK) とも呼ばれているカスタマー マネージド キー (CMK) を使用した保存データの暗号化がサポートされています。 Azure IoT Hub では、保存データや転送中のデータがデータセンターに書き込まれるときに、それらのデータを暗号化することができます。データは書き込まれるときに暗号化され、読み取られるときに暗号化解除されます。 

既定では、IoT Hub は Microsoft マネージド キーを使用してデータを暗号化します。 CMK を使用することで、既定の暗号化に加えて別の暗号化レイヤーを設けることができます。また、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で管理されるキー暗号化キーを使用して保存データを暗号化することもできます。 これにより、アクセスの制御を柔軟に作成、ローテーション、無効化、取り消しできます。 BYOK が IoT Hub 用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化も利用できます。

この機能を利用するには、新しい IoT Hub (Basic または Standard レベル) を作成する必要があります。 この機能を試用するには、[Microsoft サポート](https://azure.microsoft.com/support/create-ticket/)経由でお問い合わせください。 Microsoft サポートへのお問い合わせの際は、お客様の会社名とサブスクリプション ID をお知らせください。

## <a name="next-steps"></a>次のステップ

* [IoT Hub とは](./about-iot-hub.md)

* [Azure Key Vault の詳細を確認する](../key-vault/general/overview.md)
