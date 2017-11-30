---
title: "FedRAMP Azure Blueprint Automation - 構成管理"
description: "FedRAMP 用の Web アプリケーション - 構成管理"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: e93aa430b7150f07210f5d1f37e2027d95334a59
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="configuration-management-cm"></a>構成管理 (CM)

> [!NOTE]
> これらのコントロールは、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順およびガイダンスについては、NIST 800 53 Rev. 4 をご覧ください。

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 コントロール CM-1

#### <a name="configuration-management-policy-and-procedures"></a>構成管理ポリシーおよび手順

**CM-1** 組織は、目的、範囲、役割、責任、経営コミットメント、組織エンティティ間の調整、およびコンプライアンスに対処した構成管理ポリシー、および構成管理ポリシーとそれに関連する構成管理コントロールの実装を促進するための手順を開発して文書化し、[割り当て: 組織で定義された担当者または役割]に配布します。また、現在の構成管理ポリシーを[割り当て: 組織で定義された頻度]で、および構成管理手順を[割り当て: 組織で定義された頻度]でレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客のエンタープライズ レベルの構成管理ポリシーおよび手順は、このコントロールに対処するために十分である可能性があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 コントロール CM-2

#### <a name="baseline-configuration"></a>ベースライン構成

**CM-2** 組織は、情報システムの現在のベースライン構成を開発して文書化し、構成コントロールのもとで管理します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint を構成する Azure Resource Manager テンプレートおよび付随するリソースは、デプロイされたアーキテクチャの "コードとしての構成" ベースラインを表します。 このソリューションは、構成コントロールに使用できる GitHub 経由で提供されます。 このソリューションには、デプロイされた仮想マシンごとの望ましい状態への構成 (DSC) ベースラインが含まれています。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 コントロール CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>ベースライン構成 | レビューおよび更新

**CM-2 (1).a** 組織は、情報システムのベースライン構成を[割り当て: 組織で定義された頻度]でレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソース (アプリケーション、オペレーティング システム、データベース、およびソフトウェアを含む) のベースライン構成のレビューおよび更新に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 コントロール CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>ベースライン構成 | レビューおよび更新

**CM-2 (1).b** 組織は、[割り当て: 組織で定義された状況]のために、必要に応じて情報システムのベースライン構成をレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースのベースライン構成の必要に応じたレビューおよび更新に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 コントロール CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>ベースライン構成 | レビューおよび更新

**CM-2 (1).c** 組織は、情報システム コンポーネントのインストールおよびアップグレードの不可欠な部分として、情報システムのベースライン構成をレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースのベースライン構成の必要に応じたレビューおよび更新に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 コントロール CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>ベースライン構成 | 正確性/最新性に対する自動化サポート

**CM-2 (2)** 組織は、情報システムの最新で、完全で、正確な、かつすぐに使用可能なベースライン構成を維持するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint を構成する Azure Resource Manager テンプレートおよび付随するリソースは、デプロイされたアーキテクチャの "コードとしての構成" ベースラインを表します。 このソリューションは、構成コントロールに使用できる GitHub 経由で提供されます。 Azure Portal では、デプロイされたすべてのリソースに対して自動化スクリプトを使用でき、これらのリソースの常に最新の表現が提供されます。  |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 コントロール CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>ベースライン構成 | 以前の構成の保持

**CM-2 (3)** 組織は、ロールバックをサポートするために、[割り当て: 情報システムのベースライン構成の組織で定義された以前のバージョン]を保持します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関するベースライン構成の以前のバージョンの保持に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 コントロール CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>ベースライン構成 | 危険度の高い領域用のシステム、コンポーネント、またはデバイスの構成

**CM-2 (7).a** 組織は、[割り当て: 組織で定義された構成]を備えた[割り当て: 組織で定義された情報システム、システム コンポーネント、またはデバイス]を、組織が重大なリスクが存在すると見なす場所に移動している個人に発行します。

