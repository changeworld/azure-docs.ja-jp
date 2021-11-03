---
title: Azure でのサービス プリンシパルの作成
description: この記事では、Azure でサービス プリンシパルを作成する方法について説明します。
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d2be616d19ec862a7bf27d55f0a35ed1737fbf86
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092500"
---
# <a name="creating-a-service-principal"></a>サービス プリンシパルの作成

サービス プリンシパルを新しく作成することも、既存のサービス プリンシパルを Azure Active Directory テナントで使用することもできます。

## <a name="app-registration"></a>アプリの登録

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 左側のメニューで、 **[Azure Active Directory]** を選択します。
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-aad.png" alt-text="Azure Active Directory へのリンクを示すスクリーンショット":::

3. **[アプリの登録]** 、 **[+ 新規登録]** を選択します。
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-reg.png" alt-text="新規登録へのリンクを示すスクリーンショット":::

4. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。

5. **[この組織のディレクトリ内のアカウントのみ]** を選択します。

6. **[リダイレクト URI]** で **[Web]** を選択し、必要な URL を入力します。これは実際の URL である必要はなく、機能しなくてもかまいません。

7. 次に、 **[登録]** を選択します。
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-register.png" alt-text="新しいアプリの登録の詳細を示すスクリーンショット":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-app.png" alt-text="新しく作成されたアプリケーションを示すスクリーンショット":::

## <a name="adding-a-secret-to-the-client-credentials"></a>クライアント資格情報へのシークレットの追加

1. **[アプリの登録]** からアプリを選択します
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-app-select.png" alt-text="登録するアプリを示すスクリーンショット":::

2. **[証明書またはシークレットの追加]** をクリックします
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-add-secret.png" alt-text="アプリを示すスクリーンショット":::

3. **[クライアント シークレット]** の **[+ 新しいクライアント シークレット]** をクリックします
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-client-secret.png" alt-text="クライアント シークレット メニューを示すスクリーンショット":::

4. **[説明]** を入力して、シークレットの **[有効期限]** を設定します :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-secret-desc.png" alt-text="クライアント シークレットの詳細を示すスクリーンショット":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-secret.png" alt-text="クライアント シークレットを示すスクリーンショット":::

5. **[クライアントの資格情報]** の値を **[概要]** からコピーします
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-cred.png" alt-text="アプリの [概要] を示すスクリーンショット":::

## <a name="adding-the-secret-to-the-key-vault"></a>キー コンテナーへのシークレットの追加

1. **キー コンテナー** に移動します
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-key-vault.png" alt-text="キー コンテナーを示すスクリーンショット":::

2. **[設定]**  -->  **[シークレット]**  -->  **[+ Generate/Import]\(+ 生成/インポート\)** を選択します  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-generate-secret.png" alt-text="キー コンテナーのオプションのスクリーンショット":::

3. **[名前]** に選択した内容を入力し、サービス プリンシパルの **クライアント シークレット** として **値** を入力します  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-sp-secret.png" alt-text="シークレットを作成するためのキー コンテナーを示すスクリーンショット":::

4. **[作成]** を選択して完了します
