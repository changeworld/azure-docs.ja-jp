---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f1dc38dd2c8c198a961f1ea05f9e4fea5205ad89
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073078"
---
登録済み Web アプリケーションに対してクライアント シークレットを作成します。 Web アプリケーションでは、トークンを要求するときに、このクライアント シークレットを使ってその ID を証明します。

1. **[管理]** で、 **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します。 たとえば、*clientsecret1* のようにします。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. シークレットの **値** を記録します。 この値は、後の手順で構成に使用します。