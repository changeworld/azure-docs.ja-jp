---
title: Azure Active Directory B2C のユーザー フロー バージョン
description: Azure Active Directory B2C で使用できるユーザー フローのバージョンについて説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/17/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: e6d259d86e9c38a7b5c2d2c15d3bd65ece259d92
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043873"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー バージョン

Azure Active Directory B2C (Azure AD B2C) のユーザー フローは、ユーザー ID エクスペリエンスを完全に記述する共通の[ポリシー](user-flow-overview.md)を設定するために役立ちます。 このようなエクスペリエンスには、サインアップ、サインイン、パスワード リセット、プロファイル編集が含まれます。 次の表は、Azure AD B2C で使用できるユーザー フローを示しています。

> [!IMPORTANT]
> ユーザー フローのバージョンを参照する方法が変更されました。 これまでは、V1 (実稼働可能) バージョンと V1.1 および V2 (プレビュー) バージョンが提供されていました。 今後は、ユーザー フローが 2 つのバージョンに統合されます。
>
>- **推奨** ユーザー フローは、一般提供されている、最新機能を備えた次世代のユーザー フローです。 これらには、レガシ **V1**、**V1.1**、**V2** バージョンのすべての機能が組み込まれています。 今後は、**推奨** ユーザー フローが保守および更新されます。 これらの新しい推奨ユーザー フローに移動すると、新機能がリリースされる度にアクセスできるようになります。
>- **標準 (レガシ)** ユーザー フロー (以前の **V1**) は、レガシ ユーザー フローです。 これらのバージョンのユーザー フローは保守または更新されないため、特定のビジネス ニーズがない限り使用しないことをお勧めします。
>
>パブリック クラウドでは、すべてのレガシ プレビュー ユーザー フロー (V1.1 および V2) は非推奨です。 *これらの変更は、Azure パブリック クラウドにのみ適用されます。その他の環境では、[レガシ ユーザー フローのバージョンの管理](user-flow-versions-legacy.md)が引き続き使用されます。*

## <a name="recommended-user-flows"></a>推奨ユーザー フロー

推奨ユーザー フローは、一般提供されている、最新機能を備えた次世代のユーザー フローです。 今後は、推奨ユーザー フローが保守および更新されます。

| ユーザー フロー | 説明 |
| --------- | ----------- |
| パスワードのリセット | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| プロファイル編集 | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](age-gating.md)</li><li>サインイン ページのカスタマイズ</li></ul> |
| サインアップ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| サインアップとサインイン | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>標準ユーザー フロー

標準ユーザー フロー (以前の **V1**) は、レガシ ユーザー フローです。 これらのバージョンのユーザー フローは今後更新されないため、特定のビジネス ニーズがない限り使用しないことをお勧めします。

| ユーザー フロー | 説明 |
| --------- | ----------- |
| パスワードのリセット | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| プロファイル編集 | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>サインインのブロック</li><li>パスワードの強制的なリセット</li><li>サインインしたままにする (KMSI)</ul><br>このユーザー フローではユーザー インターフェイスをカスタマイズできません。 |
| サインアップ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| サインアップとサインイン | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-recommended-user-flow"></a>推奨ユーザー フローに切り替える方法

レガシ バージョンのユーザー フローから **推奨** バージョンに切り替えるには、こちらの手順を実行します。

1. 「[チュートリアル:Azure Active Directory B2C 内にユーザー フローを作成する](tutorial-create-user-flows.md)」の手順に従って、新しいユーザー フロー ポリシーを作成します。 ユーザー フローの作成時に、**推奨** バージョンを選択します。

3. レガシ ポリシーで構成したものと同じ設定を使用して、新しいユーザー フローを構成します。

4. アプリケーションのサインイン URL を、新しく作成したポリシーに更新します。

5. ユーザー フローをテストし、動作していることを確認したら、次の手順に従ってレガシ ユーザー フローを削除します。
   1. Azure AD B2C テナントの概要メニューで、 **[ユーザー フロー]** を選択します。
   2. 削除するユーザー フローを見つけます。
   3. 最後の列で、コンテキスト メニュー ( **...** ) を選択し、 **[削除]** を選択します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>レガシ V2 と v1.1 ユーザー フローは今後も作成できますか?

レガシ V2 と v1.1 のバージョンに基づいて新しいユーザー フローを作成することはできませんが、現在使用しているレガシ V2 と v1.1 ユーザー フローは引き続き、読み取り、更新、および削除できます。

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>レガシ V2 と v1.1 ユーザー フローを引き続き使用する理由はありますか?

特にはありません。 **推奨** バージョンには、レガシ V2 および V1.1 バージョンと同じ機能が含まれます。 削除された内容はなく、実際には追加機能が含まれています。

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>レガシ V2 と V1.1 ポリシーから切り替えないと、アプリケーションにどのような影響がありますか?

レガシ V2 または V1.1 ユーザー フローを使用している場合、アプリケーションはこのバージョン管理の変更の影響を受けません。 ただし、今後新しい機能やポリシーの変更にアクセスできるようにするには、**推奨** バージョンに切り替える必要があります。

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft では、現在使用しているレガシ V2 または v1.1 のユーザー フロー ポリシーは引き続きサポートされますか?

パブリック クラウドでは、すべてのレガシ プレビュー ユーザー フロー (V1.1 および V2) は非推奨です。 *これらの変更は、Azure パブリック クラウドにのみ適用されます。その他の環境では、[レガシ ユーザー フローのバージョンの管理](user-flow-versions-legacy.md)が引き続き使用されます。*