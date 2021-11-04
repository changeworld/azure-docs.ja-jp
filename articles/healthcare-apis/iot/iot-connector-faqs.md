---
title: IoT コネクタに関する Faq-Azure の医療 Api
description: このドキュメントでは、IoT コネクタに関してよく寄せられる質問への回答を示します。
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/03/2021
ms.author: jasteppe
ms.openlocfilehash: 00f3153981a07e436b714dadfd2785aac61dd8ab
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578896"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>IoT コネクタについてよく寄せられる質問

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

ここでは、IoT コネクタに関してよく寄せられる質問をいくつか紹介します。

## <a name="iot-connector-the-basics"></a>IoT コネクタ: 基本

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>Azure API for FHIR IoT コネクタ (プレビュー) と Azure 医療 Api IoT コネクタの違いは何ですか?

Azure の医療 Api IoT コネクタは、高速医療相互運用性リソース (FHIR&#174;) IoT コネクタ (プレビュー) のための Azure API の後継です。 

顧客がホストするデバイスメッセージインジェストエンドポイント (例: Azure Event Hub)、管理対象 Id の使用、Azure Role-Based Access Control (Azure RBAC) など、いくつかの機能強化が導入されました。

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Azure の医療 Api FHIR サービス以外の異なる FHIR サービスで IoT コネクタを使用できますか。

いいえ。 現在、Azure の医療 Api IoT コネクタでは、データの永続化のために Azure の医療 Api FHIR サービスのみがサポートされています。 オープンソースバージョンの IoT コネクタは、さまざまな FHIR サービスの使用をサポートしています。 詳細については、「 [オープンソースプロジェクト](iot-git-projects.md) 」セクションを参照してください。  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>IoT コネクタはどのようなバージョンの FHIR をサポートしていますか。

現在、IoT コネクタでは、 [HL7 FHIR&#174; R4](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)の永続性のみがサポートされています。 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>IoT コネクタのサブスクリプションクォータ制限とは何ですか。

* サブスクリプションあたり25個の IoT コネクタ (Azure サポート要求で調整可能)
* ワークスペースごとに10個の IoT コネクタ (Azure サポート要求で調整可能)
* IoT Connector ごとに1つの fhir 変換先 * (調整不可)

(*-FHIR ターゲットは IoT コネクタの子リソースです)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Apple&#174;、Google&#174;、または Fitbit&#174; デバイスからのデバイスメッセージで IoT コネクタを使用できますか。

はい。 IoT コネクタは、これらすべてのプラットフォームからのデバイスメッセージをサポートしています。 詳細については、「 [オープンソースプロジェクト](iot-git-projects.md) 」セクションを参照してください。  

## <a name="more-frequently-asked-questions"></a>その他のよく寄せられる質問
[Azure の医療 Api に関する Faq](../healthcare-apis-faqs.md)

[Azure Healthcare APIs の FHIR サービスに関する FAQ](../fhir/fhir-faq.md)

[Azure Healthcare APIs の DICOM サービスに関する FAQ](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。