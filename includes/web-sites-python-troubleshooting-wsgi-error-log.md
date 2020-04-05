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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181779"
---
アプリケーションの起動中に Python でエラーが発生した場合、簡単なエラー ページが返されます ("内部サーバー エラーが発生したため、ページを表示できません" など)。

Python アプリケーション エラーをキャプチャするには、次の手順を実行します。

1. Azure Portal の Web アプリで、 **[設定]** を選択します。
2. **[設定]** タブで、 **[アプリケーション設定]** を選択します。
3. **[アプリ設定]** の下で、次のキー/値ペアを入力します。
    * キー:WSGI_LOG
    * 値:D:\home\site\wwwroot\logs.txt (任意のファイル名を入力)

wwwroot フォルダーの logs.txt ファイルにエラーが表示されるようになります。
