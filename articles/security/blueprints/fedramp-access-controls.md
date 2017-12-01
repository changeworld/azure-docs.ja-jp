---
title: "FedRAMP Azure Blueprint Automation - アクセスの制御"
description: "FedRAMP 用の Web アプリケーション - アクセスの制御"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>アクセスの制御 (AC)

> [!NOTE]
> これらの制御は、NIST および米国商務省により、NIST Special Publication 800-53 Revision 4 の一部として定義されています。 各コントロールのテスト手順とガイダンスについては、NIST 800-53 Rev. 4 を参照してください。

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 Control AC-1

#### <a name="access-control-policy-and-procedures"></a>アクセスの制御ポリシーと手順

**AC-1**: 組織は、目的、スコープ、役割、責任、経営陣のコミットメント、組織エンティティ間の調整、コンプライアンスを取り上げたアクセスの制御のポリシーと、アクセスの制御のポリシーおよびリスク評価の関連するコントロールの実装を容易にする手順を策定、文書化し、[割り当て: 組織が定めた担当者または役割] に周知徹底します。また、アクセスの制御の現在のポリシーを [割り当て: 組織が定めた頻度] で、アクセスの制御の手順を [割り当て: 組織が定めた頻度] で見直し、更新します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御のポリシーと手順は、このコントロールに対処するために十分であると考えられます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 Control AC-2.a

#### <a name="account-management"></a>アカウント管理

**AC-2.a** 組織は、組織の使命/ビジネス機能を支援する [割り当て: 組織が定義した情報システム アカウントの種類] の情報システム アカウントを特定し、選択します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Active Directory ユーザー (ソリューションのデプロイと Azure リソースへのアクセスの管理)、Windows OS ユーザー (Active Directory による管理)、SQL Server サービス アカウントというシステム アカウントの種類に依存し、実装しています。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 Control AC-2.b

#### <a name="account-management"></a>アカウント管理

**AC-2.b** 組織は、情報システム アカウント用のアカウント マネージャーを割り当てます。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、AC-02.a に指定されているアカウントにマネージャーを割り当てる責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 Control AC-2.c

#### <a name="account-management"></a>アカウント管理

**AC-2.c** 組織は、グループとロール メンバーシップの条件を確立します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様が制御するアカウントの種類について、ロールとグループ メンバーシップの基準を確立する責任があります (AC-02.a を参照してください)。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 Control AC-2.d

#### <a name="account-management"></a>アカウント管理

**AC-2.d** 組織は、情報システムの承認されたユーザー、グループ、ロール メンバーシップ、各アカウントのアクセスの承認 (つまり特権) と他の属性 (必要に応じて) を指定します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、確立したエンタープライズレベルのアカウント承認プロセスを使用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 Control AC-2.e

#### <a name="account-management"></a>アカウント管理

**AC-2.e** 組織は、情報システム アカウントを作成する要求について、[割り当て: 組織が定義した個人またはロール] による承認が必要です。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、確立したエンタープライズレベルのアカウント承認プロセスを使用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 Control AC-2.f

#### <a name="account-management"></a>アカウント管理

**AC-2.f** 組織は、[割り当て: 組織が定義した手順または条件] に従って、情報システム アカウントの作成、有効化、変更、無効化、削除を行います。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、確立したエンタープライズレベルのアカウント管理プロセスを使用できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 Control AC-2.g

#### <a name="account-management"></a>アカウント管理

**AC-2.g** 組織は、情報システム アカウントの使用を監視します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS Security and Audit ソリューションの ID とアクセスのダッシュボードを実装します。 このダッシュボードを使用すると、アカウント マネージャーは情報システム アカウントの使用を監視できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 Control AC-2.h

#### <a name="account-management"></a>アカウント管理

**AC-2.h** 組織は、アカウントが不要になったとき、ユーザーが終了または転送されるとき、および個々の情報システムの使用または知る必要がある変更があったときに、アカウント マネージャーに通知します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順は、アカウントが不要になったときに適切なアカウント マネージャーに通知するプロセスを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 Control AC-2.i

#### <a name="account-management"></a>アカウント管理

**AC-2.i** 組織は、組織または関連する使命/ビジネス機能の必要に応じて、有効なアクセス承認、目的のシステム使用方法、その他の属性に基づいて、情報システムへのアクセスを承認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、アクセス承認プロセスを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 Control AC-2.j

#### <a name="account-management"></a>アカウント管理

**AC-2.j** 組織は、アカウント管理の要件の [割り当て: 組織が定義する頻度] にアカウントが準拠していることを確認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様が制御するアカウントを確認し、アカウントがすべての組織要件に準拠しているかどうかを判断する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 Control AC-2.k

#### <a name="account-management"></a>アカウント管理

**AC-2.k** 組織は、個人をグループから削除するときに、共有/グループ アカウントの資格情報 (デプロイする場合) を再発行するプロセスを確立します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、グループ アカウントの資格情報を管理するためのプロセスを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 Control AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>アカウント管理 | 自動システム アカウント管理

