---
title: Azure AD パスワード保護プレビュー
description: Azure AD パスワード保護プレビューを使用してオンプレミスの Active Directory で脆弱なパスワードを禁止する
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415750"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>更新:Windows Server Active Directory に Azure AD パスワード保護を適用する

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Azure AD パスワード保護は、Azure Active Directory (Azure AD) が提供するパブリック プレビュー段階の新機能です。組織のパスワード ポリシーを強化する機能です。 Azure AD パスワード保護のオンプレミス デプロイでは、グローバル禁止パスワード リストと Azure AD に格納されているカスタムの禁止パスワード リストの両方を使用して、Azure AD のクラウドベースの変更と同じチェックをオンプレミスで実行します。

## <a name="design-principles"></a>設計原則

Active Directory の Azure AD パスワード保護は、次の原則を考慮して設計されています。

* ドメイン コントローラーはインターネットと直接通信する必要がありません
* ドメイン コントローラーで新しいネットワーク ポートが開かれません。
* Active Directory スキーマの変更は必要ありません。 このソフトウェアは、既存の Active Directory コンテナーおよび serviceConnectionPoint スキーマ オブジェクトを使用します。
* 最小限の Active Directory ドメインまたはフォレスト機能レベル (DFL\FFL) は必要ありません。
* このソフトウェアは、保護する Active Directory ドメイン内にアカウントを作成せず、要求もしません。
* ユーザーのクリア テキスト パスワードは、(パスワード検証操作時や他のいかなる時にも関係なく) ドメイン コントローラーを離れることはありません。
* 増分デプロイはサポートされていますが、代わりに、ドメイン コントローラー エージェントがインストールされている場合にのみパスワード ポリシーが適用されます。
* ユビキタスなパスワード保護のセキュリティを確実に適用するには、すべての DC に DC エージェントをインストールすることをお勧めします。

## <a name="architectural-diagram"></a>アーキテクチャ図

オンプレミスの Active Directory 環境で Azure AD パスワード保護をデプロイする前に、基になる設計と機能の概念を理解することが重要です。 次の図は、Azure AD パスワード保護のコンポーネントの連携のしくみを示しています。

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

上の図は、Azure AD パスワード保護を構成する基本的な 3 つのソフトウェア コンポーネントを示しています。

* Azure AD パスワード保護プロキシ サービスは、現在の Active Directory フォレスト内のドメインに参加しているすべてのマシン上で実行されています。 その主な目的は、パスワード ポリシーのダウンロード要求をドメイン コントローラーから Azure AD に転送し、Azure AD からドメイン コントローラーに応答を返すことです。
* Azure AD パスワード保護 DC エージェント パスワード フィルター dll は、オペレーティング システムからユーザー パスワード検証要求を受け取り、ドメイン コントローラーのローカルで実行されている Azure AD パスワード保護 DC エージェント サービスにそれらの要求を転送します。
* Azure AD パスワード保護 DC エージェント サービスは、DC エージェント パスワード フィルター dll からパスワード検証要求を受け取り、現在の (ローカルで使用できる) パスワード ポリシーを使用して要求を処理し、結果 (合格/失敗) を返します。

## <a name="theory-of-operations"></a>操作の理論

上の図と設計の原則がある場合、Azure AD パスワード保護は実際にどのように機能するのでしょうか。

各 Azure AD パスワード保護プロキシ サービスは、Active Directory に serviceConnectionPoint オブジェクトを作成することによって、自己をフォレスト内のドメイン コント ローラーにアドバタイズします。

各 Azure AD パスワード保護 DC エージェント サービスは、Active Directory に serviceConnectionPoint オブジェクトも作成します。 ただしこれは、主にレポートと診断に使用されます。

