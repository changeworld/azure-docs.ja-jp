---
title: Azure の LUIS アプリで他の共同作成者と共同作業を行う | Microsoft Docs
description: Language Understanding (LUIS) アプリケーションで他の共同作成者と共同作業を行う方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225603"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Language Understanding (LUIS) アプリで他のユーザーと共同作業を行う  

LUIS アプリケーションで他のユーザーと共同作業を行うことができます。 

## <a name="owner-and-collaborators"></a>所有者とコラボレーター
アプリの所有者は 1 人ですが、複数のコラボレーターをアプリに設定することができます。 

## <a name="add-collaborator"></a>コラボレーターの追加

コラボレーターが LUIS アプリを編集できるようにするには、LUIS アプリの **[設定]** ページでコラボレーターのメール アドレスを入力し、**[Add collaborator]\(コラボレーターの追加\)** をクリックします。

![コラボレーターの追加](./media/luis-how-to-collaborate/add-collaborator.png)

* コラボレーターは、あなたがアプリで作業するのと同時に LUIS アプリにサインインして編集することができます。 <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* コラボレーターが他のコラボレーターを追加することはできません。

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-account-settings.md#azure-active-directory-tenant-user)」を参照してください。 

## <a name="set-application-as-public"></a>アプリケーションをパブリックとして設定する
詳細については、「[Public app endpoint access (パブリック アプリ エンドポイントへのアクセス)](luis-concept-security.md#public-app-endpoint-access)」を参照してください。

### <a name="transfer-of-ownership"></a>所有権の移転
現在、LUIS では、所有権の移転はサポートされていませんが、アプリをエクスポートし、そのアプリを別の LUIS ユーザーがインポートできます。 この 2 つのアプリケーション間では LUIS スコアに若干の違いがある可能性があります。 