**AC-2 (1)** 組織は、情報システム アカウントの管理を支援する自動メカニズムを採用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS Security and Audit ソリューションの ID とアクセスのダッシュボードを実装します。 このダッシュボードを使用すると、アカウント マネージャーは情報システム アカウントの使用を監視できます。 特殊な活動が疑われる場合、または他の定義済みイベントが発生した場合にアラートを送信するように OMS を構成することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 Control AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>アカウント管理 | 一時的/緊急アカウントの削除

**AC-2 (2)** [割り当て: アカウントの種類ごとに組織が定義した期間] の後に、情報システムで、一時的および緊急アカウントの [選択: 削除、無効化] を自動的に行います。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、一時的または緊急アカウントをデプロイしません。 手動で無効化しない場合、35 日後に、すべての非アクティブなアカウントはデプロイされているドメイン コントローラーによって自動的に無効化されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 Control AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>アカウント管理 | 非アクティブなアカウントの無効化

**AC-2 (3)** 情報システムは、[割り当て: 組織が定義した期間] 後に、非アクティブなアカウントを自動的に無効化します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされるドメイン コントローラーは、非アクティブな期間が 35 日経過したすべてのユーザー アカウントを無効化するように構成されています。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 Control AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>アカウント管理 | 自動監査アクション

**AC-2 (4)** 情報システムは、アカウントの作成、変更、有効化、無効化、削除のアクションを自動的に監査し、[割り当て: 組織が定義した個人またはロール] に通知します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Active Directory ユーザー、Windows OS ユーザー、SQL Server サービス アカウントというシステム アカウントの種類を実装しています。 Azure Active Directory アカウント管理アクションで、Azure アクティビティ ログにイベントが生成されます。また、OS レベルのアカウント管理アクションで、システム ログにイベントが生成されます。 これらのログは Log Analytics によって収集され、OMS リポジトリに格納されます。 定義済みのイベントが発生したときにアラートを送信するように OMS を構成できます。  |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 Control AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>アカウント管理 | 非アクティブ ログアウト

**AC-2 (5)** 組織は、[割り当て: 想定される非アクティブまたはログアウトするときの説明について組織が定義した期間] でユーザーがログアウトすることを必須にしています。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、一定期間 (または他の要因で)、ユーザーが非アクティブになると予想されるときに、ユーザーがログアウトするポリシーを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 Control AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>アカウント管理 | ロールベースのスキーム

**AC-2 (7).a** 組織は、許可される情報システム アカウントと特権をロールに編成するロールベースのアクセス スキームに従って、特権を持つユーザー アカウントを確立して管理します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Active Directory ユーザー、Windows OS ユーザー、SQL Server サービス アカウントというシステム アカウントの種類を実装しています。 Azure Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをロールに割り当てることで実装されます。Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをセキュリティ グループに割り当てることで実装されます。 これらのロールベースのスキームは、使命のニーズを満たすようにお客様が拡張できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 Control AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>アカウント管理 | ロールベースのスキーム

**AC-2 (7).b** 組織は、特権を持つロールの割り当てを監視します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS Security and Audit ソリューションの ID とアクセスのダッシュボードを実装します。 このダッシュボードを使用すると、アカウント マネージャーは情報システム アカウントの使用を監視できます。 このソリューションに対して、特権を持つロールの割り当てを報告するクエリを実行することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 Control AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>アカウント管理 | ロールベースのスキーム

**AC-2 (7).c** 組織は、特権を持つロールの割り当てが不適切になったときに、[割り当て: 組織が定義するアクション] を実行します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、特権を持つロールの割り当てが不適切になったときに、お客様が制御するアカウントに対してアクションを実行する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 Control AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>アカウント管理 | 共有/グループ アカウントの使用に対する制限

**AC-2 (9)** 組織は、[割り当て: 組織が定義する共有/グループ アカウントを確立するための条件] を満たす共有/グループ アカウントの使用のみを許可します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされるリソースで有効になる共有/グループ アカウントはありません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 Control AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>アカウント管理 | 共有/グループ アカウントの資格情報の終了

**AC-2 (10)** 情報システムは、メンバーがグループを離れたときに共有/グループ アカウントの資格情報を終了します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされるリソースで有効になる共有/グループ アカウントはありません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 Control AC-2 (11)

#### <a name="account-management--usage-conditions"></a>アカウント管理 | 使用条件

**AC-2 (11)** 情報システムは、[割り当て: 組織が定義する情報システム アカウント] に対して [割り当て: 組織が定義する状況および使用条件] を適用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 Active Directory でグループ ポリシーを確立し、時刻の制限または他のアカウント使用条件を実装するように構成することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 Control AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>アカウント管理 | アカウントの監視/特殊な使用方法

