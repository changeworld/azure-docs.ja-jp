---
title: Azure Active Directory B2C のユーザー フロー バージョン | Microsoft Docs
description: Azure Active Directory B2C で使用できるユーザー フローのバージョンについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185622"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー バージョン

Azure Active Directory B2C (Azure AD B2C) のユーザー フローは、ユーザー ID エクスペリエンスを完全に記述する共通の[ポリシー](user-flow-overview.md)を設定するために役立ちます。 このようなエクスペリエンスには、サインアップ、サインイン、パスワード リセット、プロファイル編集が含まれます。 Azure AD B2C では、推奨されるユーザー フローとプレビュー ユーザー フローの両方のコレクションから選択できます。

新しいユーザー フローが新しいバージョンとして追加されます。 ユーザー フローが安定してから使用することをお勧めします。 ユーザー フローは、完全にテストされた場合に**推奨**とマークされます。 ユーザー フローは、推奨とマークされるまでプレビューで検討されます。 運用アプリケーションには推奨されるユーザー フローを使用してください。ただし、新しい機能を使用できるようになったときにテストする場合は、他のバージョンから選択します。 以前のバージョンの推奨されるユーザー フローは使用しないでください。

>[!IMPORTANT]
> ユーザー フローが**推奨**として識別されない限り、それは*プレビュー*と見なされます。 運用アプリケーションには、推奨されるユーザー フローのみを使用してください。

## <a name="v1"></a>V1

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット | はい | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| プロファイル編集 | はい | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| ROPC を使用したサインイン | いいえ | ローカル アカウントを持つユーザーがネイティブ アプリケーションで直接ログインできるようにします (ブラウザーは必要ありません)。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li></ul> |
| サインイン | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>サインインのブロック</li><li>パスワードの強制的なリセット</li><li>サインインしたままにする (KMSI)</ul><br>このユーザー フローではユーザー インターフェイスをカスタマイズできません。 |
| サインアップ | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| サインアップとサインイン | はい | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット v1.1 | いいえ | ユーザーが電子メールを確認した後に新しいパスワードを選択できるようにします (新しいページ レイアウトを使用できます)。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット v2 | いいえ | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| プロファイル編集 v2 | はい | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン v2 | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>サインイン ページのカスタマイズ</li></ul> |
| サインアップ v2 | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| サインアップとサインイン v2 | いいえ | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
