---
title: Azure Payment Processing Blueprint - ID 要件
description: PCI DSS 要件 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>PCI DSS に準拠する環境の ID 要件 
## <a name="pci-dss-requirement-8"></a>PCI DSS 要件 8

**システム コンポーネントへのアクセスを識別して認証する**

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

アクセス権を持つ個人に一意の識別子 (ID) を割り当てて、各個人が各自のアクションに一意に責任を持つことを保証します。 このような責任を負わせることで、重要なデータとシステムで実行されるアクションが既知の承認されたユーザーとプロセスによって実行され、それらのアクションをトレースできます。
パスワードの有効性は、主に認証システムの設計と実装によって決まります。特に、攻撃者が実行できるパスワードの試行回数と、入力時、転送中、およびストレージでの格納中にパスワードを保護するためのセキュリティ方法に依存します。

> [!NOTE]
> これらの要件は、管理機能を持つ POS アカウント、カード所有者データを表示またはアクセスするために使用されるアカウント、カード所有者データがあるシステムにアクセスするために使用されるアカウントなどのすべてのアカウントに適用されます。 これには、ベンダーや他のサード パーティが (サポートやメンテナンスなどの目的で) 使用するアカウントが含まれます。 これらの要件は、コンシューマー (カード所有者など) が使用するアカウントには適用されません。 ただし、要件 8.1.1、8.2、8.5、8.2.3 ～ 8.2.5、および 8.1.6 ～ 8.1.8 は、単一のトランザクションを促進するために 1 度に 1 つのカード番号のみにアクセスできる POS 決済アプリケーション内のユーザー アカウント (キャッシャー アカウントなど) には適用される意図はありません。
 
## <a name="pci-dss-requirement-81"></a>PCI DSS 要件 8.1

**8.1** すべてのシステム コンポーネントのコンシューマー以外のユーザーと管理者の適切なユーザー ID 管理を保証するためのポリシーと手順を次のように定義して実装する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ユース ケースと、サンプル デプロイでの正しい管理者の使用法の説明を提供します。|



### <a name="pci-dss-requirement-811"></a>PCI DSS 要件 8.1.1

