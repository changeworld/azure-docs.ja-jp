---
title: "FedRAMP Azure Blueprint Automation - システムと通信の保護"
description: "FedRAMP 用の Web アプリケーション - システムと通信の保護"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 02e2d07eb29d0d5d436afed1cdab4fe710674a8c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-communications-protection-sc"></a>システムと通信の保護 (SC)

> [!NOTE]
> このコントロールは、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順およびガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 Control SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>システムと通信の保護のポリシーと手順

**SC-1** 組織は、目的、スコープ、役割、責任、経営陣のコミットメント、組織エンティティ間の調整、およびコンプライアンスを取り上げたシステムと通信の保護ポリシーと、システムと通信の保護ポリシーおよびシステムと通信の保護の関連するコントロールの実装を容易にする手順を策定、文書化し、[割り当て: 組織が定義した担当者または役割]に周知徹底する。また、現在のシステムと通信の保護ポリシーを[割り当て: 組織が定義した頻度]、システムと通信の保護の手順を[割り当て: 組織が定義した頻度]見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのシステムと通信の保護のポリシーと手順で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 Control SC-2

#### <a name="application-partitioning"></a>アプリケーションのパーティション分割

**SC-2** 情報システムでは、ユーザー機能 (ユーザー インターフェイス サービスを含む) を情報システム管理機能から分離する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、論理アクセス制御とシステム アーキテクチャを強制することによって、ユーザー機能をシステム管理機能から分離します。 ユーザー機能は、お客様がデプロイした Web アプリケーション インターフェイスに限定されます。 システム管理機能のインターフェイスは、ユーザー インターフェイスとは別のものです。 すべての管理接続は、必要に応じて運用リソースへのアクセスを制限するネットワーク セキュリティ グループの規則が適用された管理サブネットにある、セキュリティで保護された要塞ホスト (ジャンプボックス) を介して行われます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 Control SC-3

#### <a name="security-function-isolation"></a>セキュリティ機能の分離

**SC-3** 情報システムでは、セキュリティ機能を非セキュリティ機能から分離する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンでは、Windows オペレーティング システムを実行します。 Windows は、プライベート仮想アドレス空間を各プロセスに割り当てることによって、実行中のプロセスごとに個別の実行ドメインを保持します。 さらに、このソリューションは、必要に応じてセキュリティ機能を分離するように設計されたアーキテクチャとアクセス制御を実装します。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 Control SC-4

#### <a name="information-in-shared-resources"></a>共有リソースの情報

**SC-4** 情報システムでは、共有システム リソースを介した、情報の承認されていない転送または意図しない転送を防ぐ。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンでは、Windows オペレーティング システムを実行します。 オペレーティング システムはリソース (メモリ、ストレージなど) を管理して、適切なアクセス許可を持つユーザーとロールだけが情報にアクセスできるようにします。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 Control SC-5

#### <a name="denial-of-service-protection"></a>サービス拒否の防止

**SC-5** 情報システムでは、[割り当て: 組織が定義したセキュリティ対策]を使用して、[割り当て: 組織が定義したサービス拒否攻撃の種類または当該情報のソースへの参照]から保護するか、影響を抑制する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、Web アプリケーション ファイアウォールと負荷分散機能を備えた Application Gateway をデプロイします。 Web 層、データベース層、および Active Directory をサポートするデプロイ対象の仮想マシンは、スケーラブルな可用性セットにデプロイされます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 Control SC-6

#### <a name="resource-availability"></a>リソースの可用性

**SC-6** 情報システムでは、[選択 (1 つ以上): 優先度; クォータ; [割り当て: 組織が定義したセキュリティ対策]]に基づいて[割り当て: 組織が定義したリソース]を割り当てることによって、リソースの可用性を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンでは、Windows オペレーティング システムを実行します。 各 Windows プロセスは、プログラムを実行するために必要なリソースを提供します。 リソースの優先度は、オペレーティング システムによって管理されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 Control SC-7.a

#### <a name="boundary-protection"></a>境界保護

