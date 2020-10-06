---
title: Azure セキュリティ ベンチマーク V2 - ガバナンスと戦略
description: Azure セキュリティ ベンチマーク V2 ガバナンスと戦略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059056"
---
# <a name="security-control-governance-and-strategy"></a>セキュリティ コントロールガバナンスと戦略

バックアップと回復により、データと構成のバックアップがさまざまなサービス レベルで確実に実行、検証、保護されるようにコントロールがカバーされています。

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: 資産の管理と保護の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-1 | 2、13 | SC、AC |

システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス リスクに応じたデータ分類標準

-   リスクと資産のインベントリに対するセキュリティ組織の可視性 

-   Azure サービスを使用するためのセキュリティ組織の承認 

-   ライフサイクルを通じた資産のセキュリティ

-   組織のデータ分類に従った必要なアクセス制御戦略

-   Azure ネイティブおよびサードパーティのデータ保護機能の使用

-   転送中および保存中のユース ケースのデータ暗号化要件

-   適切な暗号化標準

注:クラウドとオンプレミスに対する資産の管理と保護のアプローチは、アプリケーション サービスやホスティング モデル、ビジネス リスク、コンプライアンス要件など、複数の要因によって異なる場合があります。 

- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure セキュリティ ベンチマーク - データ保護](/azure/security/benchmarks/security-controls-v2-data-protection)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: セキュリティ態勢管理の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-2 | 20、3、5 | RA、CM、SC |

個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: 組織の役割、責任、責務を整合させる

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-3 | N/A | PL、PM |

セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティの決定に関する明確なアカウンタビリティ、共有責任モデルに関する教育、クラウド セキュリティに関する技術教育を優先的に提供します。 

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](https://aka.ms/AzSec1)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](https://aka.ms/AzSec2)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](https://aka.ms/AzSec3)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: ネットワーク セキュリティ戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-4 | 9 | CA、SC |

組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

注:クラウドとオンプレミスに対するネットワーク セキュリティ アプローチは、アプリケーション サービス モデル、脅威への露出、ハイブリッド ネットワークの設定など、複数の要因によって異なる場合があります。

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](https://aka.ms/AzSec11)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure のネットワーク セキュリティの概要](../fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: ID と特権アクセス戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-5 | 16、4 | AC、AU、SC |

組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

注:クラウドとオンプレミスに関する ID と特権アクセスのアプローチは、データとアプリケーションのアクセス パス、サービス モデル、顧客とパートナーのアクセス戦略など、複数の要因によって異なる場合があります。

- [Azure セキュリティ ベンチマーク - ID 管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure セキュリティ ベンチマーク - 特権アクセス](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](https://aka.ms/AzSec11)

- [Azure ID 管理のセキュリティの概要](../fundamentals/identity-management-overview.md) 

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: ログ記録と脅威対応戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-6 | 19 | IR、AU、RA、SC |

コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 統合と手動手順ではなく脅威に集中できるように、高品質のアラートとシームレスなエクスペリエンスをアナリストに提供することを優先します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

注:クラウドとオンプレミスに関するログ記録と脅威検出のアプローチは、コンプライアンス要件、脅威の状況、検出と修復の機能など、複数の要因によって異なる場合があります。 

- [Azure セキュリティ ベンチマーク - ログと脅威検出](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure セキュリティ ベンチマーク - インシデント対応](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](https://aka.ms/AzSec11)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: バックアップと復旧の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| GS-7 | 10 | CP |

組織の Azure のバックアップと復旧の戦略を確立します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス回復性の目標に従った目標復旧時間 (RTO) と目標復旧時点 (RPO) の定義

-   アプリケーションとインフラストラクチャのセットアップにおける冗長性の設計

-   アクセスの制御とデータ暗号化を使用したバックアップの保護

注:クラウドとオンプレミスのバックアップと復旧のアプローチは、インフラストラクチャの冗長性、アプリケーションのサービスとホスティング モデル、コンプライアンス要件など、複数の要因によって異なる場合があります。

- [Azure セキュリティ ベンチマーク - バックアップと復旧](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Azure の優れたアーキテクチャ フレームワーク - Azure アプリケーションのバックアップとディザスター リカバリー](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 導入フレームワーク - ビジネス継続性とディザスター リカバリー](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**責任**: Customer

**顧客のセキュリティ上の利害関係者**:

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