**AC-2 (12).a** 組織は、情報システム アカウントの [割り当て: 組織が定義する特殊な使用方法] を監視します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS Security and Audit ソリューションの ID とアクセスのダッシュボードを実装します。 アカウント マネージャーはこのダッシュボードを使用して、デプロイされているリソースに対するアクセス試行を監視できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 Control AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>アカウント管理 | アカウントの監視/特殊な使用方法

**AC-2 (12).b** 組織は、情報システム アカウントの特殊な使用方法を [割り当て: 組織が定義した個人またはロール] に報告します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS Security and Audit ソリューションの ID とアクセスのダッシュボードを実装します。 アカウント マネージャーはこのダッシュボードを使用して、デプロイされているリソースに対するアクセス試行を監視できます。 特殊な活動が疑われる場合、または他の定義済みイベントが発生した場合にアラートを送信するようにこのソリューションを構成することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 Control AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>アカウント管理 | 危険度の高い個人のアカウントを無効にする

**AC-2 (13)** 組織は、リスクの検出の [割り当て: 組織が定義する期間 ] 内に重大なリスクをもたらすユーザーのアカウントを無効にします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーと手順で、組織に重大なリスクをもたらすユーザーのアカウントを無効にする条件を確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 Control AC-3

#### <a name="access-enforcement"></a>アクセスの適用

**AC-3** 情報システムは、適用可能なアクセスの制御ポリシーに従い、情報およびシステム リソースに対する論理アクセスに対して承認済みのアクセス権限を適用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Active Directory によるユーザーへのロール割り当て、Active Directory によるユーザーのセキュリティ グループへの割り当て、および Windows OS レベルの制御で適用されるロールベースのアクセス制御を使用して、論理アクセスの承認を適用します。 ユーザーまたはグループに割り当てられる Azure Active Directory ロールで、リソース、グループ、またはサブスクリプション レベルで Azure 内のリソースに対する論理アクセスを制御します。 Active Directory セキュリティ グループは、OS レベルのリソースと機能に対する論理アクセスを制御します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 Control AC-4

#### <a name="information-flow-enforcement"></a>情報フローの適用

**AC-4** 情報システムは、[割り当て: 組織が定義する情報フロー制御ポリシー] に基づいてシステム内および相互接続されているシステム間の情報フローを制御するために、承認済みのアクセス権限を適用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、リソースがデプロイされるサブネット、Application Gateway、ロード バランサーに適用されるネットワーク セキュリティ グループを使用して、情報フローの制限を適用します。 ネットワーク セキュリティ グループによって、リソース間の情報フローは承認済みのルールに基づいて制御されます。 Application Gateway とロード バランサーは、承認のロールに基づいてトラフィックを特定のリソースに動的にルーティングします。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 Control AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>情報フローの適用 | セキュリティ ポリシー フィルター

**AC-4 (8)** 情報システムは、[割り当て: 組織が定義する情報フロー] のフロー制御を判断する基礎として [割り当て: 組織が定義するセキュリティ ポリシー フィルター] を使用して、情報フロー制御を適用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様がデプロイしたリソース内に情報フロー制御を適用する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 Control AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>情報フローの適用 | 情報フローの物理的/論理的な分離

**AC-4 (21)** 情報システムは、[割り当て: 組織が定義したメカニズムや手法] を使用して情報フローを論理的または物理的に分離し、[割り当て: 組織が定義した、情報の種類別の必須の定義] を達成します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様がデプロイしたリソース内の情報フローを分離する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 Control AC-5.a

#### <a name="separation-of-duties"></a>職務の分離

**AC-5.a** 組織は、[割り当て: 組織が定義した個人の職務] を分離します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様が制御したアカウント全体の職務を分離する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 Control AC-5.b

#### <a name="separation-of-duties"></a>職務の分離

**AC-5.b** 組織は、個人の職務の分離について文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様が制御したアカウント全体の職務の分離を文書化する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 Control AC-5.c

#### <a name="separation-of-duties"></a>職務の分離

**AC-5.c** 組織は、職務の分離に対応するために情報システム アクセスの承認を定義します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、組織の要件に従って職務を分離するように構成することができるロールベースのアクセス制御を実装します。 Azure Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをロールに割り当てることで実装されます。Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをセキュリティ グループに割り当てることで実装されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 Control AC-6

#### <a name="least-privilege"></a>最小限の特権

**AC-6** 組織は、最小限の特権の原則を採用し、組織の使命とビジネス機能に従って、ユーザー (または、ユーザーの代理で動作するプロセス) に対して、割り当てられたタスクの達成に必要な承認済みのアクセス権限のみを許可します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、ロールベースのアクセス制御を実装して、明示的に割り当てられている特権のみにユーザーを制限します。 Azure Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをロールに割り当てることで実装されます。Active Directory アカウントの特権は、ロールベースのアクセス制御を使用し、ユーザーをセキュリティ グループに割り当てることで実装されます。  |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 Control AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>最小限の特権 | セキュリティ機能へのアクセスの承認

