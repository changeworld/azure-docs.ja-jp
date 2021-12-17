---
title: ID 保護と B2B ユーザー - Azure Active Directory
description: B2B ユーザーによる Identity Protection の使用
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3a5efbb9c990173c7012b9ff6f938fed33f1ad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734560"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection と B2B ユーザー

Identity Protection は、Azure AD ユーザーの資格情報の侵害を検出します。 資格情報が侵害されていると検出された場合は、他の誰かがパスワードを持っていて、それを不正に使用している可能性があることを意味します。 アカウントのリスクが高まるのを防ぐためには、パスワードを安全にリセットし、悪意のある人物が侵害されたパスワードを使用できなくなるようにすることが重要です。 Identity Protection は、侵害されている可能性のあるアカウントを "リスクあり" としてマークします。

組織の資格情報を使用して、別の組織にゲストとしてサインインできます。 このプロセスは、[企業間または B2B コラボレーション](../external-identities/what-is-b2b.md)と呼ばれます。 組織は、ユーザーの資格情報に[リスクがある](concept-identity-protection-risks.md)と見なされた場合に、そのユーザーがサインインできないようにするポリシーを構成できます。 自分のアカウントがリスクにさらされており、別の組織にゲストとしてサインインできない場合は、次の手順を使用して自分のアカウントを自己修復できる可能性があります。 組織でセルフサービスによるパスワード リセットが有効になっていない場合は、管理者が手動でアカウントを修復する必要があります。

## <a name="how-to-unblock-your-account"></a>自分のアカウントのブロックを解除する方法 

別の組織にゲストとしてサインインしようとして、リスクを理由にブロックされた場合は、「アカウントがブロックされています。 お客様のアカウントについて、疑わしいアクティビティが検出されました。」というブロック メッセージが表示されます。 

![ゲスト アカウントがブロックされました。組織の管理者に連絡してください](./media/concept-identity-protection-b2b/risky-guest-user-blocked.png)

