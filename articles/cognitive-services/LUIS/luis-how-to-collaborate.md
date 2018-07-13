---
title: Azure の LUIS アプリで他の共同作成者と共同作業を行う | Microsoft Docs
description: Language Understanding (LUIS) アプリケーションで他の共同作成者と共同作業を行う方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: c0451f7621a3c18dbf365f3a03934924c030092f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376904"
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

## <a name="set-application-as-public"></a>アプリケーションをパブリックとして設定する
詳細については、「[Public app endpoint access (パブリック アプリ エンドポイントへのアクセス)](luis-concept-security.md#public-app-endpoint-access)」を参照してください。

### <a name="transfer-of-ownership"></a>所有権の移転
現在、LUIS では、所有権の移転はサポートされていませんが、アプリをエクスポートし、そのアプリを別の LUIS ユーザーがインポートできます。 この 2 つのアプリケーション間では LUIS スコアに若干の違いがある可能性があります。 