Azure AD パスワード保護 DC エージェント サービスは、Azure AD からの新しいパスワード ポリシーのダウンロードの開始を担当します。 最初の手順は、フォレストで、プロキシ serviceConnectionPoint オブジェクトをクエリすることによって、Azure AD パスワード保護プロキシ サービスを見つけることです。 使用可能なプロキシ サービスが見つかると、DC エージェント サービスからプロキシ サービスに、パスワード ポリシー ダウンロード要求が送信され、さらにそれが Azure AD に送信され、応答が DC エージェント サービスに返されます。 Azure AD から新しいパスワード ポリシーを 受信すると、DC エージェント サービスは、そのドメイン sysvol 共有のルートにある専用フォルダーにポリシーを格納します。 さらに、DC エージェント サービスは、ドメイン内の他の DC エージェント サービスから新しいポリシーがレプリケートされた場合にこのフォルダーを監視します。

Azure AD パスワード保護 DC エージェント サービスはサービスの起動時に常に新しいポリシーを要求します。 DC エージェント サービスの起動後、定期的 (1 時間ごと) に現在のローカルで使用可能なポリシーの有効期間が確認されます。現在のポリシーが 1 時間よりも古い場合、DC エージェント サービスは上記のように Azure AD から新しいポリシーを要求しますが、そうでない場合、DC エージェントは現在のポリシーを使用し続けます。

Azure AD パスワード保護 DC エージェント サービスは、TCP を介した RPC (リモート プロシージャ コール) を使用して Azure AD パスワード保護プロキシ サービスと通信します。 プロキシ サービスは、(構成に従って) 動的または静的 RPC ポートでこれらの呼び出しをリッスンします。

Azure AD パスワード保護 DC エージェントは、ネットワークで使用可能なポートでリッスンせず、プロキシ サービスは、DC エージェント サービスの呼び出しを試みません。

Azure AD パスワード保護プロキシ サービスはステートレスです。Azure からダウンロードされたポリシーやその他の状態をキャッシュしません。

Azure AD パスワード保護 DC エージェント サービスは、最新のローカルで使用可能なパスワード ポリシーを使用して、ユーザーのパスワードのみを評価します。 ローカル DC に使用可能なパスワード ポリシーがない場合は、パスワードが自動的に承認され、イベント ログ メッセージが記録され、管理者に警告されます。

Azure AD パスワード保護は、リアルタイム ポリシー アプリケーション エンジンではありません。 パスワード ポリシー構成の変更が Azure AD で行われ、すべてのドメイン コントローラーに変更が到達して適用されるまでに遅延が生じることがあります。

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Azure AD パスワード保護のフォレスト/テナント バインディング

Active Directory フォレスト内の Azure AD パスワード保護のデプロイには、Active Directory フォレスト、およびすべてのデプロイ済みの Azure AD パスワード保護プロキシ サービスの Azure AD への登録が必要です。 このような登録 (フォレストとプロキシ) はともに、登録時に使用される資格情報によって暗黙的に識別される特定の Azure AD テナントと関連付けられます。 Azure AD パスワード保護ポリシーがダウンロードされると、常にこのテナントに固有になります (つまり、そのポリシーは常に、Microsoft のグローバルな禁止パスワード リストとテナントごとのカスタムの禁止パスワード リストの組み合わせになります)。 別の Azure AD テナントにバインドされるフォレスト内の別のドメインまたはプロキシを構成するようにサポートされていません。

## <a name="license-requirements"></a>ライセンスの要件

グローバル禁止パスワード リストの機能は、Azure Active Directory (Azure AD) のすべてのユーザーが利用できます。

カスタムの禁止パスワード リストには、Azure AD Basic ライセンスが必要です。

Windows Server Active Directory の Azure AD パスワード保護には、Azure AD Premium ライセンスが必要です。

追加のライセンス情報 (コストを含む) については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

## <a name="download"></a>ダウンロード

Azure AD パスワード保護のために必要な 2 つのエージェント インストーラー。これらは [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)からダウンロードできます

## <a name="next-steps"></a>次の手順

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)
