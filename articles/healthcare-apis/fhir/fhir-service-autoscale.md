---
title: Azure Healthcare API FHIR サービスの自動スケーリング機能
description: この記事では、Azure Healthcare API FHIR サービスの自動スケーリング機能について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: zxue
ms.openlocfilehash: 382a3ccee6f14c4d95e74bb40a8d899868754926
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814760"
---
# <a name="fhir-service-autoscale"></a>FHIR サービスの自動スケーリング

Azure Healthcare API の FHIR サービスは、お客様が FHIR に準拠した医療データを保持し、API サービス エンドポイントを介して安全に操作することを可能にするマネージド サービスです。 FHIR サービスは、さまざまなワークロードを満たす組み込みの自動スケーリング機能を提供します。  

## <a name="what-is-fhir-service-autoscale"></a>FHIR サービスの自動スケーリングとは   

FHIR サービスの自動スケーリング機能は、一貫性のある、またはさまざまなワークロードでいつでもデータ トランザクションを実行するときに、顧客の要求を満たすために最適化されたサービスのスケーラビリティを自動的に提供するように設計されています。 FHIR サービスがサポートされているすべてのリージョンで使用できます。 自動スケーリング機能は、Azure リージョンで使用可能なリソースの対象となるので、ご安心ください。   

## <a name="how-does-fhir-service-autoscale-work"></a>FHIR サービスの自動スケーリングの動作  

トランザクション ワークロードが高い場合、自動スケーリング機能によってコンピューティング リソースが自動的に増加します。 トランザクション ワークロードが低い場合は、コンピューティング リソースが減少します。  

自動スケーリング機能により、コンピューティング リソースが自動的に調整され、サービス全体のスケーラビリティが最適化されます。 患者 ID を使用して患者情報を取得するなどの単純なクエリや、名前が "Sarah" の患者のすべてのリソースを取得するなどの高度なクエリを含む読み取り要求を実行する場合でも、FHIR リソースを作成または更新する場合でも、自動スケーリング機能によってリソース割り当ての動的性と複雑性が管理され、高いスケーラビリティが確保されます。 `DiagnosticReport`

自動スケール機能は、マネージド サービスの一部であり、顧客からのアクションは必要ない。 ただし、お客様は、機能の改善に役立つフィードバックを共有してください。 また、お客様は、発生した可能性のあるスケーラビリティの問題に対処するためにサポート チケットを発行できます。  

### <a name="what-is-the-cost-of-the-fhir-service-autoscale"></a>FHIR サービスの自動スケーリングのコストは何ですか?  

自動スケーリング機能では、新しい API 課金モデルに基づいて、顧客に追加のコストは発生し"ない。

## <a name="next-steps"></a>次の手順

この記事では、Azure Healthcare API の FHIR サービス自動スケーリング機能について学習しました。FHIR サービスでサポートされる機能の詳細については、以下を参照してください。

>[!div class="nextstepaction"]
>[サポートされる FHIR 機能](fhir-features-supported.md)
