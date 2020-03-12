---
title: カスタマー マネージド キーを使用した Azure Device Provisioning Service 保存データの暗号化 |Microsoft Docs
description: Device Provisioning Service でのカスタマー マネージド キーを使用した保存データの暗号化
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674785"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Device Provisioning Service でのカスタマー マネージド キーを使用した保存データの暗号化

## <a name="overview"></a>概要

Device Provisioning Service (DPS) では、Bring Your Own Key (BYOK) とも呼ばれているカスタマー マネージド キー (CMK) を使用した保存データの暗号化がサポートされています。 DPS は、保存データと転送中のデータの暗号化を提供します。 既定では、DPS は Microsoft マネージド キーを使用してデータを暗号化します。 CMK のサポートにより、お客様は [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用して、お客様が管理するキー暗号化キーで保存データを暗号化することを選択できるようになりました。

この機能を使用するには、次のいずれかのリージョンで新しい DPS を作成する必要があります。米国東部、米国西部 2、または米国中南部。 この機能を試用するには、[Microsoft サポート](https://azure.microsoft.com/support/create-ticket/)経由でお問い合わせください。 Microsoft サポートへのお問い合わせの際は、お客様の会社名とサブスクリプション ID をお知らせください。

## <a name="next-steps"></a>次のステップ

* [Device Provisioning Service の詳細を確認する](https://docs.microsoft.com/azure/iot-dps/)

* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)