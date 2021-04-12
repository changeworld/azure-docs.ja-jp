---
title: 現場ワーカーの管理 - Azure Active Directory
description: マイ スタッフ ポータルを通じて提供される現場ワーカー管理機能について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975728"
---
# <a name="frontline-worker-management"></a>現場ワーカーの管理

現場ワーカーは、世界の労働人口の 80% 以上を占めています。 しかし、規模の大きさ、労働力の入れ替えの速さ、プロセスの断片化が原因で、多くの場合、要求の厳しい現場ワーカーの仕事をわずかでも簡単にするためのツールが不足しています。 現場ワーカーの管理により、現場の従業員にデジタル変革がもたらされます。 従業員とは、管理者、現場ワーカー、運用、IT 担当者などです。

現場ワーカーの管理は、次のアクティビティを簡単に実行できるようにすることで、現場の従業員を支援します。
- マイ スタッフを使用した、一般的な IT タスクの合理化
- 簡略化された認証による、現場ワーカーの容易なオンボード
- 共有デバイスのシームレスなプロビジョニングと現場ワーカーの安全なサインアウト

## <a name="delegated-user-management-through-my-staff"></a>マイ スタッフによる委任されたユーザー管理

Azure Active Directory (Azure AD) は、[マイ スタッフ ポータル](../roles/my-staff-configure.md)を介してユーザー管理を現場の管理者に委任する機能を備えています。これにより、貴重な時間を節約し、リスクを軽減できます。 店舗や工場の現場から直接、パスワード リセットや電話管理を簡単にできるようすることで、ヘルプデスク、IT 担当者、または運用担当者を介してリクエストを転送しなくても、管理者が従業員にアクセスを許可できます。

![マイ スタッフ ポータルでの委任されたユーザー管理](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>簡略化された認証によるオンボードの迅速化

マイ スタッフを使用すると、現場の管理者が自分のチーム メンバーの電話番号を [SMS サインイン](../authentication/howto-authentication-sms-signin.md)用に登録できます。 多くの業種では、現場ワーカーはローカルのユーザー名とパスワードの組み合わせを維持していますが、多くの場合、これは煩雑で、コストがかかり、間違いが発生しやすい方策です。 IT 担当者が SMS サインインを使用した認証を有効にすると、現場ワーカーは、自分の電話番号と SMS を介して送信されたワンタイム パスコード (OTP) を使用するだけで、Microsoft Teams やその他のアプリで[シングル サインオン (SSO)](../manage-apps/what-is-single-sign-on.md) によってログインできます。 これにより、現場ワーカーは、サインインが簡単で安全になり、自分が最も必要とするアプリにすばやくアクセスできます。

![SMS サインイン](media/concept-fundamentals-frontline-worker/sms-signin.png)

現場の管理者は、Managed Home Screen (MHS) アプリケーションを使用して、ワーカーが、Intune に登録された自分の Android 専用デバイスから特定のアプリケーション セットにアクセスできるようにすることもできます。 専用デバイスは [Azure AD 共有デバイス モード](../develop/msal-shared-devices.md)で登録されます。 Microsoft エンドポイント マネージャー (MEM) コンソール内でマルチアプリ キオスク モードで構成されている場合、MHS はデバイスの既定のホーム画面として自動的に起動され、"*唯一*" のホーム画面としてエンド ユーザーに表示されます。 詳細については、[Android Enterprise 用に Microsoft Managed Home Screen アプリを構成する](/mem/intune/apps/app-configuration-managed-home-screen-app)方法を参照してください。

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>共有デバイスからの現場ワーカーの安全なサインアウト

多くの会社では共有デバイスを使用することで、個々のデバイスのプロビジョニングと追跡を行うための IT 上の負担を生じることなく、現場ワーカーが在庫管理と販売時点管理のトランザクションを実行できます。 共有デバイスのサインアウトを使用すると、現場ワーカーは共有デバイスをハブに戻したり次のシフトのチームメイトに渡したりする前に、簡単にそのすべてのアプリから安全にサインアウトできます。 Microsoft Teams は、共有デバイスで現在サポートされているアプリの 1 つです。これを使用して、現場ワーカーは自分に割り当てられているタスクを確認できます。 ワーカーが共有デバイスからサインアウトすると、Intune と Azure AD によってその会社のすべてのデータがクリアされるため、デバイスを安全に次の同僚に渡すことができます。 必要に応じて、[Microsoft Authentication Library](../develop/msal-overview.md) を使用してすべての [iOS](../develop/msal-ios-shared-devices.md) および [Android](../develop/msal-android-shared-devices.md) の基幹業務アプリにこの機能を統合できます。

![共有デバイスのサインアウト](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>次の手順

- 委任されたユーザー管理の詳細については、[マイ スタッフのユーザード キュメント](../user-help/my-staff-team-manager.md)を参照してください。
- SAP SuccessFactors からのユーザー プロビジョニングについては、[SAP SuccessFactors から Active Directory へのユーザー プロビジョニングの構成](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)に関するチュートリアルを参照してください。
- Workday からのユーザー プロビジョニングについては、[Workday を構成して自動ユーザー プロビジョニングに対応させる](../saas-apps/workday-inbound-tutorial.md)ためのチュートリアルを参照してください。
