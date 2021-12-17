---
title: Azure Healthcare APIs に関する FAQ
description: このドキュメントでは、Azure Healthcare APIs についてよく寄せられる質問に対する回答を示します。
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: ginle
ms.openlocfilehash: 6ce51e4cf916baaa22d14e902872185901b4d5da
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988617"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Azure Healthcare APIs についてよく寄せられる質問 (プレビュー)

Azure Healthcare APIs についてよく寄せられる質問のいくつかを次に示します。

## <a name="azure-healthcare-apis-the-basics"></a>Azure Healthcare APIs: 基本

### <a name="what-is-the-azure-healthcare-apis"></a>Azure Healthcare APIs とは何ですか?
Azure Healthcare APIs はフル マネージドの健康データ プラットフォームであり、高速ヘルスケア相互運用性リソース (FHIR®) や Digital Imaging and Communications in Medicine (DICOM®) のような相互運用可能でオープンな業界標準を通じて、保護された医療情報 (PHI) と健康データの迅速な交換と永続化を可能にします。

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Azure Healthcare APIs で何を行うことができますか?
Azure Healthcare APIs を使用すると、以下を行うことができます。 

* 構造化、画像、デバイスのデータなど、さまざまな健康データ ソースと形式をすばやく関連付け、クラウド内で永続化するために正規化します。

* データを変換して FHIR に取り込みます。 たとえば、健康データを、HL7v2 や CDA などのレガシ形式から、またはデバイス独自の形式の高頻度 IoT データから、FHIR に変換することができます。

* Healthcare APIs に格納されているデータを、Azure エコシステム全体のサービス (Synapse など) や Microsoft 全体の製品 (Teams など) に接続し、分析と機械学習を通じて新しい分析情報を導き出し、新しいワークフローと、SMART on FHIR アプリケーションへの接続を可能にします。

* 信頼性、スケーラビリティ、セキュリティを提供するエンタープライズ機能を使用して高度なワークロードを管理し、データが確実に保護され、医療業界で求められるプライバシーとコンプライアンスの認定を満たすようにします。

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>既存の運用ワークロードを FHIR 用の Azure API から Healthcare APIs に移行できますか?
いいえ。残念ながら、現時点では移行機能は用意されていません。 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Azure Healthcare APIs の価格はどれくらいですか?
パブリック プレビュー段階では、Azure Healthcare APIs を無料でご利用いただけます。

### <a name="what-regions-are-healthcare-apis-available"></a>Healthcare APIs はどのリージョンで利用できますか?
最新の情報については、[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir)に関するページを参照してください。 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Azure Healthcare APIs のサブスクリプション クォータ制限を教えてください。

#### <a name="workspace-logical-container"></a>ワークスペース (論理コンテナー):
* サブスクリプションあたり 200 インスタンス (調整不可)

#### <a name="dicom-service"></a>DICOM サービス:
* サブスクリプションあたり 800 インスタンス (調整不可)
* ワークスペースあたり 10 DICOM インスタンス (調整不可)

#### <a name="fhir-service"></a>FHIR サービス:
* サブスクリプションあたり 25 インスタンス (調整不可)
* ワークスペースあたり 10 FHIR インスタンス (調整不可)

#### <a name="iot-connector"></a>IoT コネクタ:
* サブスクリプションあたり25個の IoT コネクタ (調整不可)
* ワークスペースごとに10個の IoT コネクタ (調整不可)
* IoT コネクタごとに1つの FHIR 宛先 * (調整不可)

(*-FHIR ターゲットは IoT コネクタの子リソースです)

## <a name="more-frequently-asked-questions"></a>その他のよく寄せられる質問
[Azure Healthcare APIs の FHIR サービスに関する FAQ](./fhir/fhir-faq.md)

[Azure Healthcare APIs の DICOM サービスに関する FAQ](./dicom/dicom-services-faqs.yml)

[Azure の医療 Api IoT コネクタに関する Faq](./iot/iot-connector-faqs.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
