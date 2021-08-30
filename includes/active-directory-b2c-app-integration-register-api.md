---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 4ab3986f66d420d1fcb1ae67747cae5e42f7c59b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778868"
---
Web API アプリの登録 (App ID: 2) を作成するには、こちらの手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します (*my-api1* など)。 **[リダイレクト URI]** は既定値のままにします。 
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web API アプリケーション ID の取得方法を示すスクリーンショット。](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)