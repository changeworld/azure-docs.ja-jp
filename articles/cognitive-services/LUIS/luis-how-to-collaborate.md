---
title: Azure の LUIS アプリで他の共同作成者と共同作業を行う | Microsoft Docs
description: Language Understanding (LUIS) アプリケーションで他の共同作成者と共同作業を行う方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397790"
---
# <a name="how-to-manage-authors-and-collaborators"></a>作成者とコラボレーターを管理する方法 

LUIS アプリケーションで他のユーザーと共同作業を行うことができます。 

## <a name="owner-and-collaborators"></a>所有者とコラボレーター

アプリの作成者は 1 人 (所有者) ですが、コラボレーターは複数いてもかまいません。 

## <a name="add-collaborator"></a>コラボレーターの追加

コラボレーターが LUIS アプリを編集できるようにするには、LUIS アプリの **[設定]** ページでコラボレーターのメール アドレスを入力し、**[Add collaborator]\(コラボレーターの追加\)** をクリックします。 コラボレーターは、あなたがアプリで作業するのと同時に LUIS アプリにサインインして編集することができます。

![コラボレーターの追加](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>所有権の移転

現在、LUIS では、所有権の移転はサポートされていませんが、アプリをエクスポートし、そのアプリを別の LUIS ユーザーがインポートできます。 この 2 つのアプリケーション間では LUIS スコアに若干の違いがある可能性があります。 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー

LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。 

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。 

まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。 

次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。

テナント管理者は、LUIS にサインインしなくても、LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 

![アプリ Web サイトによる Azure Active Directory のアクセス許可](./media/luis-how-to-account-settings/tenant-permissions.png)

テナント管理者が特定のユーザーのみに LUIS の使用を許可する場合は、こちらの[ID ブログ](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)を参照してください。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>コラボレーター用の複数の電子メールを持つユーザー アカウント

LUIS アプリにコラボレーターを追加する場合は、コラボレーターがコラボレーターとして LUIS を使用するために必要な正確な電子メール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数のメール アカウントを交互に使用できますが、LUIS では、コラボレーターの一覧に指定されたメール アドレスを使用してサインインする必要があります。