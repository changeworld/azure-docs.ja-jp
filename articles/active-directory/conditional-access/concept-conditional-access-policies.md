---
title: 条件付きアクセス ポリシーの構築 - Azure Active Directory
description: 条件付きアクセス ポリシーを構築するために使用できるすべてのオプションとその意味
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 997b3ec9b784b8b52b826b526102a97bce7d3286
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301057"
---
# <a name="building-a-conditional-access-policy"></a>条件付きアクセス ポリシーの構築

「[条件付きアクセスとは](overview.md)」の記事で説明されているように、条件付きアクセス ポリシーは、**割り当て** と **アクセス制御** の if then ステートメントです。 条件付きアクセス ポリシーではシグナルをまとめ、決定を行い、組織のポリシーを適用します。

組織ではこれらのポリシーをどのように作成しますか? 必要なものは何ですか? どのように適用しますか?

![条件付きアクセス (シグナル + 決定 + 適用 = ポリシー)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

複数の条件付きアクセス ポリシーは、いつでも個々のユーザーに適用される可能性があります。 この場合、適用されるすべてのポリシーを満たす必要があります。 たとえば、あるポリシーで多要素認証（MFA）が必要で、別のポリシーで準拠デバイスが必要な場合、MFAを完了し、準拠デバイスを使用する必要があります。 すべての割り当ては、論理的に **AND** 処理されます。 複数の割り当てを構成している場合は、ポリシーをトリガーするためにすべての割り当てが満たされている必要があります。

"選択したコントロールのいずれかが必要です" というポリシーが選択されている場合は、定義された順序で、ポリシーの要件が満たされるとすぐにアクセスが許可されることを示すメッセージが表示されます。

すべてのポリシーは 2 つのフェーズで適用されます。

- フェーズ 1:セッション詳細の収集 
   - ポリシーの評価に必要なネットワークの場所やデバイス ID などのセッションの詳細を収集します。 
   - ポリシー評価のフェーズ 1 は、有効になっているポリシーと [レポート専用モード](concept-conditional-access-report-only.md) のポリシーで発生します。
- フェーズ 2:適用 
   - フェーズ1で収集したセッションの詳細を使用して、満たされていない要件を特定します。 
   - アクセスをブロックするように構成されているポリシーがブロックの許可を持つ場合、強制はここで停止し、ユーザーはブロックされます。 
   - ユーザーは、ポリシーが満たされるまで、フェーズ1で満たされなかった付与制御要件を次の順序で完了するように求められます。  
      - 多要素認証 
      - 承認されたクライアント アプリ/アプリ保護ポリシー 
      - マネージド デバイス (準拠または Hybrid Azure AD Join) 
      - 使用条件 
      - カスタム コントロール  
   - すべての許可の制御が満たされたら、セッション制御を適用します (アプリによって適用、Microsoft Defender for Cloud Apps、トークンの有効期間) 
   - ポリシー評価のフェーズ 2 は、すべての有効になっているポリシーで発生します。 

## <a name="assignments"></a>代入

割り当て部分では、条件付きアクセス ポリシーについて、ユーザー、内容、および場所を制御します。

### <a name="users-and-groups"></a>ユーザーおよびグループ

[ユーザーとグループ](concept-conditional-access-users-groups.md)では、ポリシーに対して含めるまたは除外するユーザーを割り当てます。 この割り当てには、すべてのユーザー、特定のユーザー グループ、ディレクトリ ロール、または外部のゲスト ユーザーを含めることができます。 

### <a name="cloud-apps-or-actions"></a>クラウド アプリまたはアクション

[クラウド アプリまたはアクション](concept-conditional-access-cloud-apps.md)では、ポリシーの対象となるクラウド アプリケーション、ユーザー アクション、または認証コンテキストを含めたり除外したりすることができます。

### <a name="conditions"></a>条件

ポリシーには複数の[条件](concept-conditional-access-conditions.md)を含めることができます。

#### <a name="sign-in-risk"></a>サインイン リスク

[Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) を使用する組織では、生成されたリスク検出が条件付きアクセス ポリシーに影響を与える可能性があります。

#### <a name="device-platforms"></a>デバイス プラットフォーム

複数のデバイス オペレーティング システム プラットフォームを使用する組織では、さまざまなプラットフォームに特定のポリシーを適用することを望む場合があります。 

デバイス プラットフォームの計算に使用される情報は、変更可能なユーザー エージェント文字列など、未確認のソースから取得されます。

#### <a name="locations"></a>場所

場所データは、IP の地理位置情報データによって提供されます。 管理者は場所を定義することを選択したり、組織のネットワークの場所のように信頼済みとして一部をマークすることを選択できます。

#### <a name="client-apps"></a>クライアント アプリ

既定では、新しく作成されたすべての条件付きアクセスポリシーは、クライアントアプリの条件が構成されていない場合でも、すべてのクライアントアプリの種類に適用されます。

クライアント アプリの条件の動作は、2020 年 8 月に更新されました。 既存の条件付きアクセスポリシーがある場合は、変更されません。 ただし、[既存のポリシーを使用する] を選択すると、[構成の切り替え] が削除され、ポリシーが適用されるクライアントアプリが選択されます。

#### <a name="device-state"></a>デバイスの状態

このコントロールは、ハイブリッド Azure AD 参加済みであるか、Intune で準拠としてマークされているデバイスを除外するために使用されます。 この除外は、アンマネージド デバイスをブロックするために行うことができます。 

#### <a name="filters-for-devices-preview"></a>デバイスのフィルター (プレビュー)

このコントロールを使用すると、ポリシー内の属性に基づいて特定のデバイスを対象にすることができます。

## <a name="access-controls"></a>アクセス制御

条件付きアクセス ポリシーのアクセス制御部分では、ポリシーの適用方法を制御します。

### <a name="grant"></a>Grant

[付与](concept-conditional-access-grant.md) によって、管理者は、アクセスをブロックまたは許可するポリシーを適用する手段を得ることができます。

#### <a name="block-access"></a>アクセスのブロック

アクセスのブロックでは、指定された割り当てでのアクセスをブロックするだけです。 ブロック コントロールは強力なものであるため、適切な知識を習得したうえで扱う必要があります。

#### <a name="grant-access"></a>アクセス権の付与

許可コントロールでは、1 つまたは複数のコントロールの適用をトリガーできます。 

- 多要素認証 (Azure AD Multi-Factor Authentication) を要求する
- デバイスが準拠としてマーク済みであることを必要とする (Intune)
- ハイブリッド Azure AD 参加済みのデバイスを必要とする
- 承認済みクライアント アプリを必須にする
- アプリの保護ポリシーを必須にする
- パスワードの変更を必須とする
- 利用規約が必須

管理者は、次のオプションを使用して、前のコントロールのいずれか、または選択したすべてのコントロールを必要とすることを選択できます。 複数のコントロールの場合の既定値は、すべて必要です。

- 選択したコントロールすべてが必要 (コントロールとコントロール)
- 選択したコントロールのいずれかが必要 (コントロールまたはコントロール)

### <a name="session"></a>Session

[セッション コントロール](concept-conditional-access-session.md)でエクスペリエンスを制限できます。 

- アプリによって適用される制限を使用する
   - 現在、Exchange Online と SharePoint Online でのみ機能します。
      - デバイス情報を渡して、フル アクセスまたは制限付きアクセスを許可するエクスペリエンスを制御できるようにします。
- アプリの条件付きアクセス制御を使用する
   - Microsoft Defender for Cloud Apps からのシグナルを使用して、次のようなことを行います。 
      - 機密ドキュメントのダウンロード、切り取り、コピー、および印刷をブロックする。
      - 危険なセッションの動作を監視する。
      - 機密ファイルのラベル付けを必要とする。
- サインインの頻度
   - 先進認証の既定のサインイン頻度を変更する機能。
- 永続的ブラウザー セッション
   - ユーザーが、ブラウザー ウィンドウを閉じてから再度開いた後でもサインインした状態を維持できるようにします。

## <a name="simple-policies"></a>単純なポリシー

条件付きアクセス ポリシーを適用するには、少なくとも以下のものが含まれている必要があります。

- ポリシーの **名前**。
- **代入**
   - ポリシーを適用する **ユーザーまたはグループあるいはその両方**。
   - ポリシーを適用する **クラウド アプリまたはアクション**。
- **アクセス制御**
   - **許可** または **ブロック** コントロール

![空の条件付きアクセス ポリシー](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

記事「[一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)」に、ほとんどの組織にとって役に立つと思われるいくつかのポリシーが含まれています。

## <a name="next-steps"></a>次の手順

[条件付きアクセス ポリシーを作成する](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[クラウドベースの Azure AD Multi-Factor Authentication のデプロイの計画](../authentication/howto-mfa-getstarted.md)

[Intune でのデバイス コンプライアンスの管理](/intune/device-compliance-get-started)

[Microsoft Defender for Cloud Apps と条件付きアクセス](/cloud-app-security/proxy-intro-aad)