**8.1.1** システム コンポーネントまたはカード所有者データへのアクセスを許可する前に、すべてのユーザーに一意の ID を割り当てる。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure Active Directory を実装し、Azure Active Directory のロール ベースのアクセス制御 (RBAC) によってすべてのユーザーが一意の ID を持っていることを保証します。 詳細については、「[PCI ガイダンス - ID 管理](payment-processing-blueprint.md#identity-management)」を参照してください。<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS 要件 8.1.2

**8.1.2** ユーザー ID、資格情報、およびその他の ID オブジェクトの追加、削除、および変更を制御する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、Azure Active Directory を実装し、Azure Active Directory のロール ベースのアクセス制御 (RBAC) によってすべてのユーザーが一意の ID を持っていることを保証します。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS 要件 8.1.3

**8.1.3** 退職したユーザーのアクセス権をただちに取り消す。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ユーザー管理で Azure Active Directory を使用します。 Active Directory でユーザーの失効を実行できます。|



### <a name="pci-dss-requirement-814"></a>PCI DSS 要件 8.1.4

**8.1.4** 非アクティブなユーザー アカウントは 90 日以内に削除するか無効にする。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ユーザー管理で Azure Active Directory を使用します。 `-enableADDomainPasswordPolicy` オプションを設定して、パスワードが 90 日後に有効期限が切れることを保証できます。|



### <a name="pci-dss-requirement-815"></a>PCI DSS 要件 8.1.5

**8.1.5** サード パーティがリモート アクセス経由でシステムコンポーネントに対するアクセス、サポート、またはメンテナンスを行うために使用する ID を、次のように管理する。
- 必要な期間中のみ有効にし、使用されていないときは無効にする。
- 使用中は監視する。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、情報セキュリティ ポリシーを含む、適用可能な企業と組織のセキュリティ ポリシーを採用しています。 これらのポリシーは、承認され、公開され、Microsoft Azure に伝達されています。 業務上の正当な理由と資産の所有者の承認に基づいて付与され、"知る必要性" と "最小限の特権" の原則に基づいて制限されます。 さらに、このポリシーは、アクセスのプロビジョニング、認証、アクセスの承認、アクセス権限の削除、アクセスの定期的なレビューを含むアクセス管理ライフサイクルの要件にも対応します。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore デモでは、Azure Active Directory を実装し、Azure Active Directory のロールベースのアクセス制御でこのインストールへのユーザー アクセスを管理しています。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS 要件 8.1.6

**8.1.6** 6 回以下のアクセス試行でユーザー ID をロックアウトすることで、アクセス試行の繰り返しを制限する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デモのすべてのユーザーに対して職務の分離 (SOD) を明確に実装しています。 詳細については、「[PCI ガイダンス - ID 管理](payment-processing-blueprint.md#identity-management)の 「Azure Active Directory Identity Protection」を参照してください。|



### <a name="pci-dss-requirement-817"></a>PCI DSS 要件 8.1.7

**8.1.7** ロックアウト期間を最小値である 30 分、または管理者がユーザー ID を有効にするまでに設定する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、PCI DSS 要件に準拠しているパスワード ポリシーを作成、適用、および監視する責任があります。|



### <a name="pci-dss-requirement-818"></a>PCI DSS 要件 8.1.8

**8.1.8** セッションが 15 分を越えてアイドル状態になった場合は、再認証を行って端末またはセッションを再アクティブ化するようにユーザーに要求する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、PCI DSS 要件に準拠しているパスワード ポリシーを作成、適用、および監視する責任があります。|



## <a name="pci-dss-requirement-82"></a>PCI DSS 要件 8.2

**8.2** 一意の ID の割り当てに加え、少なくとも次の方法のいずれかを使用してすべてのユーザーを認証することで、すべてのシステム コンポーネントでのコンシューマー以外のユーザーと管理者のユーザー認証が適切に管理されることを保証する。
- 既知の情報 (パスワードやパスフレーズなど)
- 所有物 (トークン デバイスやスマート カード)
- ユーザー自身 (生体認証など)

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore の多要素認証は、デモを使いやすくするために無効になっています。 多要素認証は、[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) を使用して実装できます。|



### <a name="pci-dss-requirement-821"></a>PCI DSS 要件 8.2.1

**8.2.1** 強力な暗号化を使用して、転送中とすべてのシステム コンポーネントでの格納中にすべての認証資格情報 (パスワードやパスフレーズなど) を読み取り不可にする。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、暗号化キーをそのライフサイクルの始めから終わりまで管理するキー管理手順が確立されています (生成、配布、失効など)。 Microsoft Azure では、Microsoft の企業 PKI インフラストラクチャを使用しています。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デプロイ ガイドに文書化されている強力なパスワードの使用を強制します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS 要件 8.2.2

**8.2.2** パスワードのリセットの実行、新しいトークンのプロビジョニング、新しいキーの生成などの認証資格情報の変更を行う前にユーザー ID を確認する。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure では、暗号化キーをそのライフサイクルの始めから終わりまで管理するキー管理手順が確立されています (生成、配布、失効など)。 Microsoft Azure では、Microsoft の企業 PKI インフラストラクチャを使用しています。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デプロイ ガイドに文書化されている強力なパスワードの使用を強制します。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。|



### <a name="pci-dss-requirement-823"></a>PCI DSS 要件 8.2.3

**8.2.3** パスワード/パスフレーズは、次の要件を満たしている必要がある。
- 少なくとも 7 文字の長さがある。
- 数字と英文字の両方が含まれている。
または、パスワード/パスフレーズは、上記に規定されたパラメーターと少なくとも同等の複雑さと強さを持つ必要があります。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デプロイ ガイドに文書化されている強力なパスワードの使用を強制します。|



### <a name="pci-dss-requirement-824"></a>PCI DSS 要件 8.2.4

**8.2.4** 少なくとも 90 日ごとに 1 回はユーザーのパスワード/パスフレーズを変更する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、ユーザー管理で Azure Active Directory を使用します。 `-enableADDomainPasswordPolicy` オプションを設定して、パスワードが 90 日ごとに少なくとも 1 回有効期限が切れることを保証できます。|



### <a name="pci-dss-requirement-825"></a>PCI DSS 要件 8.2.5

**8.2.5** 最近使用した 4 つのパスワード/パスフレーズのいずれかと同じ新しいパスワード/パスフレーズの送信を個人に許可しない。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デプロイ ガイドに文書化されている強力なパスワードの使用を強制します。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS 要件 8.2.6

**8.2.6** 各ユーザーの初回の使用時と一意の値へのリセット時に使用されるパスワード/パスフレーズを設定し、初回の使用直後に変更する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、デプロイ ガイドに文書化されている強力なパスワードの使用を強制します。 詳細については、[PCI ガイダンスの ID 管理](payment-processing-blueprint.md#identity-management)に関する記事を参照してください。<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS 要件 8.3

**8.3** カード所有者データ環境 (CDE) へのすべてのコンソール以外の管理アクセスとすべてのリモート アクセスを多要素認証を使用して保護する。

> [!NOTE]
> 多要素認証では、3 つの認証方法のうちの少なくとも 2 つを使用して認証する必要があります (認証方法については、要件 8.2 の説明を参照してください)。 1 つの要素を 2 回使用する (たとえば、異なる 2 つのパスワードを使用する) 方法は 多要素認証とみなされません。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure 管理者は、Azure システムのメンテナンスと管理を実行するときは、多要素認証を使用してアクセスする必要があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 多要素認証は、デモでは実装されていません。|



### <a name="pci-dss-requirement-831"></a>PCI DSS 要件 8.3.1

**8.3.1** 管理アクセス権を持つ職員の CDE に対するコンソール以外のアクセスに多要素認証を組み込む。

> [!NOTE]
> この要件は、2018 年 1 月 31 日まではベスト プラクティスです。その後は要件になります。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure 管理者は、Azure システムのメンテナンスと管理を実行するときは、多要素認証を使用してアクセスする必要があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 多要素認証は、デモでは実装されていません。|



### <a name="pci-dss-requirement-832"></a>PCI DSS 要件 8.3.2

**8.3.2** エンティティのネットワーク外から受信されるすべてのリモート ネットワーク アクセス (ユーザーと管理者の両方。サードパーティがサポートとメンテナンスを行うためのアクセスも含まれます) に、多要素認証を組み込む。


**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Azure 管理者は、Azure システムのメンテナンスと管理を実行するときは、多要素認証を使用してアクセスする必要があります。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 多要素認証は、デモでは実装されていません。|



## <a name="pci-dss-requirement-84"></a>PCI DSS 要件 8.4

**8.4** 以下を含む認証ポリシーと手順を文書化し、すべてのユーザーに通知する。
- 強力な認証資格情報の選択に関するガイダンス
- ユーザーが自分の認証資格情報を保護する方法に関するガイダンス
- 前に使用したことがあるパスワードを再利用しないことの指示
- パスワードが漏れた疑いがある場合は、パスワードを変更することの指示

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、ガイダンスに従うこと、認証手順とポリシーを文書化してすべてのユーザーに通知する責任があります。|



## <a name="pci-dss-requirement-85"></a>PCI DSS 要件 8.5

**8.5** 次のようにグループ ID、共有 ID、汎用 ID、パスワード、または他の認証方法を使用しない。
- 汎用ユーザー ID を無効にするか削除する。
- システム管理およびその他の重要な機能用の共有ユーザー ID が存在しない。
- システム コンポーネントの管理で共有ユーザー ID と汎用ユーザー ID を使用しない。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 多要素認証は、デモでは実装されていません。|



### <a name="pci-dss-requirement-851"></a>PCI DSS 要件 8.5.1

**8.5.1** **サービス プロバイダーのみに適用される追加要件:** たとえば POS システムやサーバーをサポートするためにお客さまにリモート アクセスするサービス プロバイダーは、お客様ごとに一意の認証資格情報 (パスワード/パスフレーズなど) を使用する必要がある。 

> [!NOTE]
> この要件は、独自のホスト環境 (複数のお客さまの環境がホストされる環境) にアクセスする共有ホスティング プロバイダーに適用される意図はありません。

**責任:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | Microsoft Azure のお客様には適用されません。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Microsoft Azure のお客様には適用されません。|



## <a name="pci-dss-requirement-86"></a>PCI DSS 要件 8.6

**8.6** 他の認証メカニズム (物理的または論理的なセキュリティ トークン、スマート カード、証明書など) が使用されるところでは、これらのメカニズムの使用は次のように割り当てる必要がある。
- 認証メカニズムは、個人アカウントに割り当る必要があり、複数のアカウント間で共有してはならない。
- 物理/論理コントロールを配置して、目的のアカウントのみがそのメカニズムを使用してアクセスできることを保証する必要ある。

**責任:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore では、デプロイ時に 3 つのアカウント (admin、sqladmin、および edna) を作成します (edna はデモの実行中にこの Web アプリにログインする既定のユーザーです)。 多要素認証は、デモでは実装されていません。 すべてのアクセスは [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 経由で管理され、クラウド アプリケーションとサービスで使用される暗号化キーとシークレットをセキュリティで保護できます。 |



## <a name="pci-dss-requirement-87"></a>PCI DSS 要件 8.7

**8.7** カード所有者データが格納されているデータベースへの (アプリケーション、管理者、およびその他すべてのユーザーによるアクセスを含む) すべてのアクセスは、次のように制限する。
- データベースに対するすべてのユーザー のアクセス、クエリ、アクションは、プログラム的な方法で実行される。
- データベースに対する直接的なアクセスやクエリは、データベース管理者だけが実行できる。
- データベース アプリケーションのアプリケーション ID は、アプリケーションのみが使用できる (個人ユーザーやその他のアプリケーション以外のプロセスは使用できません)。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | Contoso Webstore は、すべてのカード所有者データを Azure Key Vault を使用して保護します。レコードの暗号化は、デプロイに関するドキュメントで概要が説明されています。 詳細については、「[PCI ガイダンス - 暗号化](payment-processing-blueprint.md#encryption-and-secrets-management)」を参照してください。<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS 要件 8.8

**8.8** ID と認証に関するセキュリティ ポリシーと運用手順が文書化され、使用され、すべての関係者に通知されていることを保証する。

**責任:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **プロバイダー <br />(Microsoft&nbsp;Azure)** | 適用不可。 |
| **お客様 <br />(PCI&#8209;DSS&nbsp;Blueprint)** | お客様は、ID と認証に関するセキュリティ ポリシーと運用手順が文書化され、使用され、すべての関係者に通知されていることを保証する責任があります。|




