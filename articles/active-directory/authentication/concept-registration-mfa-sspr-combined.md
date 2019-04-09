---
title: Azure AD SSPR と MFA のための統合された登録 (プレビュー) - Azure Active Directory
description: Azure AD Multi-Factor Authentication とセルフサービスのパスワード リセットの登録 (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 536d26abf563f18ed7cec6668fcd1d4223f5a135
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370161"
---
# <a name="combined-security-information-registration-preview"></a>統合されたセキュリティ情報の登録 (プレビュー)

統合された登録の前、ユーザーは Azure Multi-Factor Authentication (MFA) とセルフサービスのパスワード リセット (SSPR) の認証方法を 2 つの異なるエクスペリエンスを通して登録しました。 ユーザーは Azure MFA と SSPR の両方に同様の方法が使用されることに困惑しましたが、各機能に個別に登録する必要がありました。 現在では、統合された登録を使用して、ユーザーは 1 回登録して Azure MFA と SSPR の両方の利点を得ることができます。

![ユーザーの登録済みのセキュリティ情報を示しているマイ プロファイル](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

新しいエクスペリエンスを有効にする前に、この管理者対象のドキュメントとユーザー対象のドキュメントを確認して、この機能とその影響を確実に理解するようにしてください。 ユーザー ドキュメントに基づいたトレーニングによってユーザーが新しいエクスペリエンスに対して準備できるようにし、ロールアウトの成功に役立ててください。

|     |
| --- |
| Azure Multi-Factor Authentication と Azure AD セルフサービスのパスワード リセットのための統合されたセキュリティ情報の登録は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

> [!IMPORTANT]
> ユーザーが元のプレビューと拡張された統合された登録エクスペリエンスの両方に対して有効になっている場合、そのユーザーには新しいエクスペリエンスが表示されます。 両方のエクスペリエンスが有効になっているユーザーには、新しい [My Profile] (マイ プロファイル) エクスペリエンスのみが表示されます。 新しい [My Profile] (マイ プロファイル) は統合された登録の外観と統一されており、ユーザーにシームレスなエクスペリエンスを提供します。 ユーザーは、[https://myprofile.microsoft.com](https://myprofile.microsoft.com) に移動することによって [My Profile] (マイ プロファイル) を表示できます。

[My Profile] (マイ プロファイル) ページは、そのページにアクセスしているコンピューター上の現在の言語設定に基づいてローカライズされます。 Microsoft は、以降のアクセスの試みが引き続き最後に使用された言語で描画されるように、利用された最新の言語をブラウザー キャッシュに格納します。 そのキャッシュをクリアすると、ページが再描画されます。 特定の言語を強制的に使用したい場合は、`de-DE` を対応する言語コードに設定して URL の最後に `?lng=de-DE` を追加すると、ページが強制的にその言語で描画されます。

![SSPR またはその他の追加のセキュリティ検証方法をセットアップする](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>集中型登録で使用できる方法

現時点では、統合された登録は次の方法と、それらの方法での次のアクションをサポートしています。

|   | Register | Change | 削除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | はい (最大 5) | いいえ  | はい |
| その他の認証アプリ | はい (最大 5) | いいえ  | はい |
| ハードウェア トークン | いいえ  | いいえ  | はい |
| 電話 | はい | はい | はい |
| Alternate phone | はい | はい | はい |
| 会社電話 | いいえ  | いいえ  | いいえ  |
| 電子メール | はい | はい | はい |
| セキュリティの質問 | はい | いいえ  | はい |
| アプリ パスワード | はい | いいえ  | はい |

> [!NOTE]
> アプリ パスワードは、MFA が適用されているユーザーのみが使用できます。 条件付きアクセス ポリシー経由で MFA が有効になっているユーザーはアプリ パスワードを使用できません。

ユーザーは、MFA の既定の方法として次のオプションを設定できます。

- Microsoft Authenticator – 通知
- 認証アプリまたはハードウェア トークン – コード
- 音声通話
- テキスト メッセージ

今後も引き続き Azure AD にさらに多くの認証方法を追加していくため、統合された登録でこれらの方法を使用できるようになります。

## <a name="combined-registration-modes"></a>統合された登録のモード

統合された登録には、中断と管理の 2 つの "モード" があります。

中断モードは、ウィザードに似たエクスペリエンスであり、ユーザーがサインイン時に自分のセキュリティ情報を登録または更新するときにユーザーに表示されます。

管理モードは、ユーザーのプロファイルの一部であり、ユーザーが自分のセキュリティ情報を管理できるようにします。

どちらのモードでも、ユーザーが以前に MFA に使用できる方法を登録している場合、ユーザーは自分のセキュリティ情報にアクセスするには MFA を実行する必要があります。

### <a name="interrupt-mode"></a>中断モード

統合された登録は、MFA と SSPR の両方のポリシーに従います (テナントで両方が有効になって場合)。 これらのポリシーは、ユーザーがサインイン中に登録を中断されるかどうか、および登録にどの方法を使用できるかを制御します。

ユーザーが自分のセキュリティ情報を登録または更新するよう求められる可能性があるいくつかのシナリオを次に示します。

* Identity Protection によって MFA の登録が適用されている: ユーザーは、サインイン中に登録するよう求められます。 ユーザーは MFA 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
* ユーザーごとの MFA によって MFA の登録が適用されている: ユーザーは、サインイン中に登録するよう求められます。 ユーザーは MFA 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
* 条件付きアクセスまたはその他のポリシーによって MFA の登録が適用されている: ユーザーは、MFA を必要とするリソースにアクセスするときに登録するよう求められます。 ユーザーは MFA 方法と SSPR 方法を登録します (ユーザーが SSPR に対して有効になっている場合)。
* SSPR の登録が適用されている: ユーザーは、サインイン中に登録するよう求められます。 ユーザーは SSPR 方法のみを登録します。
* SSPR の更新が適用されている: ユーザーは、管理者によって設定された間隔で自分のセキュリティ情報を確認する必要があります。ユーザーには自分の情報が表示され、[Looks good] (問題なし) を選択するか、または必要に応じて変更を行うことができます。

登録が適用されると、ユーザーには、MFA と SSPR の両方のポリシーに準拠するために必要な最小数の方法が安全性の高い順に表示されます。

例:

* ユーザーが SSPR に対して有効になっています。 SSPR ポリシーはリセットするための 2 つの方法を必要とし、モバイル アプリ コード、電子メール、および電話を有効にしています。
   * このユーザーは 2 つの方法を登録する必要があります。
      * 既定では、認証アプリと電話が表示されます。
      * ユーザーは、認証アプリまたは電話の代わりに電子メールを登録することを選択できます。

次のフローチャートは、サインイン中に登録を中断されたときにユーザーにどの方法が表示されるかを説明しています。

![結合されたセキュリティ情報のフロー チャート](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

MFA と SSPR の両方が有効になっている場合は、MFA の登録を適用することをお勧めします。

SSPR ポリシーでユーザーが定期的に自分のセキュリティ情報を確認する必要がある場合、ユーザーはサインイン中に中断され、自分が登録したすべての方法が表示されます。 ユーザーは、情報が最新の場合は [Looks good] (問題なし) を選択し、変更を行う場合は [情報の編集] を選択できます。

### <a name="manage-mode"></a>管理モード

ユーザーは [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動するか、または [My Profile] (マイ プロファイル) から [Security info] (セキュリティ情報) を選択することによって管理モードにアクセスできます。 そこから、ユーザーは方法の追加、既存の方法の削除または変更、既定の方法の変更などを実行できます。

## <a name="key-usage-scenarios"></a>主な使用シナリオ

### <a name="set-up-security-info-during-sign-in"></a>サインイン中にセキュリティ情報を設定する

管理者が登録を適用しています。

ユーザーは、必要なすべてのセキュリティ情報をまだ設定していない状態で Azure Portal に移動します。 自分のユーザー名とパスワードを入力した後、ユーザーはセキュリティ情報を設定するよう求められます。 ユーザーは次に、ウィザードに示されている手順に従って、必要なセキュリティ情報を設定します。 ユーザーは、設定によって許可される場合、既定で表示されるもの以外の方法を設定することを選択できます。 ウィザードの最後に、ユーザーは、設定した方法と MFA の既定の方法を確認します。 設定プロセスを完了するために、ユーザーは情報を確認し、Azure Portal に進みます。

### <a name="set-up-security-info-from-my-profile"></a>[My Profile] (マイ プロファイル) からセキュリティ情報を設定する

管理者は登録を適用していません。

必要なすべてのセキュリティ情報をまだ設定していないユーザーが [https://myprofile.microsoft.com](https://myprofile.microsoft.com) に移動します。 ユーザーは次に、左側のナビゲーションから **[Security info] (セキュリティ情報)** を選択します。 そこから、ユーザーは方法を追加することを選択し、自分が使用できるいずれかの方法を選択した後、手順に従ってその方法を設定します。 完了すると、今設定された方法がセキュリティ情報ページに表示されます。

### <a name="delete-security-info-from-my-profile"></a>[My Profile] (マイ プロファイル) からセキュリティ情報を削除する

少なくとも 1 つの方法を以前に設定しているユーザーが [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動します。 ユーザーは、以前に登録された方法のいずれかを削除することを選択します。 完了すると、その方法はセキュリティ情報ページに表示されなくなります。

### <a name="change-default-method-from-my-profile"></a>[My Profile] (マイ プロファイル) から既定の方法を変更する

MFA に使用できる少なくとも 1 つの方法を以前に設定しているユーザーが [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動します。 ユーザーは、現在の既定の方法を別の既定の方法に変更します。 完了すると、新しい既定の方法がセキュリティ情報ページに表示されます。

## <a name="next-steps"></a>次の手順

[テナントで統合された登録を有効にする](howto-registration-mfa-sspr-combined.md)

[MFA と SSPR に使用できる方法](concept-authentication-methods.md)

[セルフサービスのパスワード リセットを構成する](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication を構成する](howto-mfa-getstarted.md)