**SC-7.a** 情報システムでは、システムの外部境界とシステム内の主要な内部境界での通信を監視および制御する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、Application Gateway とロード バランサーをデプロイし、外部境界および内部サブネット間の通信を制御するネットワーク セキュリティ グループの規則を構成します。 監視を可能にするために、Application Gateway、ロード バランサー、ネットワーク セキュリティ グループのイベント ログと診断ログが OMS Log Analytics によって収集されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 Control SC-7.b

#### <a name="boundary-protection"></a>境界保護

**SC-7.b** 情報システムは、内部組織ネットワークから[選択: 物理的に; 論理的に]分離された、公的にアクセス可能なシステム コンポーネントのサブネットワークを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、分離された Web サブネット、データベース サブネット、Active Directory サブネット、管理サブネットを使用するアーキテクチャにリソースをデプロイします。 サブネット間のトラフィックをシステムと管理機能に必要なものだけに制限するために、個々のサブネットに適用されるネットワーク セキュリティ グループの規則に従って (外部トラフィックはデータベース、管理、または Active Directory サブネットにアクセスできないなど)、サブネットが論理的に分離されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 Control SC-7.c

#### <a name="boundary-protection"></a>境界保護

**SC-7.c** 情報システムは、組織のセキュリティ アーキテクチャに従って配置された境界保護デバイスで構成される管理インターフェイスを介してのみ、外部ネットワークまたは情報システムに接続する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、Application Gateway をデプロイして、お客様がデプロイした Web アプリケーションへの外部接続を管理します。 管理アクセス用の外部接続は、外部接続を承認済みの IP アドレスに制限するためにネットワーク セキュリティの規則が適用された管理サブネットにデプロイされている要塞ホスト/ジャンプボックスに制限されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 Control SC-7 (3)

#### <a name="boundary-protection--access-points"></a>境界保護 | アクセス ポイント

**SC-7 (3)** 組織は、情報システムへの外部ネットワーク接続の数を制限する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、2 つのパブリック IP アドレスを配置します。1 つは Application Gateway に関連付けられており、もう 1 つは管理要塞ホスト/ジャンプボックスに関連付けられています。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 Control SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>境界保護 | 外部通信サービス

**SC-7 (4).a** 組織は、外部通信サービスごとに管理インターフェイスを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、2 つのパブリック IP アドレスを配置します。1 つは Application Gateway に関連付けられており、もう 1 つは管理要塞ホスト/ジャンプボックスに関連付けられています。 これらのインタフェースの管理は、ソフトウェアによるネットワーク制御によって実現されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 Control SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>境界保護 | 外部通信サービス

**SC-7 (4).b** 組織は、管理インターフェイスごとにトラフィック フロー ポリシーを確立する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、2 つのパブリック IP アドレスを配置します。1 つは Application Gateway に関連付けられており、もう 1 つは管理要塞ホスト/ジャンプボックスに関連付けられています。 これらのインタフェースの管理は、ソフトウェアによるネットワーク制御によって実現されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 Control SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>境界保護 | 外部通信サービス

**SC-7 (4).c**: 組織は、各インターフェイスで送信される情報の機密性と整合性を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる Web アプリケーション ゲートウェイは HTTPS リスナーで構成され、通信セッションの機密性と整合性が確保されます。 また、機密性と整合性を実現するために、ジャンプボックスへのリモート デスクトップ接続も暗号化されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 Control SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>境界保護 | 外部通信サービス

**SC-7 (4).d** 組織は、サポート ミッション/ビジネス ニーズとそのニーズの継続期間を含むトラフィック フロー ポリシーの各例外を文書化する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、データセンター業務 (通信サービスを含む) に対して責任を負いません。 すべての通信サービスは、Microsoft Azure によって提供され、管理されます。 このコントロールは Azure から継承されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 Control SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>境界保護 | 外部通信サービス

**SC-7 (4).e** 組織は、[割り当て: 組織が定義した頻度]トラフィック フロー ポリシーの例外を見直し、明示的なミッション/ビジネス ニーズに対応しなくなった例外を削除する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、データセンター業務 (通信サービスを含む) に対して責任を負いません。 すべての通信サービスは、Microsoft Azure によって提供され、管理されます。 このコントロールは Azure から継承されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 Control SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>境界保護 | 既定で拒否/例外的に許可

