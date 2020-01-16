---
title: 条件付きアクセス - 準拠しているデバイスが必須 - Azure Active Directory
description: 準拠しているデバイスを必須とするためにカスタムの条件付きアクセス ポリシーを作成します
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a18f0b1f83ae02b06344c332cfdd1cc093f37fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424891"
---
# <a name="conditional-access-require-compliant-devices"></a>条件付きアクセス:Require compliant devices (準拠しているデバイスが必須)

Microsoft Intune をデプロイした組織では、デバイスから返された情報を使用して、以下のようなコンプライアンス要件を満たすデバイスを識別することができます。

* ロック解除に PIN が必要
* デバイスの暗号化が必要
* オペレーティング システムの最小または最大バージョンが必要
* デバイスが脱獄または root 化されていないことが必要

このポリシー準拠情報は Azure AD に転送され、条件付きアクセスはそこで、リソースへのアクセスを許可するかブロックするかを決定できます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

リソースにアクセスするデバイスが組織の Intune 準拠ポリシーに準拠しているとマークされていることを要求する条件付きアクセス ポリシーを作成するには、次の手順に従います。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[完了]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択します。
   1. 特定のアプリケーションをポリシーから除外する必要がある場合は、 **[除外されたクラウド アプリの選択]** で **[除外]** タブから選択して **[選択]** を選びます。
   1. **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[デバイスは準拠としてマーク済みである必要があります]** を選択します。
   1. **[選択]** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

### <a name="known-behavior"></a>既知の動作

Windows 7、iOS、Android、macOS、および一部のサードパーティ製 Web ブラウザーでは、Azure AD によって、デバイスが Azure AD に登録されるときにプロビジョニングされたクライアント証明書を使用してデバイスが識別されます。 ユーザーは、ブラウザーで最初にサインインするときに、証明書の選択を求められます。 エンド ユーザーは、ブラウザーを引き続き使用する前に、この証明書を選択する必要があります。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-report-only.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[デバイス コンプライアンス ポリシーと Azure AD の連携](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
