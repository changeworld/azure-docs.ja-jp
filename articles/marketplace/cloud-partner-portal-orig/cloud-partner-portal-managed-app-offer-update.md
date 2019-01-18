---
title: Azure Marketplace の既存のプランを更新する
description: Azure Marketplace の既存のプランを更新する
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073240"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Azure Marketplace の既存のプランを更新する 
==============================================

プランを公開した後で実行する更新には、さまざまな種類があります。たとえば次のとおりです。

1.  既存の SKU に新しい \"パッケージ\" を追加する
2.  既存のプランに新しい SKU を追加する
3.  プラン/SKU のマーケットプレース メタデータを更新する

Microsoft Cloud パートナー ポータルを使用して、これらの変更を実装し、プランを再公開します。 この記事では、Azure アプリケーション プランの更新に関するさまざまな側面について説明します。

<a name="unpermitted-changes-to-azure-application-offer"></a>Azure アプリケーション プランに対して許可されない変更 
-----------------------------------------------

Azure アプリケーション プラン/SKU には、Azure Marketplace にプランを公開した後では変更できない属性があります。

* プランのプラン ID とパブリッシャー ID。
* 既存の SKU の SKU ID。
* 発行済みパッケージの更新。

<a name="adding-a-new-package-to-an-existing-sku"></a>既存の SKU に新しいパッケージを追加する 
---------------------------------------

パブリッシャーは、既存のパッケージを更新するために、新しいバージョンのパッケージを追加したい場合があります。 この追加は、異なるバージョン番号の新しいパッケージをアップロードすることによって行えます。

1.  [Cloud パートナー ポータル](http://cloudpartner.azure.com)にサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[SKU]** フォームで、パッケージを更新する SKU をクリックします。
4.  \"[新しいパッケージ]\" をクリックし、新しいバージョンを指定します。
5.  更新済みのテンプレート ファイルを含んだ新しい .zip ファイルをアップロードします。
6.  [発行] をクリックして発行ワークフローを開始し、新しい SKU を公開します。

<a name="adding-a-new-sku-to-an-existing-offer"></a>既存のプランに新しい SKU を追加する
-------------------------------------

以下の手順に従って、既存のプランで新しい SKU を利用できるようにすることができます。

1.  [Cloud パートナー ポータル](http://cloudpartner.azure.com)にログインする
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  **[SKU]** フォームの 新しい SKU の追加 をクリックし、ポップアップに SKU ID を指定します。
4.  [こちら](./cloud-partner-portal-managed-app-publish.md)に記載されている残りの手順に従います。
5.  **[発行]** をクリックして発行ワークフローを開始し、新しい SKU を公開します。

<a name="updating-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新 
-----------------------------------

プランに関連付けられている会社のロゴなどの Marketplace メタデータを更新する必要がある状況が発生する場合があります。次の手順に従ってください。

1.  Microsoft Cloud パートナー ポータルにサインインします。
2.  **[すべてのプラン]** から、更新するプランを見つけます。
3.  [Marketplace] フォームに移動し、[こちら](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md)の指示に従って変更を加えます。
4.  [発行] をクリックして発行ワークフローを開始し、変更を公開します。
