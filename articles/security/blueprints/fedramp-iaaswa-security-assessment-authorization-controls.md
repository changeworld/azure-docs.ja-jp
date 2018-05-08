---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化 - セキュリティ評価と承認
description: FedRAMP Web アプリケーションの自動化 - セキュリティ評価と承認
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 47c5914d-0d76-498a-9298-b3d9040791f8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d1c9846589adaef0baeeaf16077c8e74bef1cd9f
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="security-assessment-and-authorization-ca"></a>セキュリティ評価と承認 (CA)

> [!NOTE]
> この管理策は、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順とガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

## <a name="nist-800-53-control-ca-1"></a>NIST 800-53 Control CA-1

#### <a name="security-assessment-and-authorization-policy-and-procedures"></a>セキュリティ評価と承認に関するポリシーと手順

**CA-1** 組織は、目的、スコープ、役割、責任、経営陣のコミットメント、組織エンティティ間の調整、およびコンプライアンスを取り上げたセキュリティ評価の承認に関するポリシーと、セキュリティ評価と承認に関するポリシーおよびセキュリティ評価と承認の関連するコントロールの実装を容易にする手順を策定、文書化し、[割り当て: 組織が定義した担当者または役割]に周知徹底する。また、現在のセキュリティ評価と承認に関するポリシーを[割り当て: 組織が定義した頻度]、セキュリティ評価と承認の手順を[割り当て: 組織が定義した頻度]見直して、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認に関するポリシーと手順で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-2a"></a>NIST 800-53 Control CA-2.a

#### <a name="security-assessments"></a>セキュリティ評価

**CA-2.a** 組織は、評価対象のセキュリティ コントロール、コントロールの強化などの評価スコープについて記述するセキュリティ評価プラン、セキュリティ コントロールの有効性の見極めに使用される評価手順、評価環境、評価チーム、評価の役割と責任を策定する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたシステムのセキュリティ評価プランを策定する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-2b"></a>NIST 800-53 Control CA-2.b

#### <a name="security-assessments"></a>セキュリティ評価

**CA-2.b** 組織は、情報システムとその操作環境で、[割り当て: 組織が定義した頻度]、セキュリティ コントロールを評価し、コントロールがどの程度正しく実装され、意図したとおりに運用されているか、また、確立されているセキュリティ要件への適合性の観点から、必要な結果がどの程度実現されているかを判断する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースで、CA-02.a で定義されたセキュリティ コントロールを評価する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-2c"></a>NIST 800-53 Control CA-2.c

#### <a name="security-assessments"></a>セキュリティ評価

**CA-2.c** 組織は、評価結果を文書化したセキュリティ評価レポートを作成する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティ評価レポートを作成する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-2d"></a>NIST 800-53 Control CA-2.d

#### <a name="security-assessments"></a>セキュリティ評価

**CA-2.d** 組織は、セキュリティ コントロール評価の結果を[割り当て: 組織が定義した個人または役割]に提供する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティ評価の結果を、必要な個人/役割に提供する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ca-2-1"></a>NIST 800-53 Control CA-2 (1)

#### <a name="security-assessments--independent-assessors"></a>セキュリティ評価 |独立した評価者

**CA-2 (1)** 組織は、[割り当て: 組織が定義したレベルの独立性]を有する評価者または評価チームを採用して、セキュリティ コントロール評価を実施する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、独立した評価者または評価チームを採用して、セキュリティ コントロール評価を実施する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ca-2-2"></a>NIST 800-53 Control CA-2 (2)

#### <a name="security-assessments--specialized-assessments"></a>セキュリティ評価 | 特別な評価

**CA-2 (2)** 組織は、セキュリティ コントロール評価の一部に、[割り当て: 組織が定義した頻度]、[選択: 発表済みの; 未発表の][選択 (1 つまたは複数): 詳細な監視; 脆弱性のスキャン; 悪意のあるユーザー テスト; 内部脅威の評価; パフォーマンス/ロード テスト; [割り当て: 組織が定義した他の形式のセキュリティ評価]]を含める。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティ コントロール評価の一部に含める追加テストを選択する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ca-2-3"></a>NIST 800-53 Control CA-2 (3)

#### <a name="security-assessments--external-organizations"></a>セキュリティ評価 | 外部組織

