---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 80f8f4080b068d840460dfd9acb811174c267d04
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007788"
---
登録済み Web アプリケーションに対してクライアント シークレットを作成します。 Web アプリケーションでは、トークンを要求するときに、このクライアント シークレットを使ってその ID を証明します。

1. **[管理]** で、 **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します (例、*clientsecret1*)。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. シークレットの **値** を記録します。 この値は、後の手順での構成に使用します。