**SC-7 (5)** 管理インターフェイスの情報システムは、ネットワーク通信トラフィックを既定で拒否し、ネットワーク通信トラフィックを例外的に許可する (つまり、すべて拒否し、例外的に許可する)。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされるネットワーク セキュリティ グループに適用されるルール セットは、既定で拒否スキームを使用して構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 Control SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>境界保護 | リモート デバイスの分割トンネリングの防止

**SC-7 (7)** リモート デバイスと連携する情報システムでは、デバイスがシステムとの非リモート接続を同時に確立して他の接続経由で外部ネットワーク内のリソースと通信するのを防ぐ。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのリモート デバイス構成ポリシーで、分割トンネリングに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 Control SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>境界保護 | 認証済みプロキシ サーバーへのトラフィックのルーティング

**SC-7 (8)** 情報システムは、[割り当て: 組織が定義した内部通信トラフィック]を、管理インターフェイスの認証済みプロキシ サーバーを介して[割り当て: 組織が定義した外部ネットワーク]にルーティングする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様が定義した情報を、認証済みプロキシを介して外部ネットワークにルーティングする責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 Control SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>境界保護 | 不正流出の防止

**SC-7 (10)** 組織は、管理インターフェイスでの情報の不正流出を防ぐ。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、管理インターフェイスでの情報の不正流出を防ぐ責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 Control SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>境界保護 | ホストベースの保護

**SC-7 (12)** 組織は、[割り当て: 組織が定義した情報システム コンポーネント]で[割り当て: 組織が定義したホストベースの境界保護メカニズム]を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンは、ホストベースのファイアウォールを有効にして構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 Control SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>境界保護 | セキュリティ ツール/メカニズム/サポート コンポーネントの分離

**SC-7 (13)** 組織は、システムの他のコンポーネントに対する管理インタフェースを持つ物理的に分離されたサブネットワークを実装することにより、[割り当て: 組織が定義した情報セキュリティ ツール、メカニズム、およびサポート コンポーネント]を他の内部情報システム コンポーネントから分離する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、お客様による情報セキュリティ ツールとサポート コンポーネントのデプロイ用に分離された管理サブネットを使用するアーキテクチャにリソースをデプロイします。 サブネットは、ネットワーク セキュリティ グループの規則に従って論理的に分離されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 Control SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>境界保護 | フェール セキュア

**SC-7 (18)** 情報システムは、境界保護デバイスの動作障害が発生した場合に安全に動作を停止する。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | Azure にデプロイされたシステムのスコープ内に物理的な境界保護デバイスはありません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure では、地理的に離れた冗長ゲートウェイ サーバーと SSL VPN をデプロイします。 ゲートウェイ システムに障害が発生すると、そのシステムは安全に動作を停止し、アクセスはその環境に制限されます。 Microsoft Azure 環境への接続を確立するには、ユーザーは Microsoft Azure によって管理されているアクティブなゲートウェイ サーバーへの別の接続を確立する必要があります。 <br /> さらに、Microsoft Azure ネットワーク デバイス (エッジ ルーター、アクセス ルーター、ロード バランサー、アグリゲーション スイッチ、TORS など) に障害が発生すると、影響を受けた回線が切断されるため、安全に動作を停止します。 Microsoft Azure ネットワーク デバイスに障害が発生しても、システム外部の情報がデバイスに入ってくることはありません。また、承認されていない情報公開が誤って許可されることもありません。 冗長性が組み込まれているため、Microsoft Azure の資産が機能しなくなっても、可用性に影響を及ぼすことはありません。 |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 Control SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>境界保護 | 動的な分離/隔離

**SC-7 (20)** 情報システムは、[割り当て: 組織が定義した情報システム コンポーネント]をシステムの他のコンポーネントから動的に分離/隔離する機能を提供する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースを動的に分離する機能をシステムが備えていることを保証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 Control SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>境界保護 | 情報システム コンポーネントの分離

