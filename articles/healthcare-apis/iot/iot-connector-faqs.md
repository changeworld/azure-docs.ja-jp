---
title: IoT コネクタに関する FAQ - Azure Healthcare API
description: このドキュメントでは、IoT コネクタに関してよく寄せられる質問への回答を示します。
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 153a4c86d0f6f1b2617327360569cd92d1a318ca
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990246"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>IoT コネクタに関してよく寄せられる質問

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

IoT コネクタについてよく寄せられる質問を次に示します。

## <a name="iot-connector-the-basics"></a>IoT コネクタ: 基本

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>Azure API for FHIR IoT コネクタ (プレビュー) と Azure Healthcare API IoT コネクタの違いは何ですか?

Azure Healthcare API IoT コネクタは、Azure API for 高速ヘルスケア相互運用性リソース (FHIR&#174;) IoT コネクタ (プレビュー) の後継です。 

顧客がホストするデバイス メッセージ インジェスト エンドポイント (Azure Event Hub など)、マネージド ID の使用、Azure Role-Based Access Control (Azure RBAC) など、いくつかの機能強化が導入されました。

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Azure Healthcare API FHIR サービス以外の別の FHIR サービスで IoT コネクタを使用できますか?

いいえ。 現在、Azure Healthcare API IoT コネクタは、データの永続化のために Azure Healthcare API FHIR サービスのみをサポートしています。 オープン ソース バージョンの IoT コネクタでは、さまざまな FHIR サービスの使用がサポートされています。 詳細については、「オープンソース プロジェクト [」セクションを参照](iot-git-projects.md) してください。  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>IoT コネクタでサポートされている FHIR のバージョンは何ですか?

現在、IoT コネクタでは [、R4 の HL7 FHIR&#174;サポートされています](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)。 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>IoT コネクタのサブスクリプション クォータ制限は何ですか?

* サブスクリプションあたり 25 IoT コネクタ (調整不可)
* ワークスペースあたり 10 IoT コネクタ (調整不可)
* IoT コネクタごとに 1 つの FHIR 変換先* (調整不可)

(* - FHIR Destination は IoT コネクタの子リソースです)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Apple&#174;、Google&#174;、または Fitbit&#174; デバイスからのデバイス メッセージで IoT コネクタを使用できますか?

はい。 IoT コネクタは、これらすべてのプラットフォームからのデバイス メッセージをサポートします。 詳細については、「オープンソース プロジェクト [」セクションを参照](iot-git-projects.md) してください。  

## <a name="more-frequently-asked-questions"></a>その他のよく寄せられる質問
[Azure Healthcare API に関する FAQ](../healthcare-apis-faqs.md)

[Azure Healthcare APIs の FHIR サービスに関する FAQ](../fhir/fhir-faq.md)

[Azure Healthcare APIs の DICOM サービスに関する FAQ](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
