---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: kengaderdus
ms.openlocfilehash: 65376f6154390bad8bfaa736dc4e76204f65f396
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007622"
---
Web API アプリの登録 (**App ID: 2**) を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します (**my-api1** など)。 **[リダイレクト URI]** と **[サポートされているアカウントの種類]** を既定値のままにします。
1. **[登録]** を選択します。
1. アプリ登録が完了したら、 **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** の値を記録しておきます。これは、後で Web アプリケーションを構成するときに使用します。

    ![Web A P I アプリケーション ID の取得方法を示すスクリーンショット。](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)
