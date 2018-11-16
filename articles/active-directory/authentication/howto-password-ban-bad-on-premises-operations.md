---
title: Azure AD パスワード保護プレビューの操作とレポート
description: Azure AD パスワード保護プレビューのデプロイ後の操作とレポート
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 92c8de0961f64eea8eef830ad99c7baa268099d9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007588"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>プレビュー: Azure AD パスワード保護の操作手順

|     |
| --- |
| Azure AD パスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

[Azure AD パスワード保護のインストール](howto-password-ban-bad-on-premises-deploy.md)をオンプレミスで完了した後、Azure portal で構成する必要があるいくつかの項目があります。

## <a name="configure-the-custom-banned-password-list"></a>カスタムの禁止パスワード リストを構成する

組織の禁止パスワード リストをカスタマイズする手順については、「[カスタムの禁止パスワード リストを構成する](howto-password-ban-bad-configure.md)」の記事のガイダンスに従います。

## <a name="enable-password-protection"></a>パスワード保護を有効にする

1. [Azure portal](https://portal.azure.com) にサインインし、**[Azure Active Directory]**、**[認証方法]**、**[Password protection (Preview)]\(パスワード保護 (プレビュー)\)** の順に参照します。
1. **[Windows Server Active Directory のパスワード保護を有効にする]** を **[はい]** に設定します
1. [デプロイ ガイド](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)のページで説明しているように、最初に **[モード]** を **[監査]** に設定することをお勧めします
   * 機能に問題がなければ、**[モード]** を **[強制]** に切り替えることができます
1. **[保存]**

![Azure portal で Azure AD パスワード保護コンポーネントを有効にする](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>監査モード

監査モードは、"what-if" モードでソフトウェアを実行する方法として用意されています。 各 DC エージェント サービスは、受信したパスワードを現在アクティブなポリシーに従って評価します。 現在のポリシーが監査モードに構成されている場合、"不正な" パスワードは、イベント ログ メッセージが生成される原因となりますが受け入れられます。 これは監査モードと強制モードの唯一の違いであり、他のすべての操作は同じように実行されます。

> [!NOTE]
> 初期のデプロイとテストは常に監査モードで開始することをお勧めします。 イベント ログのイベントを監視して、強制モードが有効になった後で既存の運用プロセスが影響を受けるかどうかを予想してみる必要があります。

## <a name="enforce-mode"></a>強制モード

強制モードは最終構成として用意されています。 前述の監査モードと同じように、各 DC エージェント サービスは、受信したパスワードを現在アクティブなポリシーに従って評価します。 ただし、強制モードが有効になっている場合、ポリシーに従って安全でないと見なされるパスワードは拒否されます。

強制モードで Azure AD パスワード保護 DC エージェントによってパスワードが拒否された場合、エンド ユーザーの目に見える影響は、自分のパスワードが従来のオンプレミス パスワードの複雑さの適用によって拒否された場合と同じです。 たとえば、Windows ログオン\パスワードの変更画面で、次の従来のエラー メッセージがユーザーに表示されることがあります。

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

このメッセージは、いくつかの考えられる結果の一例にすぎません。 具体的なエラー メッセージは、実際のソフトウェアや、安全でないパスワードを設定しようとするシナリオによって異なる可能性があります。

影響を受けるエンド ユーザーは、IT スタッフと協力して、新しい要件を理解し、セキュリティで保護されたパスワードを選択できるようになる必要があります。

## <a name="enable-mode"></a>有効モード

通常、この設定は既定の有効 (はい) 状態のままにしておく必要があります。 この設定を無効 (いいえ) に設定すると、デプロイされているすべての Azure AD パスワード保護 DC エージェントは休止モードになります。このモードでは、すべてのパスワードがそのまま受け入れられ、検証アクティビティはまったく実行されません (たとえば、監査イベントでさえ実行されません)。

## <a name="usage-reporting"></a>使用状況レポート

`Get-AzureADPasswordProtectionSummaryReport` コマンドレットを使用して、アクティビティの概要ビューを生成できます。 このコマンドレットの出力例は次のとおりです。

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

コマンドレット レポートのスコープは、–Forest、-Domain、–DomainController のいずれかのパラメーターの使用の影響を受けることがあります。 パラメーターを指定しないと暗黙的に –Forest が指定されます。

> [!NOTE]
> このコマンドレットは、各ドメイン コントローラーに対して PowerShell セッションを開くことで機能します。 成功させるには、各ドメイン コントローラーで PowerShell リモート セッションのサポートを有効にする必要があります。また、クライアントには十分な特権が必要です。 PowerShell のリモート セッション要件の詳細については、PowerShell ウィンドウで 'Get-Help about_Remote_Troubleshooting' を実行します。

> [!NOTE]
> このコマンドレットは、各 DC エージェント サービスの管理イベント ログに対するクエリをリモートで実行することで動作します。 イベント ログに多数のイベントが含まれている場合、コマンドレットの完了に時間がかかることがあります。 また、大規模なデータ セットの一括ネットワーク クエリがドメイン コントローラーのパフォーマンスに影響を与える可能性があります。 そのため、このコマンドレットは、運用環境では慎重に使用する必要があります。

## <a name="dc-agent-discovery"></a>DC エージェントの検出

`Get-AzureADPasswordProtectionDCAgent` コマンドレットを使用すると、ドメインまたはフォレスト内で実行されているさまざまな DC エージェントに関する基本的な情報を表示できます。 この情報は、実行中の DC エージェント サービスによって登録された serviceConnectionPoint オブジェクトから取得されます。 このコマンドレットの出力例は次のとおりです。

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

さまざまなプロパティは、各 DC エージェント サービスによって約 1 時間ごとに更新されます。 さらに、データは Active Directory レプリケーションの待機時間の影響を受けます。

コマンドレットのクエリの範囲は、-Forest パラメーターまたは -Domain パラメーターの使用の影響を受ける可能性があります。

## <a name="next-steps"></a>次の手順

[Azure AD パスワード保護のトラブルシューティングと監視](howto-password-ban-bad-on-premises-troubleshoot.md)
