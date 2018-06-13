---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化 - 計画
description: FedRAMP Web アプリケーションの自動化 - 計画
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
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: d5e5666db71ac9f5a136c9acb448fb13bf39a853
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151100"
---
# <a name="planning-pl"></a>計画 (PL)

> [!NOTE]
> この管理策は、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順およびガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 管理策 PL-1

#### <a name="security-planning-policy-and-procedures"></a>セキュリティ計画のポリシーと手順

**PL-1** 組織は、
a. 以下を策定、文書化し、[指定：組織が定めた職員または役職]に配布する：
1. 目的、適用範囲、役割、責任、経営コミットメント、組織間の調整、およびコンプライアン
スを取り扱う、セキュリティ計画のポリシー
2. セキュリティ計画のポリシーと、関連する「セキュリティ計画」管理策の実施を容易にす
るための手順
b. 以下の最新版をレビューし、更新する：
1. セキュリティ計画保護のポリシーを[指定：組織が定めた頻度で]
2. セキュリティ計画の手順を[指定：組織が定めた頻度で] 

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルのセキュリティ計画のポリシーと手順で、この管理策に十分対処できると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 Control PL-2.a

#### <a name="system-security-plan"></a>システム セキュリティ プラン

**PL-2.a** 組織は、
a. 以下を満たす、情報システムのセキュリティ計画を作成する：
1. 組織のエンタープライズアーキテクチャに適合する
2. システムに対する認可を出す範囲を明示的に定義する
3. ミッション／業務プロセスの観点から、情報システムをどのように使用できるかについ
て説明する
4. 情報システムのセキュリティカテゴリを、裏付けとなる根拠と共に示す
5. 情報システムが稼働する環境と、他の情報システムとの関係またはつながりを示す
6. システムのセキュリティ要求事項の概要を示す
7. 関連するすべてのオーバーレイを示す（該当する場合）
8. それらの要求事項を満たすために導入されている、または導入が計画されているセキ
ュリティ管理策について（調整に関する意思決定の根拠を含めて）記述する
9. 計画の実施に先立ち、運用認可責任者または運用認可責任者が指定した代表者によ
ってレビューされ、承認される

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、対象の認可 (FedRAMP など) で定義された基準を満たすシステム セキュリティ計画 (SSP) を策定する責任を負います。 お客様は、NIST Special Publication 800-18 R1「Guide for Developing Security Plans for Federal Information Systems (連邦情報システムのためのセキュリティ計画作成ガイド)」を参照できます。 お客様の SSP では、Microsoft Azure から継承されたコントロールに対処する必要があります。実装の詳細については、Microsoft Azure の SSP を参照してください。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 Control PL-2.b

#### <a name="system-security-plan"></a>システムセキュリティ計画

**PL-2.b** 組織はセキュリティ計画のコピーを[指定： 組織が定めた職員または役職] に配布し、その後の変
更について通知する

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ計画を配布する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 Control PL-2.c

#### <a name="system-security-plan"></a>システムセキュリティ計画

**PL-2.c** 組織は、情報システムのセキュリティ計画を [指定: 組織が定めた頻度で] レビューする

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ計画をレビューする責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 Control PL-2.d

#### <a name="system-security-plan"></a>システムセキュリティ計画

**PL-2.d** 情報システムの変化 ／システムが稼働する環境の変化や、計画の実施またはセキュリティ
管理策のアセスメント時に特定された問題に対処するために、 計画を更新する

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ計画を更新する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 Control PL-2.e

#### <a name="system-security-plan"></a>システムセキュリティ計画

**PL-2.e**  組織は、セキュリティ計画を、不正な開示や変更から保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、システム セキュリティ計画を保護する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 Control PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>システム セキュリティ プラン | 計画/他の組織エンティティとの調整

**PL-2 (3)** 組織は、情報システムに影響を与えるセキュリティ関連の活動に関して、そうした活動を実
施する前に［指定：組織が定めた個人またはグループ］との間で活動を計画・調整すること
によって、組織内の他の部署に対する影響を減らす。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、セキュリティ関連の活動を計画し、組織内の他の部署への影響を軽減するために調整を行う責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 Control PL-4.a

#### <a name="rules-of-behavior"></a>行動規範

**PL-4.a** 組織は、情報システムに対するアクセスを要求する個人に対して、情報と情報システムの使用に関
する彼らの責任と期待される振る舞いを記したルールを作成し、すぐに利用できるようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規範で、この管理策に十分対処できると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 Control PL-4.b

#### <a name="rules-of-behavior"></a>行動規範

**PL-4.b** 組織は、そうした個人に情報と情報システムに対するアクセスを許可する前に、彼らが行動規範を
読んで理解したことと、行動規範に従うことに同意したことを示す署名による同意を得る。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規範で、この管理策に十分対処できると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 Control PL-4.c

#### <a name="rules-of-behavior"></a>行動規範

**PL-4.c** 組織は、行動規範を［指定：組織が定めた頻度で］レビューし、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規範で、この管理策に十分対処できると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 Control PL-4.d

#### <a name="rules-of-behavior"></a>行動規範

**PL-4.d** 組織は、行動規範が修正／更新された場合に、以前のバージョンの行動規範に署名した個人に対して、新しい行動規範を読んで、再度署名することを要求する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規範で、この管理策に十分対処できると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 Control PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>行動規範 | ソーシャルメディア / ネットワーキングの制限

**PL-4 (1)** 組織は、行動規範に、ソーシャルメディア／ネットワーキングサイトの利用と、組織の情報をパブリックなウェブサイトに載せることに対する明確な制限を含める。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズ レベルの行動規範に、ソーシャル メディア/ネットワーキング サイトに関する制限事項を含めることができます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 Control PL-8.a

#### <a name="information-security-architecture"></a>情報セキュリティ アーキテクチャ

**PL-8.a** 以下を満たす、情報システムの情報セキュリティアーキテクチャを策定する：
1. 組織の情報の機密性、完全性、および可用性を保護するのに使用できる全般的な方
針、要求事項、およびアプローチについて記述する
2. 情報セキュリティアーキテクチャがどのようにしてエンタープライズアーキテクチャに組
み入れられるか、また、どのようにしてエンタープライズアーキテクチャを支援するかに
ついて記述する
3. 外部サービスの情報セキュリティに関する想定と、それらのサービスへの依存につい
て記述する

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、お客様がデプロイしたリソースの情報セキュリティアーキテクチャを策定する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 Control PL-8.b

#### <a name="information-security-architecture"></a>情報セキュリティ アーキテクチャ

**PL-8.b** エンタープライズアーキテクチャに対する更新を反映するために、情報セキュリティアーキ
テクチャを [指定: 組織が定めた頻度で] レビューし、更新する

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、情報セキュリティアーキテクチャをレビューし、更新する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 Control PL-8.c

#### <a name="information-security-architecture"></a>情報セキュリティアーキテクチャ

**PL-8.c** 予定されている、情報セキュリティアーキテクチャに対する変更が、セキュリティ計画、セキ
ュリティの観点からの運用概念 (CONOPS)、および組織の資材調達／調達に反映されるようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、情報セキュリティ アーキテクチャの予定されている変更を考慮する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |
