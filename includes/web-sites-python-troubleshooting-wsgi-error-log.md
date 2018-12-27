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
ms.openlocfilehash: 5169432af65a046465c9d1ced349687d6fdc8bb7
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/12/2018
ms.locfileid: "35394028"
---
アプリケーションの起動中に Python でエラーが発生した場合、簡単なエラー ページが返されます ("内部サーバー エラーが発生したため、ページを表示できません" など)。

Python アプリケーション エラーをキャプチャするには、次の手順を実行します。

1. Azure Portal の Web アプリで、**[設定]** を選択します。
2. **[設定]** タブで、**[アプリケーション設定]** を選択します。
3. **[アプリ設定]** の下で、次のキー/値ペアを入力します。
    * キー : WSGI_LOG
    * 値 : D:\home\site\wwwroot\logs.txt (ファイル名を入力)

wwwroot フォルダーの logs.txt ファイルにエラーが表示されるようになります。