組織で有効になっている場合は、セルフサービスによるパスワード リセットを使用してアカウントのブロックを解除して、資格情報を安全な状態に戻すことができます。
1. [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)に移動して、パスワード リセットを開始します。 セルフサービスのパスワード リセットが自分のアカウントで有効になっておらず、続行できない場合は、[下](#how-to-remediate-a-users-risk-as-an-administrator)の情報を使用して IT 管理者に連絡してください。
2. セルフサービスのパスワード リセットが自分のアカウントで有効になっている場合は、パスワードを変更する前に、セキュリティ手段を使用して本人確認を行うように求められます。 詳細については、[職場または学校のパスワードのリセット](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e)に関する記事を参照してください。
3. パスワードが正常かつ安全にリセットされると、ユーザーのリスクが修復されます。 もう一度、ゲスト ユーザーとしてサインインを試してください。

パスワードをリセットした後も、リスクを理由にゲストとしてブロックされている場合は、組織の IT 管理者に連絡してください。

## <a name="how-to-remediate-a-users-risk-as-an-administrator"></a>管理者としてユーザーのリスクを修復する方法

Identity Protection は、Azure AD テナントにとってリスクのあるユーザーを自動的に検出します。 以前に Identity Protection のレポートを確認していなければ、リスクのあるユーザーが多数存在する可能性があります。 リソース テナントがユーザー リスク ポリシーをゲスト ユーザーに適用できるため、ユーザーは、それまで危険な状態を認識していなくても、リスクを理由にブロックされる可能性があります。 ユーザーが、別のテナントでリスクを理由にゲスト ユーザーとしてブロックされていると報告してきた場合は、そのユーザーを修復してアカウントを保護し、コラボレーションができるようにすることが重要です。 

### <a name="reset-the-users-password"></a>ユーザーのパスワードをリセットする

[Azure AD セキュリティ] メニューの [[危険なユーザー レポート]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers) から、"ユーザー" フィルターを使用して、影響を受けたユーザーを検索します。 レポートで影響を受けたユーザーを選択して、上部のツールバーで [パスワードのリセット] をクリックします。 ユーザーに割り当てられる一時パスワードは、次のサインイン時に変更しなければなりません。 このプロセスによって、ユーザーのリスクが修復されて、資格情報が安全な状態に戻ります。

### <a name="manually-dismiss-users-risk"></a>ユーザーのリスクを手動で無視する

Azure AD ポータルからパスワード リセットを選択できない場合は、ユーザーのリスクを手動で無視することができます。 このプロセスによりユーザーはリスクがなくなりますが、既存のパスワードには何の影響もありません。 重要なのは、ID を安全な状態に戻すために使用できるあらゆる方法を使用してユーザーのパスワードを変更することです。 

ユーザーのリスクを無視するには、[Azure AD セキュリティ] メニューの [[危険なユーザー レポート]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/RiskyUsers) に移動します。 "ユーザー" フィルターを使用して影響を受けたユーザーを検索し、そのユーザーをクリックします。 上部のツールバーで [ユーザー リスクを無視する] オプションをクリックします。 このアクションは、完了してレポートのユーザーのリスク状態が更新されるまでに数分かかる場合があります。

Identity Protection の詳細については、「[Identity Protection とは](overview-identity-protection.md)」を参照してください。

## <a name="how-does-identity-protection-work-for-b2b-users"></a>Identity Protection は B2B ユーザーに対してどのように機能するか?

B2B コラボレーション ユーザーのユーザー リスクは、ホーム ディレクトリで評価されます。 これらのユーザーのリアルタイムなサインイン リスクは、ユーザーがリソースにアクセスしようとしたときに、リソース ディレクトリで評価されます。 Azure AD B2B コラボレーションでは、Identity Protection を使用して、B2B ユーザーに対するリスクベースのポリシーを適用できます。 これらのポリシーは、次の 2 つの方法で構成できます。

- 管理者は、組み込みの Identity Protection によるリスクベースのポリシーを構成し、それをすべてのアプリに適用して、ゲストユーザーを対象に含めることができます。
- 管理者は、サインインリスクを条件として使用し、ゲストユーザーを対象に含めた条件付きアクセスポリシーを構成できます。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの ID 保護に関する制限事項

リソース ディレクトリ内における B2B コラボレーション ユーザーの ID 保護の実装については、複数の制限事項があります。これは、それらのユーザーの ID がホーム ディレクトリに存在するためです。 主な制限事項は次のとおりです。

- ゲスト ユーザーが Identity Protection のユーザー リスク ポリシーをトリガーしてパスワードのリセットを強制した場合、**そのユーザーはブロックされます**。 このブロックは、リソース ディレクトリ内ではパスワードをリセットできないことが原因です。
- **ゲスト ユーザーは、危険なユーザー レポートには表示されません**。 この制限事項は、リスク評価が B2B ユーザーのホーム ディレクトリで実行されることに起因します。
- 管理者は、リソース ディレクトリ内の **危険な B2B コラボレーション ユーザーを無視したり、修復したりすることはできません**。 この制限事項は、リソース ディレクトリ内の管理者が B2B ユーザーのホーム ディレクトリにアクセスできないことに起因します。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>ディレクトリ内の危険な B2B コラボレーション ユーザーを修復できないのはなぜですか?

B2B ユーザーのリスク評価と修復は、それらのユーザーのホーム ディレクトリで行われます。 そのため、ゲスト ユーザーはリソース ディレクトリの危険なユーザー レポートには表示されず、リソース ディレクトリ内の管理者は、それらのユーザーに対して、セキュリティで保護されたパスワードのリセットを強制することはできません。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>組織内のリスクベース ポリシーによって B2B コラボレーション ユーザーがブロックされた場合はどうすればよいですか?

ディレクトリ内の危険な B2B ユーザーがリスクベースのポリシーによってブロックされた場合、そのユーザーは自分のホーム ディレクトリでそのリスクを修復する必要があります。 ユーザーは、[上で説明したように](#how-to-unblock-your-account)、セキュリティで保護されたパスワードのリセットをホーム ディレクトリで実行することにより、リスクを修復できます。 ユーザーのホーム ディレクトリでセルフサービス パスワード リセット が有効になっていない場合は、そのユーザーが組織の IT スタッフに連絡して、管理者にリスクを手動で無視してもらったり、パスワードをリセットしてもらう必要があります。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B コラボレーション ユーザーがリスクベース ポリシーの影響を受けないようにするにはどうすればよいですか?

組織のリスクベース条件付きアクセスポリシーから B2B ユーザーを除外すると、B2B ユーザーがリスク評価によって影響を受けたり、ブロックされたりするのを防ぐことができます。 これらの B2B ユーザーを除外するには、組織のすべてのゲストユーザーを含むグループを Azure AD に作成します。 その後、このグループを、組み込みの Identity Protection ユーザー リスク ポリシーとサインイン リスク ポリシー、およびサインイン リスクを条件として使用している条件付きアクセス ポリシーの除外対象として追加します。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)