**責任:** `Not Applicable`

|||
|---|---|
| **顧客** | Azure 上にデプロイされたシステムの範囲内に、顧客によって制御された物理デバイスは存在しません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure の顧客のコンテンツが、物理的に米国本土内に配置されている Microsoft Azure の外部に格納されることはありません。 Microsoft Azure の担当者が、Microsoft Azure インベントリ内に含まれているデバイスを持って移動することはありません。 そのため、このコントロールは Microsoft Azure には適用されません。 |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 コントロール CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>ベースライン構成 | 危険度の高い領域用のシステム、コンポーネント、またはデバイスの構成

**CM-2 (7).b** 組織は、個人から返却されたデバイスに[割り当て: 組織で定義されたセキュリティ保護]を適用します。

**責任:** `Not Applicable`

|||
|---|---|
| **顧客** | Azure 上にデプロイされたシステムの範囲内に、顧客によって制御された物理デバイスは存在しません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure の顧客のコンテンツが Microsoft Azure の外部に格納されることはなく、Microsoft Azure の担当者が、Microsoft Azure インベントリ内に含まれているデバイスを持って移動することはないため、このコントロールは適用されません。 |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 コントロール CM-3.a

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.a** 組織は、構成によって制御された情報システムへの変更の種類を決定します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソース (アプリケーション、オペレーティング システム、データベース、およびソフトウェアを含む) へのどのような種類の変更が構成によって制御されるかの決定に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 コントロール CM-3.b

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.b** 組織は、情報システムへの構成によって制御された提案されている変更をレビューし、セキュリティ影響分析を明示的に考慮してこのような変更を承認または拒否します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの構成によって制御された提案されている変更のレビューに責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 コントロール CM-3.c

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.c** 組織は、情報システムに関連する構成変更の決定を文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関連する構成によって制御された変更の文書化に責任を負います (CM-03.b を参照)。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 コントロール CM-3.d

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.d** 組織は、情報システムへの構成によって制御された承認されている変更を実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | は、CM-03.b で承認された、構成によって制御された変更の実装に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 コントロール CM-3.e

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.e** 組織は、情報システムへの構成によって制御された変更のレコードを[割り当て: 組織で定義された期間]にわたって保持します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの構成によって制御された変更のレコードの保持に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 コントロール CM-3.f

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.f** 組織は、情報システムへの構成によって制御された変更に関連するアクティビティを監査およびレビューします。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、構成変更の監査およびレビューに責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 コントロール CM-3.g

#### <a name="configuration-change-control"></a>構成変更コントロール

**CM-3.g** 組織は、[選択 (1 つ以上): [割り当て: 組織で定義された頻度]、[割り当て: 組織で定義された構成変更条件]]を集めた[割り当て: 組織で定義された構成変更コントロール要素 (委員会、役員会など)] を通して構成変更コントロール アクティビティに対する監視を調整および提供します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、構成変更コントロール アクティビティに対する監視の調整および提供に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 コントロール CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).a** 組織は、情報システムへの提案された変更を文書化するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、提案された変更を文書化するための自動化されたメカニズムの使用に責任を負います (CM-03.b を参照)。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 コントロール CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).b** 組織は、[割り当て: 組織で定義された承認機関]に情報システムへの提案された変更および変更承認の要求を通知するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの提案された変更の承認をルーティングおよび要求するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 コントロール CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).c** 組織は、[割り当て: 組織で定義された期間]までに承認または拒否されていない情報システムへの提案された変更を強調するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、まだレビューされていない変更提案を強調するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 コントロール CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).d** 組織は、指定された承認が受信されるまで情報システムへの変更を禁止するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの承認されていない変更の実装を禁止するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 コントロール CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).e** 組織は、情報システムへのすべての変更を文書化するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの実装されたすべての変更を文書化するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 コントロール CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>構成変更コントロール | 変更の自動化された文書化/通知/禁止

**CM-3 (1).f** 組織は、情報システムへの承認された変更が完了したら、[割り当て: 組織で定義された担当者]に通知するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの承認された変更が完了したら通知を提供するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 コントロール CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>構成変更コントロール | 変更のテスト/検証/文書化

**CM-3 (2)** 組織は、運用システムで変更を実装する前に、情報システムへの変更をテスト、検証、および文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、実装前の、顧客がデプロイしたリソースへの変更のテスト、検証、および文書化に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 コントロール CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>構成変更コントロール | セキュリティ担当者

**CM-3 (4)** 組織は、情報セキュリティ担当者に[割り当て: 組織で定義された構成変更コントロール要素]のメンバーに割り当てます。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、情報セキュリティ担当者の CM-03.g で定義された変更コントロール要素のメンバーへの割り当てに責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 コントロール CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>構成変更コントロール | 暗号化の管理

**CM-3 (6)** 組織は、[割り当て: 組織で定義されたセキュリティ保護]を提供するために使用される暗号化メカニズムが構成管理のもとにあることを保証します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、暗号化メカニズムが構成管理のもとにあることの保証に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 コントロール CM-4

#### <a name="security-impact-analysis"></a>セキュリティ影響分析

**CM-4** 組織は、変更を実装する前に、情報システムへの変更を分析してセキュリティへの潜在的な影響を判定します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへの提案された変更の分析に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 コントロール CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>セキュリティ影響分析 | 分離されたテスト環境

**CM-4 (1)** 組織は、運用環境での実装の前に分離されたテスト環境で情報システムへの変更を分析して、欠陥、弱点、非互換性、または故意の悪意によるセキュリティへの影響を調べます。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、運用環境での実装前の、テスト環境での顧客がデプロイしたリソースへの提案された変更の分析に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 コントロール CM-5

#### <a name="access-restrictions-for-change"></a>変更に関するアクセス制限

**CM-5** 組織は、情報システムへの変更に関連する物理および論理的なアクセス制限を定義、文書化、承認、および適用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | ユーザーにロールを割り当て、どのユーザーがデプロイされたリソースを表示したり制御したりできるかに関する厳密な制御を提供することにより、ロールベースのアクセス制御を使用して Azure Active Directory アカウント特権が実装されます。 ユーザーをセキュリティ グループに割り当てることにより、ロールベースのアクセス制御を使用して Active Directory アカウント特権が実装されます。 これらのセキュリティ グループは、オペレーティング システムの構成に関してユーザーが実行できるアクションを制御します。 これらのロールベースのスキームは、ミッションニーズを満たすように顧客が拡張できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 コントロール CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>変更に関するアクセス制限 | 自動化されたアクセスの適用/監査

**CM-5 (1)** 情報システムはアクセス制限を適用し、適用アクションの監査をサポートします。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | ユーザーにロールを割り当て、どのユーザーがデプロイされたリソースを表示したり制御したりできるかに関する厳密な制御を提供することにより、ロールベースのアクセス制御を使用して Azure Active Directory アカウント特権が実装されます。 ユーザーをセキュリティ グループに割り当てることにより、ロールベースのアクセス制御を使用して Active Directory アカウント特権が実装されます。 これらのセキュリティ グループは、オペレーティング システムの構成に関してユーザーが実行できるアクションを制御します。 すべてのアクセスおよびアクセス試行が監査されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 コントロール CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>変更に関するアクセス制限 | システムの変更のレビュー

**CM-5 (2)** 組織は、承認されていない変更が発生しているかどうかを判定するために、情報システムの変更および[割り当て: 組織で定義された状況]を[割り当て: 組織で定義された頻度]でレビューします。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、承認されていない変更が発生しているかどうかを判定するための、顧客がデプロイしたリソースへの変更のレビューに責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 コントロール CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>変更に関するアクセス制限 | 署名されたコンポーネント

