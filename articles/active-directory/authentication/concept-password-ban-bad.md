---
title: Azure AD でのパスワードの動的禁止
description: Azure AD でのパスワードの動的禁止を使用して環境の脆弱なパスワードを禁止する
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: dfeacb266d6aa6a43e49a39bd19c9699ef65ce82
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162016"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>組織内の不適切なパスワードを排除する

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

業界のリーダーたちは、複数の場所で同じパスワードを使用しないこと、パスワードを複雑にすること、Password123 のように単純なパスワードにしないことを指示します。 組織は、ユーザーがガイダンスに従うことをどうやって保証できるでしょうか。 また、共通のパスワードや、最近のデータ違反に含まれることがわかっているパスワードをユーザーが使用しないようにするにはどうすればよいでしょうか。

## <a name="global-banned-password-list"></a>グローバル禁止パスワード リスト

Microsoft は常にサイバー犯罪者の一歩先を行く対策を講じています。 そのため、Azure AD Identity Protection チームは、よく使用され、侵害されたパスワードを継続的に探しています。 また、いわゆるグローバル禁止パスワード リストに含まれるあまりにも一般的と見なされるパスワードをブロックしています。 サイバー犯罪者も同様の戦略を攻撃に使用しているため、Microsoft はこのリストの内容を一般公開していません。 これらの脆弱なパスワードは、Microsoft ユーザーにとって現実的な脅威になる前にブロックされます。 セキュリティに関する最新の取り組みについて詳しくは、「[マイクロソフト セキュリティ インテリジェンス レポート](https://www.microsoft.com/security/intelligence-report)」を参照してください。

## <a name="preview-custom-banned-password-list"></a>プレビュー: カスタムの禁止パスワード リスト

組織によっては、グローバル禁止パスワード リストの上に、Microsoft がカスタムの禁止パスワード リストと呼ぶ独自のカスタマイズを追加することで、セキュリティをさらに向上することもできます。 Contoso のような企業ユーザーは、さらにブランド名、企業固有の用語などの項目の変異形をブロックすることもできます。

カスタムの禁止パスワード リストとオンプレミス Active Directory 統合を有効にする機能は、Azure portal を使用して管理されます。

![Azure portal の [認証方法] でカスタムの禁止パスワード リストを変更する](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>オンプレミスのハイブリッド シナリオ

クラウドのみのアカウントを保護することは有用ですが、多くの組織では、オンプレミスの Windows Server Active Directory などのハイブリッド シナリオを維持しています。 オンプレミスの Windows Server Active Directory (プレビュー) エージェント用に Azure AD パスワード保護をインストールして、禁止パスワード リストを既存のインフラストラクチャに拡張することができます。 これで、オンプレミスのパスワードを変更、設定、またはリセットするユーザーと管理者は、クラウドのみのユーザーと同じパスワード ポリシーに準拠することが必須になります。

## <a name="how-does-the-banned-password-list-work"></a>禁止パスワード リストのしくみ

禁止パスワード リストは、文字列を小文字に変換し、編集距離が 1 以内の既知の禁止パスワードとあいまい一致で比較することで、リスト内のパスワードと照合します。

例: 組織で password という単語がブロックされている
   - ユーザーはパスワードを "P@ssword" に設定しようとしています。これは "password" に変換され、password の変異形なので、ブロックされます。
   - 管理者は、ユーザー パスワードを "Password123!" に設定しようとします。 これは "password123!" に変換されます。 これは password の変異形なのでブロックされます。

ユーザーが Azure AD パスワードをリセットまたは変更するたびに、このプロセスが実行され、禁止パスワード リストに含まれないことが確認されます。 このチェックは、セルフサービスのパスワードのリセット、パスワード ハッシュの同期、およびパススルー認証を使用するハイブリッド シナリオに含まれています。

## <a name="license-requirements"></a>ライセンスの要件

グローバル禁止パスワード リストの機能は、Azure Active Directory (Azure AD) のすべてのユーザーが利用できます。

カスタムの禁止パスワード リストには、Azure AD Basic ライセンスが必要です。

Windows Server Active Directory の Azure AD パスワード保護には、Azure AD Premium ライセンスが必要です。 

追加のライセンス情報 (コストを含む) については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

## <a name="what-do-users-see"></a>ユーザーに表示される画面

ユーザーがパスワードを禁止されるパスワードにリセットしようとすると、次のエラー メッセージが表示されます。

残念ながら、パスワードを簡単に推測できる単語、語句、またはパターンが含まれています。 別のパスワードで再実行してください。

## <a name="next-steps"></a>次の手順

* [カスタムの禁止パスワード リストを構成する](howto-password-ban-bad.md)
* [オンプレミスで Azure AD パスワード保護エージェントを有効にする](howto-password-ban-bad-on-premises.md)
