---
title: Azure のセキュリティとコンプライアンスのブループリント - FedRAMP Web アプリケーションの自動化 - 識別と認証
description: FedRAMP Web アプリケーションの自動化 - 識別と認証
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="identification-and-authentication-ia"></a>識別と認証 (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 コントロール IA-1

> [!NOTE]
> この管理策は、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順とガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

#### <a name="identification-and-authentication-policy-and-procedures"></a>識別と認証のポリシーと手順

**IA-1** 組織は、目的、範囲、役割、責任、経営陣のコミットメント、組織エンティティ間の調整、およびコンプライアンスを取り上げた識別と認証ポリシーと、識別と認証ポリシーおよび識別と認証の関連する管理策の実装を容易にする手順を策定、文書化し、[割り当て: 組織が定義した担当者または役割]に周知徹底する。また、現在の識別と認証ポリシーを[割り当て: 組織が定義した頻度]で、識別と認証の手順を[割り当て: 組織が定義した頻度]で見直し、更新する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客のエンタープライズ レベルの識別と認証のポリシーと手順で、この管理策に対処できます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 コントロール IA-2

#### <a name="identification-and-authentication-organizational-users"></a>識別と認証 (組織のユーザー)

**IA-2** 情報システムは、組織のユーザー (または、組織のユーザーに代わって行動する行為) を一意に識別し、認証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントで作成されたアカウントは、一意の識別子を持ちます。 一意でない識別子を持つ組み込みアカウントは、無効になるか削除されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 コントロール IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>識別と認証 (組織のユーザー) | 特権のあるアカウントへのネットワーク アクセス

**IA-2 (1)** 情報システムは、特権のあるアカウントへのネットワーク アクセスのための多要素認証を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、特権のあるアカウントへのネットワーク アクセスのための多要素認証を実装する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 コントロール IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>識別と認証 (組織のユーザー) | 特権のないアカウントへのネットワーク アクセス

**IA-2 (2)** 情報システムは、特権のないアカウントへのネットワーク アクセスのための多要素認証を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、特権のないアカウントへのネットワーク アクセスのための多要素認証を実装する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 コントロール IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>識別と認証 (組織のユーザー) | 特権のあるアカウントへのローカル アクセス

**IA-2 (3)** 情報システムは、特権のあるアカウントへのローカル アクセスのための多要素認証を実装する。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | 顧客には、Azure データセンターのどのシステム リソースへのローカル アクセス権もありません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、物理的なアクセスが必要な場合を除き、ローカル アクセスを許可しません。 ローカル管理者のアクセス権は、メンバー サーバーでネットワークの問題が発生していてドメイン認証が機能していない場合の問題のトラブルシューティングにのみ使用する必要があります。 <br /> Azure は、環境への物理的なアクセスに必要なアクセス制御メカニズムを使用して、ローカル アクセスのための多要素認証を実装します。 システムの境界内のすべての Azure インフラストラクチャ システムが含まれている Azure データセンター内の部屋は、会社のスマート カードのバッジによるアクセスと生体認証デバイスの要件を含め、各種の物理的なセキュリティ メカニズムによって制限されています。 データセンターのコロケーションへの入り口での物理的なアクセスには、両方の形式の認証が必要です。 |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 コントロール IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>識別と認証 (組織のユーザー) | 特権のないアカウントへのローカル アクセス

**IA-2 (4)** 情報システムは、特権のないアカウントへのローカル アクセスのための多要素認証を実装する。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | 顧客には、Azure データセンターのどのシステム リソースへのローカル アクセス権もありません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure Government の担当者が使用するすべての Microsoft Azure Government アカウントを特権ありと見なします。 すべての Microsoft Azure Government の担当者のアカウントに対してスマートカードと PIN を使用して多要素認証が実装され、これにはローカル アクセスが含まれます。 |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 コントロール IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>識別と認証 (組織のユーザー) | グループの認証

**IA-2 (5)** 組織は、グループ認証子が使用されている場合、個人に個々の認証子で認証を受けることを要求する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされるリソースに対して有効な共有/グループ アカウントはありません。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 コントロール IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>識別と認証 (組織のユーザー) | 特権のあるアカウントへのネットワーク アクセス - リプレイに対する抵抗力

**IA-2 (8)** 情報システムは、特権のあるアカウントへのネットワーク アクセスのための、リプレイに対する抵抗力のある認証メカニズムを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされるリソースへのアクセスは、Azure Active Directory、Active Directory、および Windows オペレーティング システムに組み込まれている Kerberos 機能によって、リプレイ攻撃から保護されています。 Kerberos 認証では、クライアントから送信された認証子に、暗号化された IP リスト、クライアントのタイムスタンプ、チケットの有効期間などの追加データが含まれています。 パケットが再生されると、タイムスタンプが確認されます。 タイムスタンプが以前の認証子より古い場合または以前の認証子と同じである場合、パケットは拒否されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 コントロール IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>識別と認証 (組織のユーザー) | 特権のないアカウントへのネットワーク アクセス - リプレイに対する抵抗力

**IA-2 (9)** 情報システムは、特権のないアカウントへのネットワーク アクセスのための、リプレイに対する抵抗力のある認証メカニズムを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされるリソースへのアクセスは、Azure Active Directory、Active Directory、および Windows オペレーティング システムに組み込まれている Kerberos 機能によって、リプレイ攻撃から保護されています。 Kerberos 認証では、クライアントから送信された認証子に、暗号化された IP リスト、クライアントのタイムスタンプ、チケットの有効期間などの追加データが含まれています。 パケットが再生されると、タイムスタンプが確認されます。 タイムスタンプが以前の認証子より古い場合または以前の認証子と同じである場合、パケットは拒否されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 コントロール IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>識別と認証 (組織のユーザー) | リモート アクセス - 別個のデバイス

**IA-2 (11)** 組織は、要素のいずれかがアクセスするシステムから切り離されたデバイスによって提供され、そのデバイスが[割り当て: 組織が定義したメカニズム要件の強度]を満たすといった、特権ありおよび特権なしのアカウントへのリモート アクセスのための多要素認証を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソースにリモートでアクセスするための多要素認証を実装する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 コントロール IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>識別と認証 (組織のユーザー) | PIV 資格情報の受け入れ

**IA-2 (12)** 情報システムは、個人アイデンティティの検証 (PIV) の資格情報を受け入れ、電子的に検証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、個人アイデンティティの検証 (PIV) の資格情報を受け入れ、検証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 コントロール IA-3

#### <a name="device-identification-and-authentication"></a>デバイスの識別と認証

**IA-3** 情報システムは、[選択項目 (1 つまたは複数): ローカル; リモート; ネットワーク]接続を確立する前に、[割り当て: 組織が定義した特定/タイプのデバイス]を一意に識別し、認証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、デバイスの識別と認証を実装する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 コントロール IA-4.a

#### <a name="identifier-management"></a>識別子の管理

**IA-4.a** 組織は、[割り当て: 組織が定義した職員または役割]から認証を受けて個人、グループ、役割、またはデバイス識別子を割り当てることによって、情報システムの識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客のリソース用の識別子 (個人、グループ、役割、デバイスなど) を管理する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 コントロール IA-4.b

#### <a name="identifier-management"></a>識別子の管理

**IA-4.b** 組織は、個人、グループ、役割、またはデバイスを識別する識別子を選択することによって、情報システムの識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、顧客が指定した個々のアカウントの識別子のデプロイ中にメッセージを表示します。  |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 コントロール IA-4.c

#### <a name="identifier-management"></a>識別子の管理

**IA-4.c** 組織は、対象となる個人、グループ、役割、またはデバイスに識別子を割り当てることによって、情報システムの識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客のリソース用の識別子 (個人、グループ、役割、デバイスなど) を管理する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 コントロール IA-4.d

#### <a name="identifier-management"></a>識別子の管理

**IA-4.d** 組織は、[割り当て: 組織が定義した期間]の間、識別子が再利用されないようにすることによって、情報システムの識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Active Directory とローカルの Windows オペレーティング システムのアカウントには、一意のセキュリティ識別子 (SID) が割り当てられます。 Azure Active Directory のアカウントには、グローバル一意オブジェクト ID が割り当てられます。 これらの一意な ID は再利用の対象外です。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 コントロール IA-4.e

#### <a name="identifier-management"></a>識別子の管理

**IA-4.e** 組織は、[割り当て: 組織が定義した未使用の期間]後に識別子を無効にすることによって、情報システムの識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、35 日間未使用の後で自動的にアカウントを無効にする、Active Directory のスケジュール タスクを実装します。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 コントロール IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>識別子の管理 | ユーザーの状態の識別

**IA-4 (4)** 組織は、各個人を[割り当て: 組織が定義した、個人の状態を識別する特徴]として一意に識別することによって、個々の識別子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Active Directory と Active Directory は、契約社員、ベンダー、およびその他のユーザー タイプに適用される名前付け規則を使用してそれらのタイプを示すことをサポートしています。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 コントロール IA-5.a

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.a** 組織は、初期認証子の配布の一環として認証子を受け取る個人、グループ、役割、またはデバイスを検証することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、認証子を管理する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 コントロール IA-5.b

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.b** 組織は、組織が定義した認証子に対する初期の認証子の内容を設定することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントで作成されたアカウントのすべての初期認証子の内容は、デプロイ中に顧客が指定したときに検証した、IA-5 (1) に記載の要件を満たします。  |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 コントロール IA-5.c

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.c** 組織は、認証子が意図した使用に対して十分なメカニズムの強度を持つようにすることによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントで使用される認証子は、FedRAMP で要求される強度の要件を満たします。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 コントロール IA-5.d

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.d** 組織は、初期認証子の配布、認証子の紛失/セキュリティ侵害、または破損、および認証子の失効の場合の管理手順を確立し、実装することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、認証子を管理する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 コントロール IA-5.e

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.e** 組織は、情報システムをインストールする前に認証子の既定の内容を変更することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントのコンポーネント用のすべての認証子は、その既定値から変更されています。 認証子はこのソリューションのデプロイ中に顧客が指定したものです。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 コントロール IA-5.f

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.f** 組織は、認証子の最小と最大の有効期間の制限および再利用条件を設定することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、認証子を管理する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 コントロール IA-5.g

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.g** 組織は、[割り当て: 組織が定義した認証子タイプ別の期間]認証子を変更/更新することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、デプロイされているすべての仮想マシンに接続するドメイン コントローラーをデプロイします。 グループ ポリシーは、パスワードの有効期間の制限 (60 日) を実装するように設定され、構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 コントロール IA-5.h

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.h** 組織は、認証子の内容を不正な漏洩や修正から保護することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、不正な漏洩や修正から認証子の内容を保護する Key Vault を実装します。 Key Vault には、アカウントをデプロイする際の Azure パスワード、仮想マシン管理者パスワード、SQL Server サービス アカウントのパスワードの各認証子が格納されます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 コントロール IA-5.i

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.i** 組織は、認証子を保護するための具体的なセキュリティ対策を取ることを個人に要求し、デバイスに実装することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、不正な漏洩や修正から認証子の内容を保護する Key Vault を実装します。 Key Vault には、アカウントをデプロイする際の Azure パスワード、仮想マシン管理者パスワード、SQL Server サービス アカウントのパスワードの各認証子が格納されます。 Key Vault は、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 コントロール IA-5.j

#### <a name="authenticator-management"></a>認証子の管理

**IA-5.j** 組織は、これらのアカウントに対するメンバーシップが変更されたときに、グループ/役割のアカウントの認証子を変更することによって、情報システムの認証子を管理する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされるリソースに対して有効な共有/グループ アカウントはありません。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 コントロール IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1).a** 情報システムは、パスワード ベースの認証の場合、[割り当て: 組織が定義した、タイプごとの最小要件を含む大文字小文字の区別、文字数、大文字、小文字、数字、および特殊文字の混在の要件]の最小限のパスワードの複雑さを適用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、デプロイされているすべての仮想マシンに接続するドメイン コントローラーをデプロイします。 グループ ポリシーは、仮想マシンのローカル アカウントと AD のアカウントにパスワードの複雑さの要件を適用するように設定され、構成されます。  |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 コントロール IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1) .b** 情報システムは、パスワード ベースの認証の場合、新しいパスワードを作成するときに少なくとも次の変更された文字数を適用する: [割り当て: 組織が定義した数]。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソース内でパスワード ベースの認証を使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 コントロール IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1).c** 情報システムは、パスワード ベースの認証の場合、暗号で保護されたパスワードのみを保管し、送信します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Active Directory を使用して、すべてのパスワードを保存中および転送中に暗号化によって保護することができます。 Active Directory によって、配置済みの Windows 仮想マシン上にローカルに格納されているパスワードは、組み込みのセキュリティ機能の一部として自動的にハッシュされます。 リモート デスクトップ認証セッションは、送信時に認証子が確実に保護されるように、TLS を使用して保護されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 コントロール IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1) .d** 情報システムは、パスワード ベースの認証の場合、[割り当て: 組織が定義した有効期間の最小値、有効期間の最大値]のパスワードの最小および最大有効期間の制限を適用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、デプロイされているすべての仮想マシンに接続するドメイン コントローラーをデプロイします。 グループ ポリシーは、ローカル アカウントと AD アカウントに最小 (1 日) および最大 (60 日) の有効期間の制限を適用するように設定され、構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 コントロール IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1).e** 情報システムは、パスワード ベースの認証の場合、[割り当て: 組織が定義した数] 世代のパスワードの再利用を禁止する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントは、デプロイされているすべての仮想マシンに接続するドメイン コントローラーをデプロイします。 グループ ポリシーは、ローカル アカウントと AD アカウントに再利用条件 (24 のパスワード) に関する制限を適用するよう設定され、構成されます。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 コントロール IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>認証子の管理 | パスワード ベースの認証