**CM-5 (3)** 情報システムは、組織によって認識および承認された証明書を使用して、コンポーネントがデジタル署名されていることを検証することなく[割り当て: 組織で定義されたソフトウェアおよびファームウェア コンポーネント]のインストールを防止します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint によってデプロイされた仮想マシンは、特定のアプリケーションをどのユーザーがインストールまたは実行できるかを指定するために Windows AppLocker を実装します。 さらに、すべての Windows オペレーティングシステム更新がデジタル署名されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 コントロール CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>変更に関するアクセス制限 | 運用/操作特権の制限

**CM-5 (5).a** 組織は、運用または操作環境内の情報システム コンポーネントおよびシステム関連の情報を変更するための特権を制限します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイした運用または操作環境内で変更を行うための特権の制限に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 コントロール CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>変更に関するアクセス制限 | 運用/操作特権の制限

**CM-5 (5).b** 組織は、特権を[割り当て: 組織で定義された頻度]でレビューおよび再評価します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、CM-05(05).a で定義された特権のレビューおよび再評価に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 コントロール CM-6.a

#### <a name="configuration-settings"></a>構成設定

**CM-6.a** 組織は、運用要件と整合性のある最も制限的なモードを反映した[割り当て: 組織で定義されたセキュリティ構成チェックリスト]を使用して、情報システム内で使用される情報技術製品の構成設定を確立および文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint には、デプロイされた仮想マシンごとの望ましい状態への構成 (DSC) ベースラインが含まれています。 これらの宣言型の PowerShell スクリプトは、適用対象のリソースを定義および構成します。 このソリューションによってデプロイされたリソースに含まれているベースライン DSC は、ミッション ニーズを満たすように顧客が拡張できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 コントロール CM-6.b

#### <a name="configuration-settings"></a>構成設定

**CM-6.b** 組織は、構成設定を実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint には、デプロイされた仮想マシンごとの望ましい状態への構成 (DSC) ベースラインが含まれています。 このベースラインは、カスタム スクリプト仮想マシン拡張機能を使用して、デプロイ中に仮想マシンに自動的に適用されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 コントロール CM-6.c

#### <a name="configuration-settings"></a>構成設定

**CM-6.c** 組織は、[割り当て: 組織で定義された運用要件]に基づいて、[割り当て: 組織で定義された情報システム コンポーネント]に関する確立された構成設定からのすべての偏差を識別、文書化、および承認します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関する確立された構成設定からのすべての偏差の識別、文書化、および承認に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 コントロール CM-6.d

#### <a name="configuration-settings"></a>構成設定

**CM-6.d** 組織は、組織のポリシーおよび手順に従って、構成設定への変更を監視および制御します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、Automation DSC をデプロイします。 Automation DSC は、マシンの構成を組織で定義された特定の構成と整合させます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 コントロール CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>構成設定 | 自動化された集中管理/適用/検証

**CM-6 (1)** 組織は、[割り当て: 組織で定義された情報システム コンポーネント]の構成設定を集中管理、適用、および検証するための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、Azure Automation DSC をデプロイします。 Automation DSC は、マシンの構成を組織で定義された特定の構成と整合させ、変更を継続的に監視します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 コントロール CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>構成設定 | 承認されていない変更への対応

**CM-6 (2)** 組織は、[割り当て: 組織で定義された構成設定]への承認されていない変更に対応するために、[割り当て: 組織で定義されたセキュリティ保護]を使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、Azure Automation DSC をデプロイします。 Azure の Operations Management Suite (OMS) の一部である Automation DSC は、誤った構成が検出されたらアラートを生成するか、またはその構成を修正するように構成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 コントロール CM-7.a

#### <a name="least-functionality"></a>最小限の機能

