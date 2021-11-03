---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: 83ca731da109b945a654fd36406f4c523c957ef7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007574"
---
1. 作成した **my-api1** アプリケーション (**App ID: 2**) を選択して、その **[概要]** ページを開きます。

1. **[管理]** の **[API の公開]** を選択します。
1. **[アプリケーション ID URI]** の横にある **[設定]** リンクを選択します。 既定値 (GUID) を一意の名前 (たとえば、**tasks-api**) に置き換え、 **[保存]** を選択します。 
 
   Web アプリケーションで Web API のアクセス トークンを要求するときに、API に対して定義する各スコープのプレフィックスとしてこの URI を追加する必要があります。
1. **[この API で定義されるスコープ]** で、 **[スコープの追加]** を選択します。
1. API への読み取りアクセスを定義するスコープを作成するには:

    1. **[スコープ名]** に **tasks.read** を入力します。  
    1. **[管理者の同意の表示名]** で、「**Read access to tasks API**」を入力します。  
    1. **[管理者の同意の説明]** で、「**Allows read access to the tasks API**」を入力します。

1. **[スコープの追加]** を選択します。

1. **[Add a scope (スコープの追加)]** を選択し、API への書き込みアクセスを定義するスコープを追加します。 

    1. **[スコープ名]** に「**tasks.write**」を入力します。  
    1. **[管理者の同意の表示名]** に、「**Write access to tasks API**」を入力します。
    1. **[管理者の同意の説明]** に、「**Allows write access to the tasks API**」を入力します。
    
1. **[スコープの追加]** を選択します。