**SC-7 (21)** 組織は、[割り当て: 組織が定義したミッションやビジネス機能]をサポートする[割り当て: 組織が定義した情報システム コンポーネント]を分離する境界保護メカニズムを使用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、分離された Web サブネット、データベース サブネット、Active Directory サブネット、管理サブネットを使用するアーキテクチャにリソースをデプロイします。 サブネット間のトラフィックをシステムと管理機能に必要なものだけに制限するために、個々のサブネットに適用されるネットワーク セキュリティ グループの規則に従って、サブネットが論理的に分離されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 Control SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>送信の機密性と整合性

**SC-8** 情報システムでは、送信される情報の[選択 (1 つ以上): 機密性; 整合性]を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | SI-8 (1) の実装によって、このコントロールの要件が満たされます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 Control SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>送信の機密性と整合性 | 暗号化または代替の物理的保護

**SC-8 (1)** 情報システムは、[割り当て: 組織が定義した代替の物理的な保護手段]によって保護されている場合を除き、送信時の[選択 (1 つ以上): 情報の不正な開示を防ぐ; 情報の変更を検出する]ために暗号化メカニズムを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、セキュリティで保護されたプロトコルのみを使用して通信するようにリソース構成します。 Application Gateway の WAF コンポーネントは、HTTPS/TLS 経由で外部使用のコミュニケーターを受け入れ、HTTPS/TLS 経由でのみバックエンド プールと通信するように構成されます。 SQL Server は、HTTPS/TLS 経由でのみ通信するように構成されます。 リモート デスクトップ サービスは、セキュリティで保護された接続を使用するように構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 Control SC-10

#### <a name="network-disconnect"></a>ネットワークの切断

**SC-10** 情報システムは、セッションの終了時または非アクティブな状態が[割り当て: 組織が定義した期間]続いた後に、通信セッションに関連するネットワーク接続を終了する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | リモート デスクトップ セッションの認証は、Active Directory によって管理されます。 Active Directory 内のユーザーのアクセスが無効になると、リモート セッションは直ちに終了します。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 Control SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>暗号化キーの確立と管理

**SC-12** 組織は、[割り当て: キーの生成、配布、保管、アクセス、廃棄に関する組織が定義した要件]に従って、情報システム内で使用される必須の暗号化のための暗号化キーを確立し、管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、Azure Key Vault をデプロイします。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Azure Key Vault では、FIPS 140-2 レベル 2 ハードウェア セキュリティ モジュール (HSM) キー生成機能を使用してキーを生成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 Control SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>暗号化キーの確立と管理 | 可用性

**SC-12 (1)** 組織は、ユーザーが暗号化キーを紛失した場合に情報の可用性を維持する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Key Vault を使用して、この Azure Blueprint で使用される暗号化キーとシークレットを格納します。 Key Vault により、データにアクセスして暗号化するキーのキー管理プロセスが効率化されます。 Key Vault には、アカウントをデプロイする際の Azure パスワード、仮想マシン管理者パスワード、SQL Server サービス アカウントのパスワードの各認証子が格納されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 Control SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>暗号化キーの確立と管理 | 対称キー

**SC-12 (2)** 組織は、[選択: NIST FIPS 準拠; NSA 承認]のキー管理テクノロジとプロセスを使用して、対称暗号化キーを生成、管理、および配布する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Key Vault を使用して、暗号化キーを生成、管理、配布します。 Azure Key Vault では、FIPS 140-2 レベル 2 ハードウェア セキュリティ モジュール (HSM) キー生成機能を使用してキーを生成できます。 キーは、Azure Key Vault 内の暗号化されたコンテナーに安全に格納され、管理されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 Control SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>暗号化キーの確立と管理 | 非対称キー

**SC-12 (3)** 組織は、[選択: NSA 承認のキー管理テクノロジとプロセス; 承認済みの PKI クラス 3 証明書または事前に配置されたキー材料; 承認済みの PKI クラス 3 またはクラス 4 証明書と、ユーザーの秘密キーを保護するハードウェア セキュリティ トークン]を使用して、非対称暗号化キーを生成、制御、および配布する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、非対称暗号化キーを生成、制御、配布する責任を負います (お客様がデプロイしたリソース内で使用する場合)。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 Control SC-13

