---
title: "Azure Active Directory B2B コラボレーション ユーザーのプロパティ | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーション ユーザーのプロパティは構成できます"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B コラボレーション ユーザーのプロパティ

## <a name="defining-a-b2b-collaboration-user"></a>B2B コラボレーション ユーザーの定義
B2B コラボレーション ユーザーは、UserType = Guest、つまりゲスト ユーザーであるユーザーです。 このユーザーは通常、パートナー組織のユーザーを表し、既定では招待側のディレクトリ内で制限された権限を付与されています。 招待側の組織のニーズに応じて、B2B コラボレーション ユーザーは、以下のいずれかのアカウント状態になります。
1. Azure Active Directory (Azure AD) の外部インスタンスに所属し、ホスト組織のゲスト ユーザーとして表されます。 この場合、B2B ユーザーはホーム テナントに属している Azure AD アカウントでサインインします。 ユーザーが所属する外部組織が、招待時に Azure AD を使用していない場合は、ユーザーが招待に応じると、電子メール アドレスの検証後に Azure AD のゲスト ユーザーが "ジャスト イン タイム" で作成されます。 これは、ジャスト イン タイム (JIT) テナント、またはバイラル テナントとも呼ばれます。
2. Microsoft アカウントに所属し、ホストの組織のゲスト ユーザーとして表されます。 この場合、ゲスト ユーザーは Microsoft アカウントでサインインします。 B2B コラボレーションの Azure AD パブリック プレビューの更新では、招待されたユーザーの非 MSA ソーシャル ID (google.com など) は、オファーに応じる際にジャスト イン タイムで Microsoft アカウントとして作成されます。
3. ホスト組織のオンプレミス Active Directory に所属し、ホスト組織の Azure AD と同期されます。 このリリースでは、クラウドのこのようなユーザーの UserType は、PowerShell を使用して手動でゲストに変更する必要があります。 将来のリリースでは、Azure AD Connect の一部として、この操作が自動的に処理されるようになります。
4. ホスト組織の Azure AD に UserType = Guest として所属し、資格情報はホスト組織によって管理されます。

  ![招待元のイニシャルの表示](media/active-directory-b2b-user-properties/redemption-diagram.png)


ここで、状態 1 の B2B コラボレーション ユーザーが Azure AD でどのように見えるかを見てみましょう。

### <a name="before-invitation-redemption"></a>招待に応じる前

![オファーに応じる前](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>招待に応じた後

![オファーに応じた後](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>B2B コラボレーション ユーザーの主要なプロパティ

### <a name="usertype"></a>UserType
この属性は、ユーザーとホスト テナントとの関係を示します。 これは次の&2; つの値を取ります。
- メンバー: ホスト組織の従業員、組織の給与支払い名簿にあるユーザー。 たとえば、内部専用サイトにアクセスできることが想定されているユーザーです。 このユーザーが外部コラボレーターと見なされることはありません。
- ゲスト: 会社の内部人員と見なされないユーザーを示します。 外部コラボレーター、パートナー、顧客や、たとえば CEO の内部メモを見たり会社給付を受けたりすることが想定されていないようなユーザーなどです。

  > ![注] UserType は、ユーザーのサインイン方法やユーザーが所属するディレクトリ ロールなどとは関係がありません。 この属性は、単にユーザーとホスト組織との関係を示しており、組織はこの属性に基づいて任意のポリシーを強制できます。

### <a name="source"></a>から
ユーザーのサインインの方法。

- 招待されたユーザー: このユーザーは招待されましたが、まだ招待に応じていません。

- 外部 Active Directory: このユーザーは外部組織に所属し、他の組織に属している Azure AD アカウントで認証を行います。 これは、上の状態 1 に対応します。

- Microsoft アカウント: このユーザーは MSA に所属し、Microsoft アカウントで認証を行います。 これは、上の状態 2 に対応します。

- Windows Server AD: このユーザーは、この組織に属しているオンプレミス Active Directory から同期されます。 これは、上の状態 3 に対応します。

- Azure Active Directory: このユーザーは、この組織に属している Azure AD アカウントで認証されます。 これは、上の状態 4 に対応します。

  > ![注] Source と UserType は、独立した属性です。 ソースの値は、特定の UserType を意味しません。

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>B2B ユーザーをゲストではなくメンバーとして追加できるか
通常は、B2B ユーザーとゲスト ユーザーは同義です。 そのため、B2B コラボレーション ユーザーは、既定では UserType = Guest のユーザーとして追加されます。 ただし、一部のケースでは、パートナー組織が実際にはより大きな統括組織のメンバーであり、ホスト組織もその統括組織に所属しています。 そのような場合は、ホスト組織がパートナー組織のユーザーをゲストではなくメンバーとして扱いたいことがあります。 その場合は、B2B Invitation Manager API を使用して、パートナー組織のユーザーをホスト組織にメンバーとして追加または招待します。

## <a name="filtering-for-guest-users-in-the-directory"></a>ディレクトリのゲスト ユーザーのフィルター処理

![ゲスト ユーザーをフィルター処理する](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>UserType の変換
現時点では、PowerShell でユーザーが UserType をメンバーからゲストまたはその逆に変換できます。 ただし、UserType プロパティはユーザーと組織の関係を表していると考えられます。 そのため、このプロパティは、ユーザーと組織の関係が変化した場合にのみ変更する必要があります。 ユーザーの関係を変更する場合は、他の疑問にも答える必要があります。たとえば、UPN を変更する必要があるか、 アクセス権があったリソースへのアクセス権を引き続きユーザーに持たせる必要があるか、 メールボックスを割り当てる必要があるか、などの疑問です。 そのため、PowerShell でアトミック アクティビティとして UserType を変更することはお勧めしません。 また、将来のリリースではこのプロパティを PowerShell を通じて変更できないようにする予定なので、この値に依存することはお勧めしません。

## <a name="removing-guest-user-limitations"></a>ゲスト ユーザーの制限の削除
ゲスト ユーザーに、より高い権限を付与したい場合があります。 そのため、ゲスト ユーザーを任意のロールに追加することができます。また、メンバーと同じ権限を付与するために、ディレクトリでの既定のゲスト ユーザー制限を削除することもできます。 詳細については、後の説明を参照してください。

既定のゲスト ユーザー制限を無効にして、会社のディレクトリのゲスト ユーザーに通常のユーザー (メンバー) と同じディレクトリ アクセス許可を付与することができます。

![ゲスト ユーザー制限を削除する](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


