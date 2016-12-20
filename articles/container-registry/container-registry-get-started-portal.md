---
title: "ポータルでのコンテナー レジストリの作成 | Microsoft Docs"
description: "Azure Portal を使用した Azure コンテナー レジストリの作成と管理の概要"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: c22fee1a9172eba28d8f841d973704934cdb3ebb

---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Azure Portal を使用したコンテナー レジストリの作成
Azure Portal を使用して、コンテナー レジストリを作成し、その設定を管理します。 コンテナー レジストリの作成と管理は、[Azure CLI 2.0 プレビュー](container-registry-get-started-azure-cli.md)を使用するか、Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) を使用してプログラムで行うこともできます。

背景情報と概念については、「[Azure Container Registry とは](container-registry-intro.md)」を参照してください。


> [!NOTE]
> Container Registry は現在プレビュー段階です。


## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
1. [ポータル](https://portal.azure.com)で **[+ 新規]** をクリックします。
2. Marketplace で **Container Registry** を検索します。
3. 発行元が **Microsoft** の **[Container Registry (preview) (コンテナー レジストリ (プレビュー))]** を選択します。 
    ![Azure Marketplace の Container Registry サービス](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. **[作成]**をクリックします。 **[Container Registry]** ブレードが表示されます。

    ![Container Registry の設定](./media/container-registry-get-started-portal/container-registry-settings.png)
5. **[Container Registry]** ブレードで、次の情報を入力します。 完了したら、**[作成]** をクリックします。
   
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Registry name (レジストリ名)]** - 特定のレジストリのグローバルに一意のトップレベル ドメイン名。 この例のレジストリの名前は *myRegistry01* ですが、独自の一意の名前に置き換えてください。 名前に使用できるのは、アルファベットと数字のみです。
   
    b. **[リソース グループ]** - 既存の[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
   
    c. **[場所]** - サービスを[利用可能](https://azure.microsoft.com/regions/services/)な Azure データセンターの場所を選択します (例: **米国中南部**)。 
   
    d. **[Admin user (管理者ユーザー)]** - 必要に応じて、レジストリにアクセスする管理者ユーザーを有効にします。 この設定は、レジストリの作成後に変更できます。
   
   > [!IMPORTANT]
   > Container Registr では、管理者ユーザー アカウントによるアクセス権の提供だけでなく、Azure Active Directory サービス プリンシパルによる認証もサポートしています。 詳細と考慮事項については、「[Authenticate with a container registry (コンテナー レジストリによる認証)](container-registry-authentication.md)」を参照してください。
   
    e. **ストレージ アカウント** - 既定の設定を使用して[ストレージ アカウント](../storage/storage-introduction.md)を作成するか、同じ場所にある既存のストレージ アカウントを選択します。

## <a name="manage-registry-settings"></a>レジストリ設定の管理
レジストリを作成すると、ポータルの **[Container Registries (コンテナー レジストリ)]** ブレードからレジストリ設定を検索できます。 たとえば、レジストリにログインするための設定が必要な場合や、管理者ユーザーを有効または無効にすることが必要な場合があります。

1. **[Container Registries (コンテナー レジストリ)]** ブレードで、レジストリの名前をクリックします。
   
    ![[Container Registries (コンテナー レジストリ)] ブレード](./media/container-registry-get-started-portal/container-registry-blade.png)
2. アクセス設定を管理するには、**[アクセス キー]** をクリックします。
   
    ![コンテナー レジストリのアクセス](./media/container-registry-get-started-portal/container-registry-access.png)
3. 以下の設定に注意してください。
   
   * **[Login server (ログイン サーバー)]** - レジストリにログインするために使用する完全修飾名。 この例では `myregistry01-contoso.azurecr.io` です。
   * **[Admin user (管理者ユーザー)]** - レジストリの管理者ユーザー アカウントの有効と無効を切り替えます。
   * **[ユーザー名]** と **[パスワード]** - レジストリへのログインに使用できる管理者ユーザー アカウントの資格情報 (有効な場合)。 必要に応じて、パスワードを再生成できます。

## <a name="next-steps"></a>次のステップ
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)






<!--HONumber=Nov16_HO3-->


