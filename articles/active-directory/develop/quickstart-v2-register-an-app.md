---
title: ポータルを使用した Azure AD v2.0 エンドポイントへのアプリケーション登録 | Microsoft Docs
description: v2.0 エンドポイントを使用し、アプリケーションを Microsoft に登録して Microsoft サービスへのサインインとアクセスを有効にする方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507613"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>v2.0 エンドポイントを使用してアプリケーションを登録する方法
個人 Microsoft アカウント (MSA) と職場または学校のアカウント (Azure AD) サインインの両方を受け付けるアプリを構築するには、最初に、アプリを Microsoft に登録する必要があります。 現時点では、Azure AD または MSA で利用している既存のアプリは使用できません。新しいアプリを作成してください。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。


## <a name="visit-the-microsoft-app-registration-portal"></a>Microsoft アプリ登録ポータルにアクセスする
最初に、[https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で Microsoft アプリ登録ポータルにアクセスします。 

Microsoft の個人または職場/学校アカウントのいずれかでサインインします。 いずれのアカウントもをお持ちでない場合は、新しい個人アカウントを新規登録します。

サインアップできましたか? おそらく空の Microsoft アプリの一覧が表示されているはずです。 これを変更してみましょう。

**[アプリの追加]** をクリックし、名前を付けます。 ポータルにより、アプリにグローバルに一意のアプリケーション ID が割り当てられます。これは、後ほどコードで使用します。 アプリに API を呼び出すためのアクセス トークンが必要なサーバー側コンポーネントが含まれる場合 (Office、Azure、独自の Web API など)、**アプリケーションのシークレット**を作成することもできます。

次に、アプリで使用する**プラットフォーム**を追加します。

* Web ベースのアプリの場合、サインイン メッセージを送信できる **リダイレクト URI** を指定します。
* モバイル アプリの場合、自動的に作成される既定のリダイレクト URI をメモしておきます。
* Web API の場合、Web API にアクセスするための既定のスコープが自動的に作成されます。 **[スコープの追加]** ボタンを使用して、スコープを追加することもできます。 **事前承認済みアプリケーション** フォームを使って、Web API を使用するように事前承認されたアプリケーションを追加することもできます。 

必要に応じて、**[プロファイル]** セクションでサインイン ページの外観をカスタマイズできます。 続行する前に、忘れずに **[保存]** をクリックします。

> [!NOTE]
> [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) を使用して作成したアプリケーションは、このポータルへのサインインに使用しているアカウントのホーム テナントに登録されます。 つまり、個人の Microsoft アカウントを使用して Azure AD テナントにアプリケーションを登録することはできません。 アプリケーションを特定のテナントに登録する場合は、そのテナントで作成したアカウントを使ってサインインしてください。


## <a name="build-a-quickstart-app"></a>クイックスタート アプリの構築
Microsoft アプリを構築したら、いずれかの v2.0 クイックスタート チュートリアルを実行します。 以下に、推奨事項をいくつか示します。

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