**AC-6 (1)** 組織は、[割り当て: 組織が定義したセキュリティ機能 (ハードウェア、ソフトウェア、およびファームウェアにデプロイされている機能) とセキュリティ関連の情報] へのアクセスを明示的に承認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、セキュリティ機能へのアクセスを含むアクセス承認プロセスを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 Control AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>最小限の特権 | セキュリティ以外の機能に対する特権なしのアクセス

**AC-6 (2)** 組織は、[割り当て: 組織が定義したセキュリティ機能またはセキュリティ関連の情報] へのアクセス権を持つ情報システム アカウントのユーザーまたはロールが、セキュリティ以外の機能にアクセスするときは、特権を持たないアカウントを使用することを要求します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、ユーザーがセキュリティ以外の機能にアクセスするときに特権を持たないアカウントを使用するように要件にすることができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 Control AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>最小限の特権 | 特権のあるコマンドに対するネットワーク アクセス

**AC-6 (3)** 組織は、[割り当て: 組織が定義したやむを得ない運用上のニーズ] がある場合にのみ、[割り当て: 組織が定義した特権のあるコマンド] に対するネットワーク アクセスを承認し、情報システムのセキュリティ計画にこのようなアクセスの論拠について文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、ネットワーク経由でアクセス可能な特権のあるコマンドを定義できます。 注: お客様には、Azure インフラストラクチャへの物理的なアクセスがありません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 Control AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>最小限の特権 | 特権のあるアカウント

**AC-6 (5)** 組織は、情報システム上の特権のあるアカウントを [割り当て: 組織が定義した個人またはロール] に制限します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、特権のあるアカウントの使用について制限を定義することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 Control AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>最小限の特権 | ユーザー特権の確認

**AC-6 (7).a** 組織は、[割り当て: 組織が定義した頻度] で、[割り当て: 組織が定義したユーザーのロールまたはクラス] に割り当てられている特権を確認し、このような特権のニーズを検証します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様が制御するアカウントのユーザー特権を確認する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 Control AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>最小限の特権 | ユーザー特権の確認

**AC-6 (7).b** 組織は、組織の使命/ビジネス ニーズを正しく反映するように、必要に応じて特権の再割り当てまたは削除を行います。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、必要に応じてお客様が制御するアカウントの再割り当てまたは削除を行う責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 Control AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>最小限の特権 | コード実行の特権レベル

**AC-6 (8)** 情報システムでは、ソフトウェアを実行するユーザーよりも高い特権レベルで [割り当て: 組織が定義したソフトウェア] を実行できません。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、ロールベースのアクセス制御を実装して、明示的に割り当てられている特権のみにユーザーを制限します。 仮想マシンの OS レベル保護では、ソフトウェアを実行するユーザーよりも高い特権レベルでソフトウェアを実行できません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 Control AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>最小限の特権 | 特権のある機能の使用の監査

**AC-6 (9)** 情報システムは、特権のある機能の実行を監査します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、OMS に Log Analytics サービスを実装しています。 デプロイされている VM と Azure 診断ストレージ アカウントは、特権のある機能の実行が監査されるように、Log Analytics に接続されているソースです。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 Control AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>最小限の特権 | 特権のないユーザーに対する特権のある機能の実行の禁止

**AC-6 (10)** 情報システムは、実装されているセキュリティ保護策/対策の無効化、回避、または改ざんを含む特権のある機能を、特権のないユーザーが実行することを禁止しています。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、ロールベースのアクセス制御を実装して、明示的に割り当てられている特権のみにユーザーを制限します。  |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 Control AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>ログオン試行の失敗

**AC-7.a** 情報システムは、[割り当て: 組織が定義した期間] に、ユーザーによる [割り当て: 組織が定義した回数] の連続する無効なログオン試行の制限を適用します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Portal は、ユーザーによる連続する無効なログオン試行を制限します。 この Azure Blueprint でデプロイされるすべての仮想マシンについて、オペレーティング システム レベルでグループ ポリシーが適用されます。 このポリシーでは、ユーザーによる無効なログオン試行が 15 分以内に 3 回を超えないように制限しています。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 Control AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>ログオン試行の失敗

**AC-7.b** 試行の失敗回数の上限を超えると、情報システムは、[選択: [割り当て: 組織が定義した期間] アカウント/ノードをロックする、管理者が解放するまでアカウント/ノードをロックする、[割り当て: 組織が定義した遅延アルゴリズム] に従って次回のログオン プロンプトを遅延させる] 処理を自動実行します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | Azure Portal は、ユーザーによる連続する無効なログオン試行後にアカウントをロックします。 この Azure Blueprint でデプロイされるすべての仮想マシンについて、オペレーティング システム レベルでグループ ポリシーが適用されます。 このポリシーでは、ユーザーによる無効なログオン試行が 3 回連続した後、アカウントを 3 時間ロックします。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 Control AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>ログオン試行の失敗 | モバイル デバイスの消去またはワイプ