**CA-2 (3)** 組織は、[割り当て: 組織が定義した外部組織]によって実施された[割り当て: 組織が定義した情報システム]の評価結果を受け入れる (評価が[割り当て: 組織が定義した要件]を満たす場合)。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認の手順によって、FedRAMP の外部組織 (第三者評価機関 (3PAO)、その他の機関など) によって実施されたクラウド サービス (Azure など) の評価結果の受け入れに対処できます。 Azure は、FedRAMP セキュリティ コントロールおよびその他の要件へのコンプライアンスを検証する、FedRAMP 認可の第三者評価機関 (3PAO) によって評価されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-3a"></a>NIST 800-53 Control CA-3.a

#### <a name="system-interconnections"></a>システムの相互接続

**CA-3.a** 組織は、相互接続セキュリティ契約を使用して、情報システム間の接続を承認する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認の手順で、システムの相互接続の承認に対処できます。 注: FedRAMP では、CSP と連邦機関の間の ISA は必要ありません。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-3b"></a>NIST 800-53 Control CA-3.b

#### <a name="system-interconnections"></a>システムの相互接続

**CA-3.b** 組織は、相互接続ごとに、インターフェイスの特性、セキュリティ要件、および通信対象となる情報の性質を文書化する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認の手順で、システムの相互接続を確立するための要件を策定できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-3c"></a>NIST 800-53 Control CA-3.c

#### <a name="system-interconnections"></a>システムの相互接続

**CA-3.c**: 組織は、[割り当て: 組織が定義した頻度]、相互接続セキュリティ契約を見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認の手順で、ISA の見直しおよび更新プロセスを作成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ca-3-3"></a>NIST 800-53 Control CA-3 (3)

#### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>システムの相互接続 | 未分類の非国家セキュリティ システム

**CA-3 (3)** 組織は、[割り当て; 組織が定義した境界保護デバイス]を使用せずに、[割り当て: 組織が定義した未分類の非国家セキュリティ システム]を外部ネットワークに直接接続することを禁止する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ評価と承認の手順で、システムを相互接続するための境界保護要件を策定できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ca-3-5"></a>NIST 800-53 Control CA-3 (5)

#### <a name="system-interconnections--restrictions-on-external-system-connections"></a>システムの相互接続 | 外部システム接続に関する制限事項

**CA-3 (5)** 組織は、[選択: すべて許可、例外的に拒否; すべて拒否、例外的に許可]ポリシーを使用して、[割り当て: 組織が定義した情報システム]による外部情報システムへの接続を許可する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされているリソースへの外部接続を制限するために、Azure Application Gateway とネットワーク セキュリティ グループがデプロイされます。 ネットワーク セキュリティ グループに適用されるルールセットは、既定で拒否スキームを使用して構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-5a"></a>NIST 800-53 Control CA-5.a

#### <a name="plan-of-action-and-milestones"></a>アクションとマイルストーンのプラン

**CA-5.a** 組織は、情報システムのアクションとマイルストーンのプランを策定し、セキュリティ コントロールの評価中に見つかった弱点や欠陥を修正したり、システムの既知の脆弱性を削減または排除したりするために、組織の計画された対応策を文書化する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソース (アプリケーション、オペレーティング システム、データベース、ソフトウェアを含む) のアクションおよびマイルストーン (POA & M) のプランを作成する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-5b"></a>NIST 800-53 Control CA-5.b

#### <a name="plan-of-action-and-milestones"></a>アクションとマイルストーンのプラン

**CA-5.b** 組織は、[割り当て: 組織が定義した頻度]、セキュリティ コントロールの評価、セキュリティへの影響の分析、および継続的な監視アクティビティの結果に基づいて、アクションとマイルストーンの既存のプランを更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、CA-05.a で定義した POA&M 項目を更新する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-6a"></a>NIST 800-53 Control CA-6.a

#### <a name="security-authorization"></a>セキュリティ承認

**CA-6.a** 組織は、上級管理者またはマネージャーを、情報システムの正式な承認担当者として任命する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、正式な承認担当者が任命されている場合に、エンタープライズ レベルのプロセスを利用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-6b"></a>NIST 800-53 Control CA-6.b

#### <a name="security-authorization"></a>セキュリティ承認

