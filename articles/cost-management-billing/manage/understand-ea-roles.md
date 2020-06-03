---
title: Azure の Enterprise 管理者ロールを理解する
description: Azure の Enterprise 管理者ロールについて説明します。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: banders
ms.openlocfilehash: 15c686e0d33d7341d16097f32e1c69077c319a12
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295305"
---
# <a name="managing-azure-enterprise-roles"></a>Azure エンタープライズ ロールの管理

組織による使用量/支出管理を支援するために、Enterprise Agreement (EA) をお持ちの Azure カスタマーは 5 つの異なる管理者ロールを割り当てることができます。

- エンタープライズ管理者
- エンタープライズ管理者 (読み取り専用)<sup>1</sup>
- 部門管理者
- 部門管理者 (読み取り専用)
- アカウント所有者<sup>2</sup>

<sup>1</sup> EA 契約の請求先は、このロールの連絡先になります。

<sup>2</sup> 請求先担当者は、Azure EA Portal で追加したり変更したりすることはできません。契約レベルで請求先担当者として設定されているユーザーに基づいて、EA 加入契約に追加されます。 請求先担当者を変更するには、パートナーまたはソフトウェア アドバイザーを通じて地域オペレーション センター (ROC) に要請する必要があります。

以上のロールは Azure Enterprise Agreement 管理に固有のものであり、リソース アクセスを制御する目的で Azure に与えられる組み込みロールに追加されるものです。 詳細については、「[Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

次のセクションでは、各ロールの制限事項と機能について説明します。

## <a name="user-limit-for-admin-roles"></a>管理ロールのユーザー数制限

|Role| ユーザー数制限|
|---|---|
|エンタープライズ管理者|無制限|
|エンタープライズ管理者 (読み取り専用)|無制限|
|部門管理者|無制限|
|部門管理者 (読み取り専用)|無制限|
|アカウント所有者|アカウントにつき 1 つ<sup>3</sup>|

<sup>3</sup> アカウントごとに、重複のない Microsoft アカウントか、職場または学校アカウントを 1 つ必要とします。

## <a name="organization-structure-and-permissions-by-role"></a>組織の構造とロール別のアクセス許可

|タスク| エンタープライズ管理者|エンタープライズ管理者 (読み取り専用)|部門管理者|部門管理者 (読み取り専用)|アカウント所有者| Partner|
|---|---|---|---|---|---|---|
|エンタープライズ管理者を表示する|✔|✔|✘|✘|✘|✔|
|エンタープライズ管理者を追加または削除する|✔|✘|✘|✘|✘|✘|
|通知の連絡窓口を表示する<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|通知の連絡窓口を追加または削除する<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|部門を作成し、管理する |✔|✘|✘|✘|✘|✘|
|部門管理者を表示する|✔|✔|✔|✔|✘|✔|
|部門管理者を追加または削除する|✔|✘|✔|✘|✘|✘|
|登録済みのアカウントを表示する |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|登録にアカウントを追加する/アカウント所有者を変更する|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|サブスクリプションとサブスクリプション アクセス許可を作成し、管理する|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> 通知の連絡窓口には Azure Enterprise Agreement に関する電子メール通信が送信されます。
- <sup>5</sup> タスクは部門内のアカウントに制限されます。

## <a name="add-a-new-enterprise-administrator"></a>新しいエンタープライズ管理者を追加する

エンタープライズ管理者は、Azure EA 登録を管理する際に、最も多くの特権を持っています。 EA 契約を設定したときに、最初の Azure EA 管理者が作成されています。 ただし、いつでも新しい管理者を追加したり、管理者を削除したりできます。 新しい管理者を追加できるのは、既存の管理者のみです。 エンタープライズ管理者を追加する方法の詳細については、「[Create another enterprise admin (別のエンタープライズ管理者を作成する)](ea-portal-get-started.md#create-another-enterprise-administrator)」を参照してください。課金プロファイルのロールとタスクの詳細については、「[課金プロファイルのロールとタスク](understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

## <a name="update-account-owner-state-from-pending-to-active"></a>アカウント所有者状態を保留中からアクティブに更新する

新しいアカウント所有者 (AO) が初めて Azure EA 登録に追加されると、状態は "_保留中_" と表示されます。 アクティブ化のウェルカム メールを受け取った新しいアカウント所有者は、サインインしてアカウントをアクティブにすることができます。 アカウントをアクティブにすると、アカウントの状態が "_保留中_" から "_アクティブ_" に更新されます。 アカウント オーナーは "警告" メッセージを読み、 **[続行]** を選択する必要があります。 新しいユーザーは、姓と名を入力してコマース アカウントを作成するよう求められる場合があります。 その場合、続行するためには必須情報を追加する必要があります。これによって、アカウントがアクティブになります。

## <a name="add-a-department-admin"></a>部署管理者を追加する

Azure EA 管理者は、部署を作成した後、部署管理者を追加し、それぞれを部署に関連付けることができます。 部署管理者は、新しいアカウントを作成できます。 Azure EA サブスクリプションを作成するには、新しいアカウントが必要です。

部門管理者を追加する方法の詳細については、[Azure EA 部門管理者の作成](ea-portal-get-started.md#add-a-department-administrator)に関するセクションを参照してください。

## <a name="usage-and-costs-access-by-role"></a>ロール別の使用量/コスト アクセス

|タスク| エンタープライズ管理者|エンタープライズ管理者 (読み取り専用)|部門管理者|部門管理者 (読み取り専用) |アカウント所有者| Partner|
|---|---|---|---|---|---|---|
|年額コミットメントを含む、与信残高を表示する|✔|✔|✘|✘|✘|✔|
|部門の支出クォータを表示する|✔|✔|✘|✘|✘|✔|
|部門の支出クォータを設定する|✔|✘|✘|✘|✘|✘|
|組織の EA 価格シートを表示する|✔|✔|✘|✘|✘|✔|
|使用量とコストの詳細を表示する|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Azure portal でリソースを管理する|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> エンタープライズ管理者が Enterprise portal で **DA ビューの請求額**ポリシーを有効にする必要があります。 有効にすると、部門管理者は部門のコスト詳細を表示できます。
- <sup>7</sup> エンタープライズ管理者が Enterprise portal で **AO ビューの請求額**ポリシーを有効にする必要があります。 有効にすると、アカウント所有者はアカウントのコスト詳細を表示できます。

## <a name="see-pricing-for-different-user-roles"></a>さまざまなユーザー ロールの価格を確認する

Azure portal に表示される価格は管理者ロールによって異なる場合があります。また、エンタープライズ管理者による請求金額の表示ポリシーの設定方法にも左右されます。 Azure portal に表示される価格を左右する Enterprise portal の 2 つのポリシー:

- DA ビューの請求額
- AO ビューの請求額

これらのポリシーを設定する方法については、「[Manage access to billing information for Azure](manage-billing-access.md)」(Azure の課金情報へのアクセスの管理) を参照してください。

次の表は、Enterprise Agreement 管理者ロール、請求金額の表示ポリシー、Azure portal のロールベースのアクセス制御 (RBAC) ロール、Azure portal に表示される価格の関係をまとめたものです エンタープライズ管理者には、組織の EA 価格に基づいて使用料詳細が常に表示されます。 ただし、部門管理者とアカウント所有者では、請求金額の表示ポリシーとその RBAC ロールに基づいて、表示される価格ビューが異なります。 次の表に記載されている部門管理者ロールは、部門管理者ロールと部門管理者 (読み取り専用) ロールの両方を指します。

|Enterprise Agreement 管理者ロール|ロールの請求金額の表示ポリシー|RBAC ロール|価格ビュー|
|---|---|---|---|
|アカウント所有者または部門管理者|✔ 有効|所有者|組織の EA 価格|
|アカウント所有者または部門管理者|✘ 無効|所有者|小売価格|
|アカウント所有者または部門管理者|✔ 有効 |なし|価格設定なし|
|アカウント所有者または部門管理者|✘ 無効 |なし|価格設定なし|
|なし|適用なし |所有者|小売価格|

Enterprise portal でエンタープライズ管理者ロールと請求金額の表示ポリシーを設定します。 RBAC ロールは Azure portal で更新できます。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。



## <a name="next-steps"></a>次のステップ

- [Azure の課金情報へのアクセスの管理](manage-billing-access.md)
- [RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)
- [Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)