**AC-7 (2)** 情報システムは、デバイス ログオン試行の失敗が [割り当て: 組織が定義した回数] 連続した後に、[割り当て: 組織が定義した消去/ワイプの要件/手法] に基づいて、[割り当て: 組織が定義したモバイル デバイス] から情報を消去/ワイプします。

**責任:** `Not Applicable`

|||
|---|---|
| **お客様** | モバイル デバイスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure では、Azure 境界内でモバイル デバイスを許可していません。 そのため、このコントロールは Microsoft Azure には適用されません。 |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 Control AC-8.a

#### <a name="system-use-notification"></a>システム使用通知

**AC-8.a** 情報システムは、該当する連邦法、大統領令、指令、ポリシー、規制、標準、ガイダンスと矛盾しないプライバシーおよびセキュリティ通知を提供するシステムに対してアクセス権を付与する前に、ユーザーに [割り当て: 組織が定義したシステム使用通知メッセージまたはバナー] を表示します。また、ユーザーが米国政府の情報システムにアクセスしていることを示します。情報システムの使用状況は、監視および記録され、監査の対象になる可能性があります。情報システムの不正使用は禁止されており、刑事罰および民事罰の対象になります。また、この情報システムの使用は、監視および記録に同意していることを示します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 グループ ポリシーは、ログイン前にユーザーに表示されるシステム使用通知を実装しています。 注: Azure Blueprint は、サンプル システムの使用通知を実装しています。 お客様は、組織または規制機関の要件に合わせてこの文章を編集する必要があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 Control AC-8.b

#### <a name="system-use-notification"></a>システム使用通知

**AC-8.b** 情報システムは、ユーザーが使用条件に同意し、情報システムにログオンまたはさらにアクセスするための明示的なアクションを実行するまで、通知メッセージまたはバナーを画面に表示し続けます。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 グループ ポリシーは、ログオン前にユーザーに表示されるシステム使用通知を実装しています。 ユーザーは、ログインするために通知に同意する必要があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 Control AC-8.c

#### <a name="system-use-notification"></a>システム使用通知

**AC-8.c** パブリックにアクセスできるシステムの情報システムは、さらにアクセス権を付与する前に、システム使用情報の [割り当て: 組織が定義した条件] を表示します。監視、記録、または監査が一般的に禁止されているシステムの場合、プライバシーが保護される施設と矛盾しない監視、記録、または監査の関連文書 (ある場合) を表示します。また、システムの承認された使用方法の説明を含めます。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様がデプロイするすべてのパブリックにアクセス可能なリソースでは、システム使用通知を表示する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 Control AC-10

#### <a name="concurrent-session-control"></a>同時セッション制御

**AC-10** 情報システムは、[割り当て: 組織が定義したアカウントやアカウントの種類] ごとの同時セッション数を、[割り当て: 組織が定義した数] に制限します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされる仮想マシン用にオペレーティング システム ポリシーが実装されています。 このポリシーは、同時セッションの制限 (2 セッション) を実装します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 Control AC-11.a

#### <a name="session-lock"></a>セッションのロック

**AC-11.a** 情報システムは、非アクティブな状態が [割り当て: 組織が定義した期間] 続いた後、またはユーザーから要求を受信したときに、セッションのロックを開始することでシステムに対する以降のアクセスを防ぎます。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 グループ ポリシーは、RDP セッションに対して非アクティブ ロックを実装しています。 ユーザーはロックを手動で開始できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 Control AC-11.b

#### <a name="session-lock"></a>セッションのロック

**AC-11.b** 情報システムは、ユーザーが確立済みの ID および認証手順を使用してアクセスを再確立するまで、セッションのロックを継続します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 グループ ポリシーは、RDP セッションに対して非アクティブ ロックを実装しています。 セッションのロックを解除するには、ユーザーが再認証を受ける必要があります。  |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 Control AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>セッションのロック | パターン非表示ディスプレイ

**AC-11 (1)** 情報システムは、セッションのロックを使用して、以前はディスプレイに表示されていた情報 (パブリックに閲覧可能な画像を含む) を非表示にします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、デプロイされるすべての仮想マシンを登録するドメイン コントローラーをデプロイします。 グループ ポリシーは、RDP セッションに対して非アクティブ ロックを実装しています。 セッションのロックによって、以前は表示されていた情報が非表示になります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 Control AC-12

#### <a name="session-termination"></a>セッションの終了

**AC-12** 情報システムは、[割り当て: 組織が定義した、セッションの切断を必要とする条件またはトリガー イベント] の発生後に、ユーザー セッションを自動的に終了します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされる Windows 仮想マシンのリモート デスクトップ セッション ホスト構成は、組織のセッション終了要件に合わせて構成できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 Control AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>セッションの終了 | ユーザーが開始したログアウト/メッセージの表示

