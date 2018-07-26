---
title: Azure Active Directory B2B コラボレーション ユーザーのプロパティ | Microsoft Docs
description: Azure Active Directory B2B コラボレーション ユーザーのプロパティは構成できます
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057894"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B コラボレーション ユーザーのプロパティ

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーション ユーザーは、UserType = Guest のユーザーです。 このゲスト ユーザーは通常、パートナー組織に属しており、既定では招待側のディレクトリ内で制限された権限を付与されています。

招待側の組織のニーズに応じて、Azure AD B2B コラボレーション ユーザーは、以下のいずれかのアカウント状態になります。

- 状態 1: Azure AD の外部インスタンスに所属し、招待側組織のゲスト ユーザーとして表されます。 この場合、B2B ユーザーは招待されたテナントに属している Azure AD アカウントを使用してサインインします。 パートナー組織が Azure AD を使用しない場合でも、Azure AD にゲスト ユーザーが作成されます。 要件は、ゲスト ユーザーが招待に応じること、および Azure AD が彼らの電子メール アドレスを検証することです。 この状態は、Just-In-Time (JIT) テナント、または "バイラル テナント" とも呼ばれます。

- 状態 2: Microsoft アカウントに所属し、ホスト組織のゲスト ユーザーとして表されます。 この場合、ゲスト ユーザーは Microsoft アカウントでサインインします。 招待されたユーザーのソーシャル ID (google.com など、Microsoft アカウント以外のもの) は、招待に応じる際に Microsoft アカウントとして作成されます。

- 状態 3: ホスト組織のオンプレミス Active Directory に所属し、ホスト組織の Azure AD と同期されます。 このリリースでは、クラウドのこのようなユーザーの UserType を、PowerShell を使用して手動で変更する必要があります。

- 状態 4: ホスト組織の Azure AD に所属し、UserType = Guest で、資格情報はホスト組織によって管理されています。

  ![招待元のイニシャルの表示](media/user-properties/redemption-diagram.png)


ここで、状態 1 の Azure AD B2B コラボレーション ユーザーが Azure AD でどのように見えるかを見てみましょう。

### <a name="before-invitation-redemption"></a>招待に応じる前

![オファーに応じる前](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>招待に応じた後

![オファーに応じた後](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B コラボレーション ユーザーの主要なプロパティ
### <a name="usertype"></a>UserType
このプロパティは、ユーザーとホスト テナントとの関係を示します。 このプロパティは次の 2 つの値を取ります。
- メンバー: この値は、ホスト組織の従業員、組織の給与支払い名簿にあるユーザーを示します。 たとえば、このユーザーは、内部専用のサイトにアクセスできることが想定されます。 このユーザーが外部コラボレーターと見なされることはありません。

- ゲスト: この値は、社内ユーザーとみなされないユーザー (外部コラボレーター、パートナー、顧客、または類似ユーザーなど) を示します。 このようなユーザーに、CEO の社内メモの送信や各種手当の給付が行われることは期待されません。

  > [!NOTE]
  > UserType は、ユーザーのサインイン方法、ユーザーのディレクトリ ロールなどとは関係ありません。 このプロパティは、単にユーザーとホスト組織との関係を示しており、組織はこのプロパティに基づくポリシーを強制できます。

### <a name="source"></a>ソース
このプロパティは、ユーザーのサインイン方法を示します。

- 招待されたユーザー: このユーザーは招待されましたが、まだ招待に応じていません。

- 外部 Active Directory: このユーザーは外部組織に所属し、他の組織に属している Azure AD アカウントを使用して認証を行います。 この種類のサインインは、状態 1 に対応しています。

- Microsoft アカウント: このユーザーは Microsoft アカウントに所属し、Microsoft アカウントを使用して認証を行います。 この種類のサインインは、状態 2 に対応しています。

- Windows Server Active Directory: このユーザーは、この組織に属しているオンプレミス Active Directory からサインインされました。 この種類のサインインは、状態 3 に対応しています。

- Azure Active Directory: このユーザーは、この組織に属している Azure AD アカウントを使用して認証されます。 この種類のサインインは、状態 4 に対応しています。
  > [!NOTE]
  > Source と UserType は、独立したプロパティです。 Source の値は、UserType の特定の値を意味しません。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B ユーザーをゲストではなくメンバーとして追加できるか
通常は、Azure AD B2B ユーザーとゲスト ユーザーは同義です。 そのため、Azure AD B2B コラボレーション ユーザーは、既定では UserType = Guest のユーザーとして追加されます。 ただし、一部のケースでは、パートナー組織がより大きな組織のメンバーであり、ホスト組織もその組織に所属しています。 そのような場合は、ホスト組織がパートナー組織のユーザーをゲストではなくメンバーとして扱いたいことがあります。 Azure AD B2B Invitation Manager API を使用して、パートナー組織のユーザーをホスト組織にメンバーとして追加または招待します。

## <a name="filter-for-guest-users-in-the-directory"></a>ディレクトリのゲスト ユーザーのフィルター処理

![ゲスト ユーザーのフィルター処理](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType の変換
現時点では、ユーザーは PowerShell を使用して、UserType をメンバーからゲストまたはその逆に変換できます。 ただし、UserType プロパティはユーザーと組織の関係を表していると考えられます。 そのため、このプロパティの値は、ユーザーと組織の関係が変化した場合にのみ変更する必要があります。 ユーザーの関係が変化した場合に、ユーザー プリンシパル名 (UPN) を変更する必要があるかどうかというような問題に対処する必要がありますか。 また、同じリソースへのアクセス権を引き続きユーザーに持たせる必要がありますか。 メールボックスを割り当てる必要があるか、などの疑問です。 そのため、PowerShell を使用してアトミック アクティビティとして UserType を変更することはお勧めしません。 さらに、PowerShell を使用してこのプロパティを変更できなくなる場合に備えて、この値には依存しないことをお勧めします。

## <a name="remove-guest-user-limitations"></a>ゲスト ユーザー制限の削除
ゲスト ユーザーに、より高い権限を付与したい場合があります。 ゲスト ユーザーを任意のロールに追加することができます。また、メンバーと同じ権限を付与するために、ディレクトリでの既定のゲスト ユーザー制限を削除することもできます。

既定のゲスト ユーザー制限を無効にして、会社のディレクトリのゲスト ユーザーにメンバー ユーザーと同じアクセス許可を付与することができます。

![ゲスト ユーザー制限の削除](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Exchange のグローバル アドレス一覧にゲスト ユーザーを表示できますか。
はい。 既定では、ゲスト オブジェクトは組織のグローバル アドレス一覧には表示されませんが、Azure Active Directory PowerShell を使用してそれらを表示できます。 詳しくは、「[Office 365 グループのゲスト アクセス](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ)」の「**グローバル アドレス一覧にゲスト オブジェクトを表示することはできますか?**」をご覧ください。 

## <a name="next-steps"></a>次の手順

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [B2B コラボレーション ユーザーのトークン](user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](claims-mapping.md)