**IA-5 (1).f** 情報システムは、パスワード ベースの認証の場合、システムへのログオン用の一時パスワードを即座に恒久的なパスワードに変更して使用できるようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Active Directory を使用して、情報システムへの制御アクセスを管理します。 アカウントが最初に作成されたとき、または一時パスワードが生成されたときには、Azure Active Directory を使用して、次回ログイン時にユーザーにパスワードの変更を要求します。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 コントロール IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>認証子の管理 | PKI ベースの認証

**IA-5 (2).a** 情報システムは、PKI ベースの認証の場合、証明書の状態情報を含む受け入れ済みのトラスト アンカーへの証明書パスを構築し、検証することによって、証明書を検証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソース内で PKI ベースの認証を使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 コントロール IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>認証子の管理 | PKI ベースの認証

**IA-5 (2).b** 情報システムは、PKI ベースの認証の場合、対応する秘密キーに承認済みのアクセス権を適用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソース内で PKI ベースの認証を使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 コントロール IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>認証子の管理 | PKI ベースの認証

**IA-5 (2).c** 情報システムは、PKI ベースの認証の場合、認証済みの ID を個人のアカウントまたはグループにマップする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソース内で PKI ベースの認証を使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 コントロール IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>認証子の管理 | PKI ベースの認証

