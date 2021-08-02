---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: cd6bddb6988555b336cb920c481fe675e9f7a276
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073079"
---
1. 作成した *my-api1* アプリケーションを選択して、その **[概要]** ページを開きます。
1. **[管理]** の **[API の公開]** を選択します。
1. **[アプリケーション ID URI]** の横にある **[設定]** リンクを選択します。 既定値 (GUID) を `tasks-api` などの一意の名前に置き換え、 **[保存]** を選択します。 Web アプリケーションで Web API のアクセス トークンを要求するときに、API に対して定義する各スコープのプレフィックスとしてこの URI を追加する必要があります。
1. **[この API で定義されるスコープ]** で、 **[スコープの追加]** を選択します。
1. 次の値を入力して、API への読み取りアクセスを定義するスコープを作成し、 **[スコープの追加]** を選択します。
    1. **スコープ名**: `tasks.read`
    1. **管理者の同意の表示名**: `Read access to tasks API`
    1. **管理者の同意の説明**: `Allows read access to the tasks API`
1. **[スコープの追加]** を選択し、次の値を入力して、API への書き込みアクセスを定義するスコープを作成し、 **[スコープの追加]** を選択します。
    1. **スコープ名**: `tasks.write`
    1. **管理者の同意の表示名**: `Write access to tasks API`
    1. **管理者の同意の説明**: `Allows write access to the tasks API`