**CA-6.b** 組織では、運用を開始する前に、必ず承認担当者が情報システムによる処理を承認する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズ レベルのシステム承認プロセスを利用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-6c"></a>NIST 800-53 Control CA-6.c

#### <a name="security-authorization"></a>セキュリティ承認

**CA-6.c**: 組織は、[割り当て: 組織が定義した頻度]、セキュリティ承認を更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズ レベルのシステム承認プロセスを利用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-7a"></a>NIST 800-53 Control CA-7.a

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.a** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、監視対象となる、[割り当て: 組織が定義したメトリック]の確立が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-7b"></a>NIST 800-53 Control CA-7.b

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.b** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、[割り当て: 組織が定義した頻度]の監視と、[割り当て: 組織が定義した頻度]のこのような監視をサポートする評価の確立が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-7c"></a>NIST 800-53 Control CA-7.c

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.c** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、組織の継続的な監視戦略に従った、進行中のセキュリティ コントロール評価が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-7d"></a>NIST 800-53 Control CA-7.d

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.d** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、組織の継続的な監視戦略に従った、組織が定義したメトリックに対する進行中のセキュリティ状態監視が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-7e"></a>NIST 800-53 Control CA-7.e

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.e** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、評価および監視によって生成されたセキュリティ関連情報の関連付けと分析が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-7f"></a>NIST 800-53 Control CA-7.f

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.f** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、セキュリティ関連情報の分析結果に対処するための応答アクションが含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-7g"></a>NIST 800-53 Control CA-7.g

#### <a name="continuous-monitoring"></a>継続的な監視

**CA-7.g** 組織は継続的な監視戦略を策定し、継続的な監視プログラムを実装する。この監視プログラムには、[割り当て: 組織が定義した頻度]、[割り当て: 組織が定義した担当者または役割]に対する組織および情報システムのセキュリティ状態の報告が含まれる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの監視プログラムで、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ca-7-1"></a>NIST 800-53 Control CA-7 (1)

#### <a name="continuous-monitoring--independent-assessment"></a>継続的な監視 | 独立した評価

**CA-7 (1)** 組織は、[割り当て: 組織が定義した独立レベル]の評価者または評価チームを採用して、情報システムでのセキュリティ コントロールを継続的に監視する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの継続的な監視プログラムで、セキュリティ コントロールの独立した評価プロセスを確立できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ca-7-3"></a>NIST 800-53 Control CA-7 (3)

#### <a name="continuous-monitoring--trend-analyses"></a>継続的な監視 | 傾向分析

**CA-7 (3)** 組織は傾向分析を使用して、セキュリティ コントロールの実装、継続的な監視アクティビティの頻度、継続的な監視プロセスで使用されるアクティビティの種類を、実証データに基づいて変更する必要があるかどうかを判断する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの継続的な監視プログラムで、傾向分析のプロセスを確立できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ca-8"></a>NIST 800-53 Control CA-8

#### <a name="penetration-testing"></a>侵入テスト

**CA-8** 組織は、[割り当て: 組織が定義した頻度]、[割り当て: 組織が定義した情報システムまたはシステム コンポーネント]に対して侵入テストを実施する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズ レベルの侵入テスト評価を利用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ca-8-1"></a>NIST 800-53 Control CA-8 (1)

#### <a name="penetration-testing--independent-penetration-agent-or-team"></a>侵入テスト | 独立した侵入エージェントまたはチーム

**CA-8 (1)** 組織は、独立した侵入エージェントまたは侵入チームを使用して、情報システムまたはシステム コンポーネントに対する侵入テストを実行する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、独立したエージェントまたはチームによって実行される、エンタープライズ レベルの侵入テスト評価を利用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-9a"></a>NIST 800-53 Control CA-9.a

#### <a name="internal-system-connections"></a>内部システム接続

**CA-9.a** 組織は、[割り当て: 組織が定義した情報システム コンポーネントまたはコンポーネントのクラス]から情報システムへの内部接続を承認する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソース (VM へのシステム接続など) 間の内部接続を承認する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ca-9b"></a>NIST 800-53 Control CA-9.b

#### <a name="internal-system-connections"></a>内部システム接続

**CA-9.b** 組織は、内部接続ごとに、インターフェイスの特性、セキュリティ要件、および通信対象となる情報の性質を文書化する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、CA-09.a で定義されているクラス/リソース間の各内部接続の詳細を文書化する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |
