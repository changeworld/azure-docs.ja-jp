---
author: Juliako
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: db2f6fe6175b8ff4d6371cb7074fb8c83af51c14
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386904"
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
