---
title: "FedRAMP Azure Blueprint Automation - 計画"
description: "FedRAMP 用の Web アプリケーション - 計画"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 8e4d4f228940d08bc522aabcd6f4ab573ff231d4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="planning-pl"></a>計画 (PL)

> [!NOTE]
> このコントロールは、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順およびガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 Control PL-1

#### <a name="security-planning-policy-and-procedures"></a>セキュリティ計画のポリシーと手順

**PL-1** 組織は、目的、スコープ、役割、責任、経営陣のコミットメント、組織エンティティ間の調整、およびコンプライアンスを取り上げたセキュリティ計画ポリシーと、セキュリティ計画ポリシーおよびセキュリティ計画の関連するコントロールの実装を容易にする手順を策定、文書化し、[割り当て: 組織が定義した担当者または役割]に周知徹底する。また、現在のセキュリティ計画ポリシーを[割り当て: 組織が定義した頻度]、セキュリティ計画の手順を[割り当て: 組織が定義した頻度]見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ計画のポリシーと手順で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 Control PL-2.a

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.a** 組織は、組織のエンタープライズ アーキテクチャに適合し、システムの承認境界の明示的な定義、ミッションとビジネス プロセスの観点での情報システムの運用コンテキストの説明、裏付けとなる根拠を含む情報システムのセキュリティ分類の提供、情報システムの運用環境および他の情報システムとの関係または接続の説明、システムのセキュリティ要件の概要の説明、関連するオーバーレイの特定 (該当する場合)、テーラリングの決定の根拠を含め、要件を満たすために実施または計画されているセキュリティ コントロールの説明を行う、情報システムのセキュリティ プランを策定し、プランの実施前に運用承認責任者または指名された担当者のレビューと承認を受ける。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、対象の認定 (FedRAMP など) で定義された基準を満たすシステム セキュリティ プラン (SSP) を策定する責任を負います。 お客様は、NIST Special Publication 800-18 R1「Guide for Developing Security Plans for Federal Information Systems (連邦情報システムのためのセキュリティ プラン策定ガイド)」を参照できます。 お客様の SSP では、Microsoft Azure から継承されたコントロールに対処する必要があります。実装の詳細については、Microsoft Azure の SSP を参照してください。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 Control PL-2.b

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.b** 組織はセキュリティ プランのコピーを配布し、プランの以降の変更を[割り当て: 組織が定義した担当者または役割]に伝える。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ プランを配布する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 Control PL-2.c

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.c** 組織は、[割り当て: 組織が定義した頻度]情報システムのセキュリティ プランを見直す。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ プランを見直す責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 Control PL-2.d

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.d** 組織はプランを更新して、情報システム/運用環境の変更や、プランの実施中またはセキュリティ コントロールの評価中に特定された問題に対処する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ プランを更新する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 Control PL-2.e

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.e**  組織は、許可されていない開示や変更からセキュリティ プランを保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ プランを保護する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 Control PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>システム セキュリティ プラン | 計画/他の組織エンティティとの調整

**PL-2 (3)** 組織は、情報システムに影響を及ぼすセキュリティ関連のアクティビティを計画し、他の組織エンティティへの影響を軽減するために、当該アクティビティを実施する前に[割り当て: 組織が定義した個人またはグループ]と調整する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティ関連のアクティビティを計画し、他の組織エンティティへの影響を軽減するために調整を行う責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 Control PL-4.a

#### <a name="rules-of-behavior"></a>行動規則

**PL-4.a** 組織は、情報および情報システムの使用に関する責任と求められる行動を示す規則を確立し、情報システムにアクセスする必要がある個人がすぐに利用できるようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規則で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 Control PL-4.b

#### <a name="rules-of-behavior"></a>行動規則

**PL-4.b** 組織は、情報および情報システムへのアクセスを承認する前に、各個人が行動規則を読み、理解し、これに従うことに同意したことを示す署名済みの確認書を当該個人から受領する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規則で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 Control PL-4.c

#### <a name="rules-of-behavior"></a>行動規則

**PL-4.c** 組織は、[割り当て：組織が定義した頻度]行動規則を見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規則で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 Control PL-4.d

#### <a name="rules-of-behavior"></a>行動規則

**PL-4.d** 組織は、行動規則を改訂/更新したときに、以前のバージョンの行動規則に署名済みの個人に対して、行動規則を読んで再署名することを要求する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規則で、このコントロールに対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 Control PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>行動規則 | ソーシャル メディア/ネットワーキングの制限事項

**PL-4 (1)** 組織は、ソーシャル メディア/ネットワーキング サイトの使用およびパブリック Web サイトへの組織情報の投稿に関する明示的な制限事項を行動規則に含める。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規則に、ソーシャル メディア/ネットワーキング サイトに関する制限事項を含めることができます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 Control PL-8.a

#### <a name="information-security-architecture"></a>情報セキュリティ アーキテクチャ

**PL-8.a** 組織は、組織情報の機密性、整合性、可用性の保護に関する全体的な理念、要件、および採用する手法、情報セキュリティ アーキテクチャを統合し、エンタープライズ アーキテクチャをサポートする方法、外部サービスに関する情報セキュリティの前提事項と依存関係を示す、情報システムの情報セキュリティ アーキテクチャを開発する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースの情報セキュリティ アーキテクチャを開発する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 Control PL-8.b

#### <a name="information-security-architecture"></a>情報セキュリティ アーキテクチャ

**PL-8.b** 組織は、エンタープライズ アーキテクチャの更新を反映するために、[割り当て: 組織が定義した頻度]情報セキュリティ アーキテクチャを見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、情報セキュリティ アーキテクチャを見直し、更新する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 Control PL-8.c

#### <a name="information-security-architecture"></a>情報セキュリティ アーキテクチャ

**PL-8.c** 組織は、情報セキュリティ アーキテクチャの計画された変更が、セキュリティ プラン、セキュリティ運用構想書 (CONOPS)、および組織の調達/取得に確実に反映されるようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、情報セキュリティ アーキテクチャの計画された変更を考慮する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |
