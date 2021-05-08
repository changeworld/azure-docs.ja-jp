---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "67181779"
---
アプリケーションの起動時に Python でエラーが発生した場合は、単純なエラー ページ (例: "内部サーバー エラーが発生したため、ページを表示できません") のみが返されます。

Python アプリケーション エラーをキャプチャするには、次の手順を実行します。

1. Azure Portal の Web アプリで、**[設定]** を選択します。
2. **[設定]** タブで、**[アプリケーション設定]** を選択します。
3. **[アプリ設定]** の下で、次のキー/値ペアを入力します。
    * キー : WSGI_LOG
    * 値 : D:\home\site\wwwroot\logs.txt (ファイル名を入力)

wwwroot フォルダーの logs.txt ファイルにエラーが表示されるようになります。