**IA-5 (2).d** 情報システムは、PKI ベースの認証の場合、ネットワーク経由で失効情報にアクセスできない場合のパスの検出と検証をサポートするために、失効データのローカル キャッシュを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソース内で PKI ベースの認証を使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 コントロール IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>認証子の管理 | 直接または信頼できるサードパーティによる登録

**IA-5 (3)** 組織は、[割り当て: 組織が定義したタイプ/特定の認証子]を受け取るための登録プロセスが、[割り当て: 組織が定義した職員または役割]が[割り当て: 組織が定義した登録機関]を承認する前に[選択項目: 本人によって直接; 信頼できるサードパーティによって]行われることを要求する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、認証子を登録する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 コントロール IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>認証子の管理 | パスワードの強度決定のための自動化されたサポート

**IA-5 (4)** 組織は、自動化されたツールを使用して、パスワードの認証子が[割り当て: 組織が定義した要件]を満たすのに十分な強度であるかどうかを確認する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされるユーザー アカウントには、AD アカウントとローカル ユーザー アカウントがあります。 これらは両方とも、初期パスワードを作成するために、およびパスワードの変更中に、設定済みのパスワード要件の順守を強制するメカニズムを提供します。 Azure Active Directory は、パスワード認証子が、IA-5 (1) に定められたパスワードの長さ、複雑さ、循環、および有効期間の制限を満たす十分な強度であるかどうかを確認するために使用される自動化されたツールです。 Azure Active Directory では、作成時のパスワード認証子の強度がこれらの基準を満たしていることを確認します。 パスワードの強度の要件を満たすために、このソリューションの展開に使用される顧客指定のパスワードがチェックされます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 コントロール IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>認証子の管理 | 認証子の保護