**AC-12 (1).a** 情報システムには、[割り当て: 組織が定義した情報リソース] へのアクセス権を得るために認証が使用されるときに毎回実行される、ユーザーが開始した通信セッションのログアウト機能があります。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされる Azure Portal と仮想マシンのオペレーティング システムでは、ユーザーがログアウトを開始できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 Control AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>セッションの終了 | ユーザーが開始したログアウト/メッセージの表示

**AC-12 (1).b** 情報システムは、認証済みの通信セッションの信頼できる終了を示す明示的なログアウト メッセージをユーザーに表示します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされる Azure Portal と仮想マシンのオペレーティング システムでは、ユーザーがログアウトを開始できます。 このログアウト プロセスでは、セッションが終了したことがユーザーに示されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 Control AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>ID または認証なしで許可されるアクション

**AC-14.a** 組織は、組織の使命/ビジネス機能と矛盾しない、ID または認証なしで実行できる情報システムに対する [割り当て: 組織が定義したユーザー アクション] を指定します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様がデプロイしたリソースに対して、ID または認証なしで実行できるアクション (パブリックでアクセスできる Web ページの閲覧など) を指定する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 Control AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>ID または認証なしで許可されるアクション

**AC-14.b** 組織は、情報システム、ID または認証を必要としないユーザー アクションについて、情報システムのセキュリティ計画で文書化し、それを補助する論拠を提供します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様には、お客様がデプロイしたリソースに対して ID または認証を必要としないユーザー アクションについて文書を提供する責任があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 Control AC-17.a

#### <a name="remote-access"></a>リモート アクセス

**AC-17.a** 組織は、許可される各種のリモート アクセスについて、使用方法の制限、構成/接続要件、実装ガイダンスを規定し、文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、リモート アクセスの使用方法の制限を定義することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 Control AC-17.b

#### <a name="remote-access"></a>リモート アクセス

**AC-17.b** 組織は、このような接続を許可する前に、情報システムへのリモート アクセスを承認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、リモート アクセス承認プロセスを確立することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 Control AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>リモート アクセス | 自動監視/制御

**AC-17 (1)** 情報システムは、リモート アクセス方法を監視および制御します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Portal、踏み台を経由するリモート デスクトップ接続、お客様が実装した Web アプリケーションを介して、情報システムへのリモート アクセスを提供します。 Azure Portal とリモート デスクトップ セッションを介したアクセスは監査されます。また、OMS で監視できます。 お客様は、必要に応じて Web アプリケーションにリモート アクセス制御を実装します。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 Control AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>リモート アクセス | 暗号化を使用した機密性/整合性の保護

**AC-17 (2)** 情報システムは、リモート アクセス セッションの機密性と整合性を保護する暗号化メカニズムを実装します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint によってデプロイされるリソース (Azure Portal、リモート デスクトップ接続、Web アプリケーション ゲートウェイなど) へのリモートアクセスは、TLS を使用して保護されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 Control AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>リモート アクセス | 管理対象のアクセス制御ポイント

**AC-17 (3)** 情報システムは、すべてのリモート アクセスを [割り当て: 組織が定義した数] の管理対象ネットワーク アクセス制御ポイントを介してルーティングします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint でデプロイされる概念的な Web アプリケーションへのリモート アクセスは、アプリケーション ゲートウェイを介して行われます。 その他すべてのリソースに対するリモート アクセスは、踏み台を介して行われます。 パブリックにアクセスできるエンドポイントは他にありません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 Control AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>リモート アクセス | 特権のあるコマンド/アクセス

**AC-17 (4).a** 組織は、[割り当て: 組織が定義したニーズ] がある場合にのみ、リモート アクセスを介するセキュリティに関連する情報に対する特権のあるコマンドとアクセスの実行を承認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、リモートでアクセスできる特権のあるコマンドを定義し、論拠を含めることができます。 注: お客様は、Azure インフラストラクチャに直接アクセスすることはできません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 Control AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>リモート アクセス | 特権のあるコマンド/アクセス

**AC-17 (4).b** 組織は、情報システムのセキュリティ計画で、このようなアクセスの論拠を文書化します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーで、リモートでアクセスできる特権のあるコマンドを定義し、論拠を含めることができます。 注: お客様は、Azure インフラストラクチャに直接アクセスすることはできません。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 Control AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>リモート アクセス | アクセスの切断/無効化

**AC-17 (9)** 組織は、[割り当て: 組織が定義した期間] 内に情報システムに対するリモート アクセスを迅速に切断または無効化する機能を提供します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | この Azure Blueprint は、Azure Portal、踏み台を経由するリモート デスクトップ接続、Web アプリケーションを介して、情報システムへのリモート アクセスを提供します。 Azure Active Directory アカウントが無効化または削除されると、Azure Portal アクセスは即時に切断されます。 同様に、仮想マシンの OS レベル アカウントが無効化または削除されると、踏み台経由のリモート デスクトップ アクセスは即時に切断されます。 お客様は、Web アプリケーションのリモート アクセスの切断を実装する必要があります。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 Control AC-18.a

