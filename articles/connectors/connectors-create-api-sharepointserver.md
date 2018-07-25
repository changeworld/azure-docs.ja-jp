---
title: Logic Apps で SharePoint Server コネクタを使用する | Microsoft Docs
description: ロジック アプリで SharePoint Server コネクタを使用する方法の概要について説明します
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 3ac3105231017cdbf8bfcf143b26451a68da5283
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868703"
---
# <a name="get-started-with-the-sharepoint-connector"></a>SharePoint コネクタの概要
SharePoint コネクタを使用すると、SharePoint リストを操作できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-sharepoint"></a>SharePoint への接続を作成する
SharePoint コネクタを使用するには、まず**接続**を作成してから、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |[はい] |SharePoint の資格情報を指定します |

**SharePoint** に接続するには、ID (ユーザー名とパスワード、スマート カードの資格情報など) を入力します。 認証されたら、ロジック アプリで SharePoint コネクタを使用できるようになります。 

ロジック アプリ デザイナーで、次の手順に従ってサインインし、ロジック アプリで使用する**接続**を作成します。

1. 検索ボックスに「SharePoint」と入力し、名前に SharePoint が含まれるすべてのエントリが返されるまで待ちます。   
   ![SharePoint の構成][1]  
2. **[SharePoint - When a file is created]\(SharePoint - ファイルの作成時\)** を選択します。   
3. **[Sign in to SharePoint]\(SharePoint にサインイン\)** をクリックします。   
   ![SharePoint の構成][2]    
4. SharePoint で認証するために、SharePoint 資格情報を入力してサインインします。   
   ![SharePoint の構成][3]     
5. 認証が完了すると、ロジック アプリにリダイレクトされます。SharePoint の **[When a file is created]\(ファイルの作成時\)** ダイアログを構成して、ロジック アプリを完成させます。          
   ![SharePoint の構成][4]  
6. ロジック アプリを完成させるために必要な他のトリガーやアクションを追加できます。   
7. メニュー (上部) の **[保存]** を選択して、作業内容を保存します。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sharepoint/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