**IA-5 (6)** 組織は、認証子の使用がアクセスを許可する情報のセキュリティ カテゴリに応じて、認証子を保護する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、認証子を保護する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 コントロール IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>認証子の管理 | 暗号化されていない静的認証子の埋め込みなし

**IA-5 (7)** 組織は、暗号化されていない静的認証子がアプリケーションやアクセス スクリプトに埋め込まれたりファンクション キーに格納されたりしないようにする。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされたアプリケーション、アクセス スクリプト、またはファンクション キーに埋め込まれた暗号化されていない静的認証子は、使用されません。 認証子を使用するスクリプトやアプリケーションでは、使用前に毎回 Azure Key Vault コンテナーを呼び出します。 Azure Key Vault コンテナーへのアクセスは監査されるため、サービス アカウントを使用して Azure Key Vault コンテナーの対応する呼び出しなしにシステムにアクセスした場合に、この禁止の違反を検出できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 コントロール IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>認証子の管理 | 情報システムの複数のアカウント

**IA-5 (8)** 組織は、個人に複数の情報システムのアカウントを持たせることによるセキュリティ侵害のリスクを管理するために、[割り当て: 組織が定義したセキュリティ対策]を実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、エンタープライズ レベルのセキュリティ対策を利用して、個人に複数のシステムのアカウントを持たせることに関連するリスクを管理できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 コントロール IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>認証子の管理 | ハードウェア トークン ベースの認証

