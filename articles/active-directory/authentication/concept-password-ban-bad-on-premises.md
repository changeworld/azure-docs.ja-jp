---
title: Azure AD パスワード保護プレビュー
description: Azure AD パスワード保護プレビューを使用してオンプレミスの Active Directory で脆弱なパスワードを禁止する
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: ca412e94f65c7e1ed9a547ec9dcabc62fac7d42f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741829"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>プレビュー: Windows Server Active Directory に Azure AD パスワード保護を適用する

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Azure AD パスワード保護は、Azure Active Directory (Azure AD) が提供するパブリック プレビュー段階の新機能です。組織のパスワード ポリシーを強化する機能です。 Azure AD パスワード保護のオンプレミス デプロイでは、グローバル禁止パスワード リストと Azure AD に格納されているカスタムの禁止パスワード リストの両方を使用して、Azure AD のクラウドベースの変更と同じチェックをオンプレミスで実行します。

Azure AD パスワード保護を構成するソフトウェア コンポーネントは 3 つあります。

* Azure AD パスワード保護プロキシ サービスは、現在の Active Directory フォレスト内のドメインに参加しているすべてのマシン上で実行されています。 要求はドメイン コントローラーから Azure AD に転送され、応答は Azure AD からドメイン コントローラーに返されます。
* Azure AD パスワード保護 DC エージェント サービスは、DC エージェント パスワード フィルター dll からパスワード検証要求を受け取り、現在使用できるパスワード ポリシーを使用して要求を処理し、結果 (合格/失敗) を返します。 このサービスは、定期的に (1 時間に 1 回) Azure AD パスワード保護プロキシ サービスを呼び出し、新しいバージョンのパスワード ポリシーを取得します。 Azure AD パスワード保護プロキシ サービスとの間の呼び出しの通信は、RPC (Remote Procedure Call) over TCP を介して処理されます。 新しいポリシーが取得されると、sysvol フォルダーに格納されます。これらのポリシーは、他のドメイン コントローラーにレプリケートすることができます。 また、DC エージェント サービスは、他のドメイン コントローラーが新しいパスワード ポリシーを書き込んだ場合の変更について sysvol フォルダーを監視します。適切な最近のポリシーが既に使用できる場合、Azure AD パスワード保護プロキシ サービスのチェックはスキップされます。
* DC エージェント パスワード フィルター dll は、オペレーティング システムからパスワード検証要求を受け取り、ドメイン コントローラーのローカルで実行されている Azure AD パスワード保護 DC エージェント サービスにそれらの要求を転送します。

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="license-requirements"></a>ライセンスの要件

グローバル禁止パスワード リストの機能は、Azure Active Directory (Azure AD) のすべてのユーザーが利用できます。

カスタムの禁止パスワード リストには、Azure AD Basic ライセンスが必要です。

Windows Server Active Directory の Azure AD パスワード保護には、Azure AD Premium ライセンスが必要です。

追加のライセンス情報 (コストを含む) については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

## <a name="download"></a>[ダウンロード]

Azure AD パスワード保護のために必要なインストーラーが 2 つあり、これらは [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)からダウンロードできます

## <a name="answers-to-common-questions"></a>よく寄せられる質問に対する回答

* ドメイン コントローラーからのインターネット接続は必要ありません。 Azure AD パスワード保護プロキシ サービスを実行しているマシンにのみ、インターネット接続が必要です。
* ドメイン コントローラーのネットワーク ポートは開かれません。
* Active Directory スキーマの変更は必要ありません。
* このソフトウェアは、既存の Active Directory コンテナーおよび serviceConnectionPoint スキーマ オブジェクトを使用します。
* 最小限の Active Directory ドメインまたはフォレスト機能レベル (DFL\FFL) の要件はありません。
* このソフトウェアは、保護する Active Directory ドメイン内にアカウントを作成せず、要求もしません。
* 増分デプロイはサポートされていますが、代わりに、ドメイン コントローラー エージェントがインストールされている場合にのみパスワード ポリシーが適用されます。
* パスワード保護を確実に適用するには、すべての DC に DC エージェントをインストールすることをお勧めします。 
* Azure AD パスワード保護は、リアルタイム ポリシー アプリケーション エンジンではありません。 パスワード ポリシー構成の変更と、すべてのドメイン コントローラーに変更が到達して適用されるまでに遅延が生じることがあります。

## <a name="next-steps"></a>次の手順

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)
