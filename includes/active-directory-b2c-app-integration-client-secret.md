---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 18120c126fafd03b6bb91901b33f8932644079c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122868369"
---
登録済み Web アプリケーションに対してクライアント シークレットを作成します。 Web アプリケーションでは、トークンを要求するときに、このクライアント シークレットを使ってその ID を証明します。

1. **[管理]** で、 **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します (例、*clientsecret1*)。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. シークレットの **値** を記録します。 この値は、後の手順での構成に使用します。