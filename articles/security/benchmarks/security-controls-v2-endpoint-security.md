---
title: Azure セキュリティ ベンチマーク V2 - エンドポイント セキュリティ
description: Azure セキュリティ ベンチマーク V2 のエンドポイント セキュリティ
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 452b1a33520309ae696ab318c034f0186c993fdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91402924"
---
# <a name="security-control-v2-endpoint-security"></a>セキュリティ コントロール V2:エンドポイント セキュリティ

エンドポイント セキュリティでは、エンドポイントでの検出と対応の制御を行います。 これには、Azure 環境内のエンドポイントに対して、エンドポイントでの検出と対応 (EDR) およびマルウェア対策サービスを使用することが含まれます。

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1:エンドポイントでの検出と対応 (EDR) を使用する

| Azure ID | CIS Controls v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| ES-1 | 8.1 | SI-2、SI-3、SC-3 |

サーバーとクライアントに対してエンドポイントでの検出と対応 (EDR) 機能を有効にし、SIEM およびセキュリティ運用プロセスと統合します。

Microsoft Defender Advanced Threat Protection では、高度な脅威を防御、検出、調査し、それに対応できるように、エンタープライズ エンドポイント セキュリティ プラットフォームの一部として EDR 機能を提供しています。 

- [Microsoft Defender Advanced Threat Protection の概要](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows サーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Windows 以外のサーバー向けの Microsoft Defender ATP サービス](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2:一元管理された最新のマルウェア対策ソフトウェアを使用する

| Azure ID | CIS Controls v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| ES-2 | 8.1 | SI-2、SI-3、SC-3 |

リアルタイムおよび定期的なスキャンを行える、一元管理されたエンドポイントのマルウェア対策ソリューションを使用します

Azure Security Center では、お使いの仮想マシンにいくつもの一般的なマルウェア対策ソリューションが使用されていることを自動的に特定し、エンドポイント保護の実行状態を報告したり、推奨事項を提供したりできます。 

Azure Cloud Services 向けの Microsoft Antimalware は、Windows 仮想マシン (VM) の既定のマルウェア対策です。 Linux VM の場合は、サードパーティのマルウェア対策ソリューションを使用します。  さらに、データ サービス向けの Azure Security Center の脅威検出を使用して、Azure Storage アカウントにアップロードされたマルウェアを検出することもできます。 

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../fundamentals/antimalware.md)

- [サポートされているエンドポイント保護ソリューション](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

| Azure ID | CIS Controls v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| ES-3 | 8.2 | SI-2、SI-3 |

マルウェア対策の署名が迅速かつ一貫して更新されるようにします。 

Azure Security Center の"計算とアプリ" の推奨事項に従って、すべてのエンドポイントが最新の署名を備え、最新の状態になっているようにします。 Microsoft Antimalware では、既定で、最新の署名とエンジンの更新プログラムが自動的にインストールされます。 Linux の場合は、サードパーティのマルウェア対策ソリューションを使用します。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware をデプロイする方法](../fundamentals/antimalware.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Azure Security Center での Endpoint Protection の評価と推奨事項](../../security-center/security-center-endpoint-protection.md)