#### <a name="cryptographic-protection"></a>暗号化による保護

**SC-13** 情報システムは、適用される連邦法、大統領令、指令、政策、規制、および標準に従って、[割り当て: 組織が定義した暗号化の用途および各用途に必要な暗号化の種類]を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint では、Windows 認証、リモート デスクトップ、BitLocker が使用されます。 これらのコンポーネントは、FIPS 140 検証済み暗号化モジュールを利用するように構成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 Control SC-15.a

#### <a name="collaborative-computing-devices"></a>コラボレーション コンピューティング デバイス

**SC-15.a** 情報システムでは、次の例外を除き、コラボレーション コンピューティング デバイスのリモートからのアクティブ化を禁止する:[割り当て: 組織が定義した、リモートからのアクティブ化が許可される例外]。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint の一部としてデプロイされるコラボレーション コンピューティング デバイスはありません。 注: Azure にデプロイされたシステムのスコープ内には、物理的なコラボレーション コンピューティング デバイスがあります。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 Control SC-15.b

#### <a name="collaborative-computing-devices"></a>コラボレーション コンピューティング デバイス

**SC-15.b** 情報システムは、デバイスに物理的に存在するユーザーに使用を明示的に示す。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint の一部としてデプロイされるコラボレーション コンピューティング デバイスはありません。 注: Azure にデプロイされたシステムのスコープ内には、物理的なコラボレーション コンピューティング デバイスがあります。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 Control SC-17

#### <a name="public-key-infrastructure-certificates"></a>公開キー基盤証明書

**SC-17** 組織は、[割り当て: 組織が定義した証明書ポリシー]の下で公開キー証明書を発行するか、承認済みのサービス プロバイダーから公開キー証明書を取得する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズ レベルの公開キー基盤を利用して証明書を発行できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 Control SC-18.a

#### <a name="mobile-code"></a>モバイル コード

**SC-18.a** 組織は、許容可能および許容不可能なモバイル コードとモバイル コード テクノロジを定義する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのシステムと通信の保護手順で、許容可能および許容不可能なモバイル コードを定義できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 Control SC-18.b

#### <a name="mobile-code"></a>モバイル コード

**SC-18.b** 組織は、許容可能なモバイル コードとモバイル コード テクノロジの使用制限および実装ガイダンスを定める。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのシステムと通信の保護手順で、モバイル コードの使用制限を定めることができます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 Control SC-18.c

#### <a name="mobile-code"></a>モバイル コード

**SC-18.c** 組織は、情報システム内のモバイル コードの使用を承認、監視、および制御する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズ レベルのプロセスを利用して、モバイル コードの使用を承認、監視、制御できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 Control SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**SC-19.a** 組織は、悪意を持って使用された場合に情報システムに損害を与える可能性に基づいて、Voice over Internet Protocol (VoIP) テクノロジの使用制限および実装ガイダンスを定める。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint の一部としてデプロイされる VoIP 技術はありません。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 Control SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice over Internet Protocol

**SC-19.b** 組織は、情報システム内での VoIP の使用を承認、監視、および制御する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint の一部としてデプロイされる VoIP 技術はありません。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 Control SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>セキュリティで保護された名前/アドレス解決サービス (権限のあるソース)

**SC-20.a** 情報システムでは、外部の名前/アドレス解決クエリに応答してシステムが返す信頼できる名前解決データと共に、追加のデータ送信元認証と整合性検証の成果物を提供する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティで保護された名前/アドレス解決サービスに対して責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 Control SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>セキュリティで保護された名前/アドレス解決サービス (権限のあるソース)

**SC-20.b** 情報システムでは、階層構造の分散名前空間の一部として動作しているときに、子ゾーンのセキュリティの状態を示し、(子がセキュリティで保護された解決サービスをサポートしている場合に) 親ドメインと子ドメインの間の信頼チェーンの検証を可能にする手段を提供する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティで保護された名前/アドレス解決サービスに対して責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 Control SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>セキュリティで保護された名前/アドレス解決サービス (再帰的リゾルバーまたはキャッシュ リゾルバー)