**CM-7.a** 組織は、不可欠な機能のみを提供するように情報システムを構成します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint によってデプロイされたリソースは、意図された目的のための最小限の機能を提供するように構成されます。 デプロイされた仮想マシンごとの望ましい状態への構成 (DSC) ベースラインが含まれています。 これらの宣言型の PowerShell スクリプトは、適用対象のリソースを定義および構成します。 このソリューションによってデプロイされたリソースに含まれているベースライン DSC は、機能をさらに制限してミッションニーズを満たすように顧客が拡張できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 コントロール CM-7.b

#### <a name="least-functionality"></a>最小限の機能

**CM-7.b** 組織は、次の関数、ポート、プロトコル、またはサービスの使用を禁止または制限します: [割り当て: 組織で定義された禁止または制限された関数、ポート、プロトコル、またはサービス]。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、ポートとプロトコルの使用を必要なユーザーのみに制限するために、Azure Application Gateway およびネットワーク セキュリティ グループをデプロイします。 Application Gateway、ネットワーク セキュリティ グループ、および仮想マシンの DSC ベースラインは、関数、ポート、プロトコル、およびサービスの使用を制限して意図された機能のみを提供するように顧客がさらに構成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 コントロール CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>最小限の機能 | 定期的なレビュー

**CM-7 (1).a** 組織は、不必要なまたはセキュリティで保護されていない関数、ポート、プロトコル、およびサービスを識別するために、情報システムを[割り当て: 組織で定義された頻度]でレビューします。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、不必要なまたはセキュリティで保護されていない関数、ポート、プロトコル、およびサービスを識別するための、顧客がデプロイしたリソース (アプリケーション、オペレーティング システム、データベース、およびソフトウェアを含む) のレビューに責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 コントロール CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>最小限の機能 | 定期的なレビュー

**CM-7 (1).b** 組織は、[割り当て: 不必要またはセキュリティで保護されていないと見なされた情報システム内の組織で定義された関数、ポート、プロトコル、およびサービス]を無効にします。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、不必要またはセキュリティで保護されていないと見なされた関数、ポート、プロトコル、およびサービスの無効化に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 コントロール CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>最小限の機能 | プログラムの実行の防止

**CM-7 (2)** 情報システムは、[選択 (1 つ以上): [割り当て: ソフトウェア プログラムの使用と制限に関連した組織で定義されたポリシー]、ソフトウェア プログラムの使用の条項および条件を承認する規則]に従って、プログラムの実行を防止します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客が定義したソフトウェア プログラムの使用ポリシーに従ったプログラムの実行の防止に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 コントロール CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>最小限の機能 | 承認されたソフトウェア/ホワイトリスト登録

**CM-7 (5).a** 組織は、[割り当て: 情報システムでの実行が承認された組織で定義されたソフトウェア プログラム]を識別します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、承認されたソフトウェア プログラムの識別に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 コントロール CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>最小限の機能 | 承認されたソフトウェア/ホワイトリスト登録

**CM-7 (5).b** 組織は、情報システムでの承認されたソフトウェア プログラムの実行を許可するために、すべてを拒否し、例外を許可するポリシーを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースでの承認されたソフトウェア プログラムの実行を許可するための、すべてを拒否し、例外を許可するポリシーの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 コントロール CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>最小限の機能 | 承認されたソフトウェア/ホワイトリスト登録

**CM-7 (5).c** 組織は、承認されたソフトウェア プログラムの一覧を[割り当て: 組織で定義された頻度]でレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、承認されたソフトウェア プログラムの一覧のレビューおよび更新に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 コントロール CM-8.a

#### <a name="information-system-component-inventory"></a>情報システム コンポーネント インベントリ

**CM-8.a** 組織は、現在の情報システムを正確に反映し、情報システムの承認境界内のすべてのコンポーネントを含み、追跡および報告に必要と見なされる粒度レベルにあり、さらに[割り当て: 効果的な情報システム コンポーネントのアカウンタビリティを実現するために必要なと見なされる組織で定義された情報]を含む情報システム コンポーネントのインベントリを開発および文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure Resource Manager は、デプロイされたリソースの常に最新の一覧を提供し、インベントリ管理のためにリソースをタグ付けおよびグループ化するようにカスタマイズできます。 このソリューションによってデプロイされたリソースには、システムの境界に関連付けることができる特定のリソース タグが付けられます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 コントロール CM-8.b