#### <a name="wireless-access"></a>ワイヤレス アクセス

**AC-18.a** 組織は、ワイヤレス アクセスについて、使用方法の制限、構成/接続要件、実装ガイダンスを規定します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure 環境でワイヤレスの使用を明示的に禁止するネットワーク セキュリティ標準を介したワイヤレス アクセスについて、使用方法の制限、構成/接続要件、実装ガイダンスを規定します。 |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 Control AC-18.b

#### <a name="wireless-access"></a>ワイヤレス アクセス

**AC-18.b** 組織は、このような接続を許可する前に、情報システムへのワイヤレス アクセスを承認します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure データセンター内のワイヤレス アクセスを許可していません。 |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 Control AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>ワイヤレス アクセス | 認証と暗号化

**AC-18 (1)** 情報システムは、[選択 (1 つ以上): ユーザー、デバイス] の認証および暗号化を使用して、システムに対するワイヤレス アクセスを保護しています。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure 環境内のワイヤレス アクセスを許可していません。 |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 Control AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>ワイヤレス アクセス | ワイヤレス ネットワーキングの無効化

**AC-18 (3)** 組織は、発行およびデプロイ前に情報システム コンポーネント内に組み込まれているワイヤレス ネットワーキング機能の使用が想定されていない場合、その機能を無効化します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure 環境内のワイヤレス アクセスを許可していません。 |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 Control AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>ワイヤレス アクセス | ユーザーによる構成の制限

**AC-18 (4)** 組織は、ワイヤレス ネットワーキング機能を個別に構成することを許可するユーザーを指定し、明示的に承認します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure 環境内のワイヤレス アクセスを許可していません。 |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 Control AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>ワイヤレス アクセス | アンテナ/送電レベル

**AC-18 (5)** 組織は、無線アンテナを選択し、送電レベルを調整して、組織が制御する境界を外れる使用可能な信号を受信する可能性を軽減します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | ワイヤレス アクセスは、Azure にデプロイされているシステムの範囲に含まれません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure は、Microsoft Azure 環境内のワイヤレス アクセスを許可していません。 |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 Control AC-19.a

#### <a name="access-control-for-mobile-devices"></a>モバイル デバイスのアクセスの制御

**AC-19.a** 組織は、組織が制御するモバイル デバイスについて、使用方法の制限、構成要件、接続要件、実装ガイダンスを規定します。

**責任:** `Not Applicable`

|||
|---|---|
| **お客様** | Azure 上にデプロイされたシステムの範囲内に、顧客によって制御されたモバイル デバイスは存在しません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure では、Azure 境界内でモバイル デバイスを許可していません。 そのため、このコントロールは Microsoft Azure には適用されません。 |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 Control AC-19.b

#### <a name="access-control-for-mobile-devices"></a>モバイル デバイスのアクセスの制御

**AC-19.b** 組織は、組織の情報システムに対するモバイル デバイスの接続を承認します。

**責任:** `Not Applicable`

|||
|---|---|
| **お客様** | Azure 上にデプロイされたシステムの範囲内に、顧客によって制御されたモバイル デバイスは存在しません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure では、Azure 境界内でモバイル デバイスを許可していません。 そのため、このコントロールは Microsoft Azure には適用されません。 |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 Control AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>モバイル デバイスのアクセスの制御 | 完全なデバイス/コンテナーベースの暗号化

**AC-19 (5)** 組織は、[選択: 完全なデバイスの暗号化、コンテナーの暗号化] を採用して、[割り当て: 組織が定義するモバイル デバイス] 上の情報の機密性と整合性を保護します。

**責任:** `Not Applicable`

|||
|---|---|
| **お客様** | Azure 上にデプロイされたシステムの範囲内に、顧客によって制御されたモバイル デバイスは存在しません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft Azure では、Azure 境界内でモバイル デバイスを許可していません。 そのため、このコントロールは Microsoft Azure には適用されません。 |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 Control AC-20.a

#### <a name="use-of-external-information-systems"></a>外部情報システムの使用

**AC-20.a** 組織は、外部に所有、運用、保守する他の組織との間で確立した信頼関係と矛盾しない条項を規定し、権限を持つ個人が外部情報システムからこの情報システムにアクセスできるようにします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーには、FedRAMP に従うクラウド サービス プランの使用に関する条項を含めることができます。 Azure には、FedRAMP Joint Authorization Board (JAB) から Provisional Authorization To Operate(P-ATO) が付与されているため、政府機関による Azure Cloud Services の取得使用が可能です。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 Control AC-20.b

