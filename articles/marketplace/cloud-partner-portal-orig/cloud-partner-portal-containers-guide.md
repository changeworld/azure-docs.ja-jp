---
title: コンテナー | Microsoft Docs
description: コンテナー イメージを発行するための手順。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807986"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Cloud パートナー ポータルでのコンテナー イメージの発行
========================================================

この記事では、Cloud パートナー ポータル上で新しいコンテナー イメージを発行する方法について説明します。

新しいコンテナー イメージを発行する手順は次のとおりです。

1. **[新しいオファー]** を選択し、**[コンテナー]** を選択します。

    ![新しいオファーのコンテナー オプションを選択](media/cpp-containers-guide/azure-container-offer.png)

2. [オファーの設定] タブの [Offer Identity]\(オファー識別情報\) で、**[オファー ID]**、**[発行元 ID]**、**[名前]** に入力します。

    ![オファー識別情報](media/cpp-containers-guide/containers-offer-settings.png)

3. [SKU] タブで、**[新しい SKU]** を選択します。
    >[!NOTE]
    >複数の SKU を追加できます。

    ![新しい SKU のプロンプト](media/cpp-containers-guide/containers-sku-settings.png)

4. SKU とコンテナーの情報を入力します。 各 SKU が 1 つのコンテナー イメージに対応します。 SKU は 2 つの部分に分かれています。

    -   SKU メタデータ
    -   コンテナー メタデータ

    SKU メタデータには、コンテナー イメージの表示情報が含まれています。

    ![SKU メタデータ](media/cpp-containers-guide/containers-sku-details.png)

    コンテナー メタデータには、Azure Container Registry (ACR) 内にあるイメージ リポジトリの詳細の参照情報が含まれています。 次に、このイメージが Azure Marketplace のパブリック マーケットプレース レジストリにコピーされ、認定後に顧客向けに利用可能になります。 Azure ユーザーからのコンテナー イメージ使用要求は、すべて Marketplace コンテナー レジストリで処理されます。

   ![コンテナー メタデータ](media/cpp-containers-guide/containers-image-repository.png)

    上記のスクリーン キャプチャに示されているイメージ リポジトリの詳細には、次のフィールドがあります。

    -   **[サブスクリプション ID]**: ACR レジストリが存在する Azure サブスクリプション ID です。
    -   **[リソース グループ名]**: ACR レジストリのリソース グループ名です。
    -   **[レジストリ名]**: ACR レジストリの名前です。
    -   **[リポジトリ名]**: リポジトリ名です。 一度設定すると、この値を後から変更することはできません。 これは一意の名前にする必要があります。お使いのアカウントのその他のオファーと同じ名前にならないようにするためです。
    -   **[ユーザー名]**: ACR に関連付けられているユーザー名 (管理者ユーザー名) です。
    -   **[パスワード]**: ACR に関連付けられているパスワードです。

    >[!NOTE]
    >発行プロセスで説明した ACR にパートナーが確実にアクセスできるようにするため、ユーザー名とパスワードが必要です。

    コンテナー イメージを発行する際、1 つ以上のイメージ タグを指定することもできます。 イメージ タグだけでなく、パートナーは SHA ダイジェストも指定できます。 テスト中にイメージを識別できるようにするため、必ず**テスト タグ**をイメージに追加してください。

5. [マーケットプレース] タブで、使用するマーケティング固有のコンテンツを追加します。

    ![マーケットプレースの情報](media/cpp-containers-guide/containers-marketplace-tab.png)

6. [サポート] タブで、エンジニア リングの連絡先と顧客サポート情報を入力します。

   ![サポート情報](media/cpp-containers-guide/containers-support-tab.png)

7. **[発行]** を選択してオファーを発行します。 [発行] を選択した後、コンテナー イメージの発行に関連するステップを示すタイムラインが表示されます。