#### <a name="information-system-component-inventory"></a>情報システム コンポーネント インベントリ

**CM-8.b** 組織は、情報システム コンポーネント インベントリを[割り当て: 組織で定義された頻度]でレビューおよび更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure Resource Manager は、Azure Portal でのレビューに使用できる、デプロイされたリソースの常に最新の一覧を提供します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 コントロール CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>情報システム コンポーネント インベントリ | インストール/削除中の更新

**CM-8 (1)** 組織は、コンポーネントのインストール、削除、および情報システムの更新の不可欠な部分として、情報システム コンポーネントのインベントリを更新します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure Portal のリソース ブレードは、デプロイされたすべてのリソースを一覧表示し、リソースがデプロイされたり削除されたりすると常に最新のインベントリを提供します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 コントロール CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>情報システム コンポーネント インベントリ | 自動化されたメンテナンス

**CM-8 (2)** 組織は、情報システム コンポーネントの最新で、完全で、正確な、かつすぐに使用可能なインベントリを維持しやすくするための自動化されたメカニズムを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure Portal のリソース ブレードは、デプロイされたすべてのリソースを一覧表示し、リソースがデプロイされたり削除されたりすると常に最新のインベントリを提供します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 コントロール CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>情報システム コンポーネント インベントリ | 承認されていないコンポーネントの自動化された検出

**CM-8 (3).a** 組織は、情報システム内の承認されていないハードウェア、ソフトウェア、およびファームウェア コンポーネントの存在を検出するための自動化されたメカニズムを[割り当て: 組織で定義された頻度]で使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソース内の承認されていないソフトウェアの存在を検出するための自動化されたメカニズムの使用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 コントロール CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>情報システム コンポーネント インベントリ | 承認されていないコンポーネントの自動化された検出

**CM-8 (3).b** 組織は、承認されていないコンポーネントが検出されると次のアクションを実行します: [選択 (1 つ以上): このようなコンポーネントによるネットワーク アクセスを無効にする、そのコンポーネントを分離する、[割り当て: 組織で定義された担当者または役割]に通知する]。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、承認されていないソフトウェアが検出されたときのアクションの実行に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 コントロール CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>情報システム コンポーネント インベントリ | アカウンタビリティ情報

**CM-8 (4)** 組織は、情報システム コンポーネント インベントリ情報内に、これらのコンポーネントの管理に責任を負うか、または説明責任のある個人を[選択 (1 つ以上): 名前、地位、役割]で識別するための手段を含めます。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure のリソース タグは、アカウンタビリティまたは管理の目的でリソースを分類するために使用できるキー/値のペアです。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 コントロール CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>情報システム コンポーネント インベントリ | コンポーネントの複製なしのアカウンティング

**CM-8 (5)** 組織は、情報システムの承認境界内のすべてのコンポーネントが他の情報システム コンポーネント インベントリに複製されていないことを検証します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint は、すべてのリソースを Azure Resource Manager リソース グループにデプロイします。 Azure Resource Manager は、デプロイされたリソースの常に最新の一覧を提供します。 このソリューションによってデプロイされたリソースには、システムの境界に関連付けることができる特定のリソース タグが付けられます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 コントロール CM-9.a

#### <a name="configuration-management-plan"></a>構成管理プラン

**CM-9.a** 組織は、役割、責任、および構成管理のプロセスと手順に対処した情報システムの構成管理プランを開発、文書化、および実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関する構成管理プランの開発、文書化、および実装に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 コントロール CM-9.b

#### <a name="configuration-management-plan"></a>構成管理プラン