#### <a name="use-of-external-information-systems"></a>外部情報システムの使用

**AC-20.a** 組織は、外部に所有、運用、保守する他の組織との間で確立した信頼関係と矛盾しない条項を規定し、権限を持つ個人が外部情報システムを使用して、組織が制御する情報を処理、格納、または送信できるようにします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーには、FedRAMP に従うクラウド サービス プランの使用に関する条項を含めることができます。 Azure には、FedRAMP Joint Authorization Board (JAB) から Provisional Authorization To Operate(P-ATO) が付与されているため、政府機関による Azure Cloud Services の取得使用が可能です。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 Control AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>外部情報システムの使用 | 権限を持つユーザーの使用の制限

**AC-20 (1)** 組織は、組織の情報セキュリティ ポリシーおよびセキュリティ計画の規定に従って、組織が外部システム上に必要なセキュリティ制御が実装されていることを確認した場合にのみ、権限を持つ個人が外部情報システムを使用してこの情報システムにアクセスすること、または組織が制御する情報を処理、格納、または送信することを許可します。または、外部情報システムをホストする組織のエンティティとの承認済み情報システムの接続または処理の同意を保持します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルの情報テクノロジ グループは、クラウド サービス プロバイダーが組織の情報セキュリティ要件に準拠していることを確認し、関連するクラウド サービス プランを使用するエンタープライズ全体の承認を付与することができます。 Azure には、FedRAMP Joint Authorization Board (JAB) から Provisional Authorization To Operate (P-ATO) が付与されています。 Azure は、FedRAMP セキュリティ管理策およびその他の要件へのコンプライアンスを検証する、FedRAMP 認可の第三者評価機関 (3PAO) によって評価されます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 Control AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>外部情報システムの使用 | ポータブル ストレージ デバイス

**AC-20 (2)** 組織は、外部情報システム上の権限を持つ個人が、組織の制御するポータブル ストレージ デバイスを使用することを [選択: 制限、禁止] します。

**責任:** `Azure Only`

|||
|---|---|
| **お客様** | お客様は、Azure データセンターのどのシステム リソースにも物理的なアクセスはできません。 |
| **プロバイダー (Microsoft Azure)** | Microsoft は、Microsoft Azure 環境内でお客様が制御するポータブル ストレージ デバイスを許可していません。 |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 Control AC-21.a

#### <a name="information-sharing"></a>情報共有

**AC-21.a** 組織は、共有パートナーに割り当てられた承認済みのアクセス権限が、[割り当て: 組織が定義した、ユーザーの判断が必要な情報共有条件] の情報に対するアクセス制限と一致するかどうかを、権限を持つユーザーが判断できるようにして、情報共有を容易にします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御ポリシーには、情報共有に関する条項を含めることができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 Control AC-21.b

#### <a name="information-sharing"></a>情報共有

**AC-21.b** 組織は、ユーザーが情報共有/コラボレーションの判断を下すときに役立つ [割り当て: 組織が定義した自動メカニズムまたは手動プロセス] を採用しています。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、エンタープライズレベルの情報共有に関する判断のサポート機能を利用することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 Control AC-22.a

#### <a name="publicly-accessible-content"></a>パブリックにアクセスできるコンテンツ

**AC-22.a** 組織は、パブリックにアクセスできる情報システムに情報を投稿する権限を持つ個人を指定します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、パブリックにアクセスできる情報を投稿する権限を持つ個人を指定できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 Control AC-22.b

#### <a name="publicly-accessible-content"></a>パブリックにアクセスできるコンテンツ

**AC-22.b** 組織は、パブリックにアクセスできる情報に非公開の情報を含まないように、権限を持つ個人をトレーニングします。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様は、パブリックにアクセスできる情報を投稿する権限を持つ個人に対して、エンタープライズレベルのトレーニングを利用することができます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 Control AC-22.c

#### <a name="publicly-accessible-content"></a>パブリックにアクセスできるコンテンツ

**AC-22.c** 組織は、非公開の情報が含まれないように、パブリックにアクセスできる情報システムに投稿する前に、提示された情報のコンテンツを確認します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、提示されたコンテンツをパブリックにアクセスできるシステムに投稿するためのレビュー プロセスを規定できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 Control AC-22.d

#### <a name="publicly-accessible-content"></a>パブリックにアクセスできるコンテンツ

**AC-22.d** 組織は、[割り当て: 組織が定義した頻度] でパブリックにアクセスできる情報システム上のコンテンツに非公開の情報が含まれていないかを確認し、そのような情報が検出された場合は削除します。

**責任:** `Customer Only`

|||
|---|---|
| **お客様** | お客様のエンタープライズレベルのアクセスの制御手順で、パブリックにアクセスできるシステムに投稿されたコンテンツの定期的なレビューのためのプロセスを規定できます。 |
| **プロバイダー (Microsoft Azure)** | 適用不可 |

