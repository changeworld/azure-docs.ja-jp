---
title: Azure AD パスワード保護プレビューのトラブルシューティング
description: Azure AD パスワード保護プレビューの一般的なトラブルシューティングについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 7474027368949d5ad2202881ac68096fac2b8bd2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693906"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>更新:Azure AD パスワード保護のトラブルシューティング

|     |
| --- |
| Azure AD パスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Azure AD パスワード保護をデプロイした後、トラブルシューティングを必要とする場合があります。 この記事では、いくつかの一般的なトラブルシューティング手順を理解しやすいように詳しく説明しています。

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>脆弱なパスワードが期待どおりには拒否されない

これには、次に示すいくつかの原因が考えられます。

1. ご利用の DC エージェントによってポリシーがまだダウンロードされていません。 この問題の症状は、DC エージェント管理イベント ログ内の 30001 イベントに相当します。

    この問題の原因としては、次のようなことが考えられます。

    1. フォレストがまだ登録されていない
    2. プロキシがまだ登録されていない
    3. ネットワーク接続の問題により、プロキシ サービスが Azure と通信できない (HTTP プロキシ要件を確認してください)

2. パスワード ポリシーの適用モードがまだ [監査] に設定されています。 この場合は、Azure AD パスワード保護ポータルを使用してそのモードを [強制] に再構成します。 [パスワード保護の有効化](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)に関するページを参照してください。

3. パスワード ポリシーが無効にされています。 この場合は、Azure AD パスワード保護のポータルを使用してパスワード ポリシーが有効になるように再構成します。 [パスワード保護の有効化](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)に関するページを参照してください。

4. パスワード検証アルゴリズムが期待どおりに動作している可能性があります。 「[パスワードの評価方法](concept-password-ban-bad.md#how-are-passwords-evaluated)」を参照してください。

## <a name="directory-services-repair-mode"></a>ディレクトリ サービスの修復モード

ドメイン コントローラーがディレクトリ サービスの修復モードで起動された場合、DC エージェント サービスでそれが検出され、現在アクティブなポリシー構成に関係なく、すべてのパスワード検証または適用アクティビティが無効にされます。

## <a name="emergency-remediation"></a>緊急時の修復

DC エージェント サービスが問題の原因である状況が発生した場合、DC エージェント サービスは直ちにシャットダウンされる可能性があります。 DC エージェントのパスワード フィルター dll が実行中ではないサービスをまだ呼び出そうとすると、警告イベント (10012、10013) がログに記録されますが、その間にすべての受信パスワードが承認されます。 DC エージェント サービスは、必要に応じて Windows サービス コントロール マネージャーを使用してスタートアップの種類を "無効" に構成することもできます。

また、Azure AD パスワード保護ポータルで有効モードを [いいえ] に設定することで修復する方法もあります。 更新されたポリシーがダウンロードされたら、各 DC エージェント サービスが休止モードに入り、すべてパスワードが現状のままで受け入れられます。 詳細については、「[強制モード](howto-password-ban-bad-on-premises-operations.md#enforce-mode)」を参照してください。

## <a name="domain-controller-demotion"></a>ドメイン コントローラーの降格

DC エージェント ソフトウェアを実行中でもドメイン コントローラーを降格させることができます。 ただし、降格手続き中も DC エージェント ソフトウェアによって現在のパスワード ポリシーが継続的に適用されることに管理者は注意する必要があります。 新しいローカル管理者アカウントのパスワード (降格操作の一環で指定されます) は、他のパスワードと同様に検証されます。 DC の降格手続きの一環でローカル管理者アカウントに安全なパスワードを選択することをお勧めします。ただし、DC エージェント ソフトウェアによる新しいローカル管理者アカウントのパスワードの検証は、既存の降格操作手続きに影響する可能性があります。

降格が成功し、ドメイン コントローラーが再起動され、通常のメンバー サーバーとして改めて実行されると、DC エージェント ソフトウェアはパッシブ モードで動作するようになります。 このソフトウェアはいつでもアンインストールできます。

## <a name="removal"></a>削除

パブリック プレビュー ソフトウェアをアンインストールし、関連するすべての状態をドメインとフォレストからクリーンアップする場合、次の手順で実行できます。

> [!IMPORTANT]
> これらの手順は、順番に実行することが重要です。 プロキシ サービスのインスタンスを実行中のままにすると、定期的に serviceConnectionPoint オブジェクトが再作成されます。 DC エージェント サービスのインスタンスを実行中のままにすると、定期的に serviceConnectionPoint オブジェクトと sysvol 状態が再作成されます。

1. すべてのマシンからプロキシ ソフトウェアをアンインストールします。 この手順では、再起動する**必要はありません**。
2. すべてのドメイン コントローラーから DC エージェント ソフトウェアをアンインストールします。 この手順では、再起動する**必要があります**。
3. 各ドメイン名前付けコンテキストのすべてのプロキシ サービス接続ポイントを手動で削除します。 これらのオブジェクトの場所は、次の Active Directory PowerShell コマンドを使用して検出できます。

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 変数値の末尾のアスタリスク ("*") は省略しないでください。

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。 

4. 各ドメイン名前付けコンテキストに含まれるすべての DC エージェント接続ポイントを手動で削除します。 パブリック プレビュー ソフトウェアの展開の規模によっては、フォレスト内のドメイン コントローラーごとにこのようなオブジェクトが 1 つ存在することがあります。 そのオブジェクトの場所は、次の Active Directory PowerShell コマンドを使用して検出できます。

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。

   $keywords 変数値の末尾のアスタリスク ("*") は省略しないでください。

5. フォレストレベルの構成状態を手動で削除します。 フォレストの構成状態は、Active Directory 構成の名前付けコンテキストのコンテナーに保持されます。 次のように検出および削除できます。

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 次のフォルダーとそのすべての内容を手動で削除して、すべての sysvol 関連の状態を手動で削除します。

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   必要に応じて、特定のドメイン コントローラーのローカルでこのパスにアクセスすることもできます。既定の場所は次のようなパスになります。

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   sysvol 共有が既定以外の場所に設定されている場合は、別のパスになります。

## <a name="next-steps"></a>次の手順

[Azure AD パスワード保護についてよく寄せられる質問](howto-password-ban-bad-on-premises-faq.md)

グローバルおよびカスタムの禁止パスワード リストの詳細については、[不適切なパスワードの禁止](concept-password-ban-bad.md)に関する記事を参照してください。
