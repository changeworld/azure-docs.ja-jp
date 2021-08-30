---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778849"
---
1. 作成した *my-api1* アプリケーション (App ID: 2) を選択して、その **[概要]** ページを開きます。

1. **[管理]** の **[API の公開]** を選択します。
1. **[アプリケーション ID URI]** の横にある **[設定]** リンクを選択します。 既定値 (GUID) を一意の名前 (たとえば、*tasks-api*) に置き換え、 **[保存]** を選択します。 
 
   Web アプリケーションで Web API のアクセス トークンを要求するときに、API に対して定義する各スコープのプレフィックスとしてこの URI を追加する必要があります。
1. **[この API で定義されるスコープ]** で、 **[スコープの追加]** を選択します。
1. API への読み取りアクセスを定義するスコープを作成するには、次の値を入力します。

    a. **スコープ名**: *tasks.read*  
    b. **管理者の同意の表示名**: *Read access to tasks API*  
    c. **管理者の同意の説明**: *Allows read access to the tasks API*

1. **[スコープの追加]** を選択します。

1. **[スコープの追加]** を選択し、次の値を入力して、API への書き込みアクセスを定義するスコープを追加します。 

    a. **スコープ名**: *tasks.write*  
    b. **管理者の同意の表示名**: *Write access to tasks API*  
    c. **管理者の同意の説明**: *Allows write access to the tasks API*
1. **[スコープの追加]** を選択します。