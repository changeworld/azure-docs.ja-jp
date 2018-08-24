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
ms.openlocfilehash: ff349c480ca14b4242fb7597751b4eb6acb0ee78
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145851"
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

## <a name="requirements"></a>必要条件

* ドメイン コントローラーを含む Azure AD パスワード保護コンポーネントがインストールされているすべてのマシンは、Windows Server 2012 以降を実行している必要があります。
* ドメイン コントローラーを含む Azure AD パスワード保護コンポーネントがインストールされているすべてのマシンに、ユニバーサル C ランタイムがインストールされている必要があります。 これは、Windows Update 経由でマシンに対する修正プログラムの適用を完全に行うことで実現することをお勧めします。 それ以外の場合は、適切な OS 固有の更新パッケージをインストールできます。「[Windows での汎用の C ランタイムの更新プログラム](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)」を参照してください。
* 各ドメイン内の少なくとも 1 つのドメイン コントローラーと、Azure AD パスワード保護プロキシ サービスをホストする少なくとも 1 つのサーバーとの間に、ネットワーク接続が存在する必要があります。
* DC エージェント サービス ソフトウェアを実行しているすべての Active Directory ドメインは、sysvol レプリケーションに DFSR を使用する必要があります。
* Azure AD で Azure AD パスワード保護プロキシ サービスを登録するグローバル管理者アカウント。
* フォレスト ルート ドメイン内の Active Directory ドメイン管理者特権を持つアカウント。

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
* Azure AD パスワード保護は、リアルタイム ポリシー アプリケーション エンジンではありません。 パスワード ポリシー構成の変更と、すべてのドメイン コントローラーに変更が到達して適用されるまでに遅延が生じることがあります。

## <a name="next-steps"></a>次の手順

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises.md)