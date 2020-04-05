---
title: Azure セキュリティ コントロール - インシデント対応
description: セキュリティ コントロール、インシデント対応
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934414"
---
# <a name="security-control-incident-response"></a>セキュリティ コントロール:インシデント対応

攻撃を迅速に検出し、被害を効果的に抑制し、攻撃者の存在を一掃し、ネットワークとシステムの整合性を回復するためのインシデント対応インフラストラクチャ (計画、定義済みのロール、トレーニング、通信、管理監督など) を開発して実装することにより、組織の情報とその評判を保護します。

## <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.1 | 19.1、19.2、19.3 | Customer |

組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center のインシデントの構造:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

お客様は、独自のインシデント対応計画の作成のために、NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます。

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.2 | 19.8 | Customer |

Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

## <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.3 | 19 | Customer |

定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.4 | 19.5 | Customer |

セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

Azure Security Center のセキュリティ連絡先を設定する方法:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.5 | 19.6 | Customer |

連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

連続エクスポートを構成する方法:

https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 10.6 | 19 | Customer |

Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して &quot;Logic Apps&quot; 経由で自動的に応答をトリガーできます。

ワークフローの自動化と Logic Apps を構成する方法:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください。[侵入テストとレッド チーム演習](security-control-penetration-tests-red-team-exercises.md)