**CM-9.b** 組織は、システム開発ライフサイクルを通して構成項目を識別し、構成項目の構成を管理するためのプロセスを確立する情報システムの構成管理プランを開発、文書化、および実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関する構成管理プランの開発、文書化、および実装に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 コントロール CM-9.c

#### <a name="configuration-management-plan"></a>構成管理プラン

**CM-9.c** 組織は、情報システムの構成項目を定義し、構成項目を構成管理のもとに配置する情報システムの構成管理プランを開発、文書化、および実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関する構成管理プランの開発、文書化、および実装に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 コントロール CM-9.d

#### <a name="configuration-management-plan"></a>構成管理プラン

**CM-9.d** 組織は、承認されていない開示および変更から構成管理プランを保護する情報システムの構成管理プランを開発、文書化、および実装します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースに関する構成管理プランの開発、文書化、および実装に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 コントロール CM-10.a

#### <a name="software-usage-restrictions"></a>ソフトウェアの使用制限

**CM-10.a** 組織は、契約および著作権法に従って、ソフトウェアとそれに関連するドキュメントを使用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint によってデプロイされたリソースには Windows および SQL Server ライセンスが含まれています。 これは Azure の組み込みの機能です。 既存のソフトウェア ライセンス契約を結んでいる組織は、代わりのライセンス モデルの採用を検討できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 コントロール CM-10.b

#### <a name="software-usage-restrictions"></a>ソフトウェアの使用制限

**CM-10.b** 組織は、コピーおよび配布を制御するために、数量ライセンスによって保護されたソフトウェアとそれに関連するドキュメントの使用を追跡します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint によってデプロイされたリソースには Windows および SQL Server ライセンスが含まれています。 ライセンスの使用をユーザーが個別に追跡する必要はありません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 コントロール CM-10.c

#### <a name="software-usage-restrictions"></a>ソフトウェアの使用制限

**CM-10.c** 組織は、ピアツーピアのファイル共有テクノロジが著作権で保護された製品の承認されていない配布、表示、パフォーマンス、または複製に使用されないようにするために、この機能の使用を制御および文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | この Azure Blueprint によってデプロイされたピアツーピアのファイル共有機能は存在しません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 コントロール CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>ソフトウェアの使用制限 | オープン ソース ソフトウェア

**CM-10 (1)** 組織は、オープン ソース ソフトウェアの使用に対する次の制限を確立します: [割り当て: 組織で定義された制限]。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客のエンタープライズ レベルの構成管理ポリシーは、オープン ソース ソフトウェアの使用に対する制限に対処している可能性があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 コントロール CM-11.a

#### <a name="user-installed-software"></a>ユーザーがインストールするソフトウェア

**CM-11.a** 組織は、ユーザーによるソフトウェアのインストールを管理する[割り当て: 組織で定義されたポリシー]を確立します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、顧客がデプロイしたリソースへのユーザーによるソフトウェアのインストールを管理するポリシーの確立に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 コントロール CM-11.b

#### <a name="user-installed-software"></a>ユーザーがインストールするソフトウェア

**CM-11.b** 組織は、[割り当て: 組織で定義された方法]を使用してソフトウェア インストール ポリシーを適用します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、ソフトウェア インストール ポリシーの適用に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 コントロール CM-11.c

#### <a name="user-installed-software"></a>ユーザーがインストールするソフトウェア

**CM-11.c** 組織は、ポリシー コンプライアンスを[割り当て: 組織で定義された頻度]で監視します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、CM-11.a で識別されたポリシーによる顧客がデプロイしたリソースのコンプライアンスの監視に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 コントロール CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>ユーザーがインストールするソフトウェア | 承認されていないインストールに対するアラート

**CM-11 (1)** 情報システムは、承認されていないソフトウェアのインストールが検出されたら、[割り当て: 組織で定義された担当者または役割]にアラートを送信します。

**責任:** `Customer Only`

|||
|---|---|
| **顧客** | 顧客は、承認されていないソフトウェアのインストールが検出されたときのアラートの送信に責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |

