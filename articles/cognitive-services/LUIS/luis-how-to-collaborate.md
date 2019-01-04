---
title: 他の人と共同作業を行う
titleSuffix: Language Understanding - Azure Cognitive Services
description: アプリの所有者は、コラボレーターをアプリに追加できます。 これらの共同作業者は、アプリのモデル作成、トレーニング、および発行が可能です。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: d1db8974ce134b50340db500c9ea1b00126fe10a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086421"
---
# <a name="how-to-manage-authors-and-collaborators"></a>作成者とコラボレーターを管理する方法 

アプリの所有者は、コラボレーターをアプリに追加できます。 これらの共同作業者は、アプリのモデル作成、トレーニング、および発行が可能です。 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>コラボレーターの追加

アプリの作成者は 1 人 (所有者) ですが、コラボレーターは複数いてもかまいません。 コラボレーターが LUIS アプリを編集できるようにするには、コラボレーターの一覧に、LUIS ポータルへのアクセスに使用する電子メールを追加する必要があります。 追加されると、LUIS ポータルにアプリが表示されます。

1. 右上のメニューから **[管理]** を選択してから、左側のメニューで **[コラボレーター]** を選択します。

2. ツールバーから **[Add Collaborator]\(コラボレーターの追加)** を選択します。

    [![コラボレーターの追加](./media/luis-how-to-collaborate/add-collaborator.png "コラボレーターの追加")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. コラボレーターが LUIS ポータルにサインインするために使用するメール アドレスを入力します。

    ![コラボレーターのメール アドレスを追加する](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>所有権の移転

現在、LUIS では、所有権の移転はサポートされていませんが、アプリをエクスポートし、そのアプリを別の LUIS ユーザーがインポートできます。 この 2 つのアプリケーション間では LUIS スコアに若干の違いがある可能性があります。 

## <a name="azure-active-directory-resources"></a>Azure Active Directory リソース

組織で Azure Active Directory (Azure AD) を使用する場合、LUIS には、ユーザーが LUIS を使用するときにユーザーに関する情報にアクセスするためのアクセス許可が必要です。 LUIS に必要なリソースはごくわずかです。 

以下に関する管理者の同意など、管理者の同意があるか、管理者の同意が不要のアカウントでサインアップしようとしたときに、詳細な説明が表示されます。

* 組織のアカウントでアプリにサインインし、アプリにユーザーのプロファイルを読み取らせることを許可します。 基本的な会社情報の読み取りもアプリに許可します。
* ユーザーが現在アプリを使用していないときでも、ユーザーのデータの参照と更新をアプリに許可します。

最初のアクセス許可では、ユーザー ID、電子メール、名前などの基本的なプロファイル データを読み取るアクセス許可を LUIS に与えます。 2 つ目のアクセス許可は、ユーザーのアクセス トークンを更新するために必要です。

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー

LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。 

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。 

まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。 

次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。

テナント管理者は、LUIS にサインインしなくても、LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 

![アプリ Web サイトによる Azure Active Directory のアクセス許可](./media/luis-how-to-collaborate/tenant-permissions.png)

テナント管理者が特定のユーザーのみに LUIS の使用を許可する場合は、こちらの[ID ブログ](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)を参照してください。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>コラボレーター用の複数の電子メールを持つユーザー アカウント

LUIS アプリにコラボレーターを追加する場合は、コラボレーターがコラボレーターとして LUIS を使用するために必要な正確な電子メール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数のメール アカウントを交互に使用できますが、LUIS では、コラボレーターの一覧に指定されたメール アドレスを使用してサインインする必要があります。