**IA-5 (11)** 情報システムは、ハードウェア トークン ベースの認証の場合、[割り当て: 組織が定義したトークン品質の要件]を満たすメカニズムを採用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、ハードウェア トークン ベースの認証の品質要件を満たすメカニズムを採用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 コントロール IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>認証子の管理 | キャッシュされた認証子の有効期限

**IA-5 (13)** 情報システムは、[割り当て: 組織が定義し期間]後のキャッシュされた認証子の使用を禁止する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされたリソースは、キャッシュされたトークンを使用できるようには構成されていません。 デプロイされた仮想マシンの認証には、認証時に認証子を入力することが必要です。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 コントロール IA-6

#### <a name="authenticator-feedback"></a>認証子のフィードバック

**IA-6** 情報システムは、可能性のある悪用/不正ユーザーによる使用から情報を保護するために、認証プロセス中は認証情報を隠す。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでデプロイされたリソースへのアクセスは、リモート デスクトップを経由し、Windows 認証に依存しています。 Windows 認証セッションの既定の動作は、認証セッション中の入力時にパスワードをマスクします。  |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 コントロール IA-7

#### <a name="cryptographic-module-authentication"></a>暗号化モジュールの認証

**IA-7** 情報システムは、該当の連邦法、行政命令、命令、ポリシー、規制、標準、およびこのようなガイダンスの要件を満たす暗号化モジュールの認証メカニズムを実装する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | このブループリントでは、Windows 認証、リモート デスクトップ、BitLocker が使用されます。 これらのコンポーネントは、FIPS 140 検証済み暗号化モジュールを利用するように構成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 コントロール IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>識別と認証 (組織外のユーザー)

**IA-8** 情報システムは、組織外のユーザー (または、組織外のユーザーに代わって行動する行為) を一意に識別し、認証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、顧客がデプロイしたリソースにアクセスする組織外のユーザーを識別し、認証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 コントロール IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>識別と認証 (組織外のユーザー) | 他の政府機関からの PIV 資格情報の受け入れ

**IA-8 (1)** 情報システムは、他の連邦機関の個人アイデンティティの検証 (PIV) の資格情報を受け入れ、電子的に検証する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、他の連邦機関が発行した個人アイデンティティの検証 (PIV) の資格情報を受け入れ、検証する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 コントロール IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>識別と認証 (組織外のユーザー) | サードパーティ資格情報の受け入れ

**IA-8 (2)** 情報システムは、FICAM 承認済みのサードパーティ資格情報のみを受け入れる。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、米国連邦政府 FICAM (Federal Identity, Credential, and Access Management) のトラスト フレームワーク ソリューションのイニシアチブで承認されているサードパーティ資格情報のみを受け入れる責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用外 |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 コントロール IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>識別と認証 (組織外のユーザー) | FICAM 承認済みの製品の使用

**IA-8 (3)** 組織は、サードパーティ資格情報を受け入れるために、[割り当て: 組織が定義した情報システム]の FICAM 承認済みの情報システム コンポーネントのみを使用する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、サードパーティ資格情報の受け入れに、米国連邦政府 FICAM (Federal Identity, Credential, and Access Management) のトラスト フレームワーク ソリューションのイニシアチブで承認されているリソースのみを使用する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 コントロール IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>識別と認証 (組織外のユーザー) | FICAM 発行のプロファイルの使用

**IA-8 (4)** 情報システムは、FICAM 発行のプロファイルに準拠する。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | 顧客は、米国連邦政府 FICAM (Federal Identity, Credential, and Access Management) のトラスト フレームワーク ソリューションのイニシアチブによって発行されたプロファイルに準拠する責任を負います。 |
| **プロバイダー (Microsoft Azure)** | 適用されません |
