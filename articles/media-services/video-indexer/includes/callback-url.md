---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994497"
---
以下のイベントについてのユーザーへの通知 (POST 要求を使用) に使われる URL です。

- インデックス状態の変更: 
    - プロパティ:    
    
        |名前|説明|
        |---|---|
        |id|ビデオ ID|
        |state|ビデオの状態|  
    - 例: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- ビデオで特定された人物:
  - Properties
    
      |名前|説明|
      |---|---|
      |id| ビデオ ID|
      |faceId|ビデオ インデックスに表示される顔 ID|
      |knownPersonId|顔モデル内で一意の人物 ID|
      |personName|人物の名前|
        
    - 例: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 