**SC-21** 情報システムは、システムが権限のあるソースから受け取る名前/アドレス解決応答に対するデータ送信元認証とデータ整合性検証を要求し、実行する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、権限のあるソースから受け取る名前/アドレス解決応答に対するデータ送信元認証とデータ整合性検証を要求および実行するように、お客様がデプロイしたリソースを構成する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 Control SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>名前/アドレス解決サービスのアーキテクチャとプロビジョニング

**SC-22** 名前/アドレス解決サービスを組織にまとめて提供する情報システムはフォールト トレラントであり、内部/外部の役割の分離を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースのアドレス解決サービスを提供するシステムがフォールト トレラントであり、内部/外部の役割の分離を実装していることを保証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 Control SC-23

#### <a name="session-authenticity"></a>セッションの信頼性

**SC-23** 情報システムでは、通信セッションの信頼性を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされるリソース (Azure Portal、リモート デスクトップ接続、Web アプリケーション ゲートウェイなど) へのリモート アクセスは、TLS を使用して保護されます。 TLS は、セッション レベルで通信の信頼性を実現します。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 Control SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>セッションの信頼性 | ログアウト時のセッション識別子の無効化

**SC-23 (1)** 情報システムでは、ユーザーのログアウト時または他のセッション終了時にセッション識別子を無効にする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされるリソース (Azure Portal、リモート デスクトップ接続、Web アプリケーション ゲートウェイなど) へのリモート アクセスは、TLS を使用して保護されます。 Azure Portal とリモート デスクトップのセッションでは、ログアウト時にセッション識別子が無効になります。 Web セッションの無効化は、Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) 規則によって強制されます。 WAF はセッションごとに Cookie アフィニティを適用し、クライアントからの非アクティブな状態が 30 分続いた後にセッション タイムアウトを実行します (組織固有の規則への展開後に構成可能)。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 Control SC-24

#### <a name="fail-in-known-state"></a>既知の状態での機能停止

**SC-24** 情報システムは、障害発生時に[割り当て: 組織が定義したシステム状態情報]を保持することで、[割り当て: 組織が定義した障害の種類]の[割り当て: 組織が定義した既知の状態]で機能を停止する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースが既知の状態で機能を停止することを保証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 Control SC-28

#### <a name="protection-of-information-at-rest"></a>保存情報の保護

**SC-28** 情報システムでは、[割り当て: 組織が定義した保存情報]の[選択 (1 つ以上): 機密性; 整合性]を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | SC-28 (1) の実装によって、このコントロールの要件が満たされます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 Control SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>保存情報の保護 | 暗号化による保護

**SC-28 (1)** 情報システムは、[割り当て: 組織が定義した情報システム コンポーネント]上の[割り当て: 組織が定義した情報]の承認されていない開示と変更を防ぐために暗号化メカニズムを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンは、ディスク暗号化を実装して保存情報の機密性と整合性を保護します。 Windows の BitLocker 機能を使用して、Windows 用の Azure Disk Encryption が実装されます。 SQL Server は、Transparent Data Encryption (TDE) を使用するように構成されます。TDE は、データとログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 TDE は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。 お客様は、保存された情報の整合性を保護するために、アプリケーション レベルの追加のコントロールを実装することもできます。 この Azure Blueprint によってデプロイされるすべてのストレージ BLOB の機密性と整合性は、Azure Storage Service Encryption (SSE) を使用して保護されます。 SSE は、256 ビット AES 暗号化を使用して Azure ストレージ アカウント内の保存データを保護します。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 Control SC-39

#### <a name="process-isolation"></a>プロセスの分離

**SC-39** 情報システムは、実行中のプロセスごとに個別の実行ドメインを保持する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされる仮想マシンでは、Windows オペレーティング システムを実行します。 Windows は、プライベート仮想アドレス空間を各プロセスに割り当てることによって、実行中のプロセスごとに個別の実行ドメインを保持します。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |
