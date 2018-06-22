---
title: 国に応じて Azure CDN コンテンツへのアクセスを制限 | Microsoft Docs
description: geo フィルタリング機能を使用して Azure CDN コンテンツへのアクセスを制限する方法について説明します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 93321c4c8a7f8d79835d702ca07132eed94f6493
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260754"
---
# <a name="restrict-azure-cdn-content-by-country"></a>国に応じて Azure CDN コンテンツへのアクセスを制限

## <a name="overview"></a>概要
ユーザーがコンテンツを要求した場合、既定では、ユーザーが要求を行った場所に関係なく、コンテンツは提供されます。 場合によっては、国によってコンテンツへのアクセスを制限することがあります。 この記事では、"*geo フィルタリング*" 機能を使用して、国別にアクセスを許可またはブロックするようにサービスを構成する方法について説明します。

> [!IMPORTANT]
> すべての Azure CDN 製品で同じ geo フィルタリング機能が用意されていますが、サポートされる国番号が少し異なります。 詳細については、「[Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)」 (Azure CDN 国コード) を参照してください。


このような制限を構成する際に考慮する事項については、「[考慮事項](cdn-restrict-access-by-country.md#considerations)」を参照してください。  

![国のフィルタリング](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>手順 1: ディレクトリのパスを定義する
> [!IMPORTANT]
> **Azure CDN Standard from Microsoft** プロファイルでは、パス ベースの geo フィルタリングはサポートされません。
>


ポータル内のエンドポイントを選択し、左側のナビゲーションで [Geo-Filtering (地理フィルタリング)] タブを探して、この機能を確認します。

国フィルターを構成するときは、ユーザーのアクセスを許可または拒否する場所への相対パスを指定する必要があります。 スラッシュ (/) を指定してすべてのファイルに geo フィルタリングを適用することも、ディレクトリ パス */pictures/* を指定して選択したフォルダーに適用することもできます。 また、*/pictures/city.png* のようにファイルを指定して後のスラッシュを除外して、1 つのファイルに geo フィルタリングを適用することもできます。

ディレクトリ パスのフィルターの例:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>手順 2: ブロックまたは許可アクションを定義する
**ブロック** : 指定した国のユーザーは、その再帰パスから要求された資産へのアクセスを拒否されます。 その場所に対して他の国フィルター オプションが構成されていない場合、他のすべてのユーザーはアクセスを許可されます。

**許可** : 指定した国のユーザーだけが、その再帰パスから要求された資産へのアクセスを許可されます。

## <a name="step-3-define-the-countries"></a>手順 3: 国を定義する
パスに対してブロックまたは許可する国を選択します。 

たとえば、/Photos/Strasbourg/ をブロックするルールは、次のようなファイルをフィルターします。

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>国コード
geo フィルタリング機能では、国番号を使用して、保護されたディレクトリに対する要求が許可またはブロックされる国を定義します。 すべての Azure CDN 製品で同じ geo フィルタリング機能が用意されていますが、サポートされる国番号が少し異なります。 詳細については、「[Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)」 (Azure CDN 国コード) を参照してください。 

## <a name="considerations"></a>考慮事項
* 国フィルタリング構成への変更は、すぐには有効になりません。
   * **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
   * **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
   * **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。 
 
* この機能では、ワイルドカード文字 (例: *) はサポートされていません。

* 相対パスに関連付けられている地理フィルタリング構成は、そのパスに再帰的に適用されます。

* 同じ相対パスに適用できるのは、1 つのルールのみです。 つまり、同じ相対パスを指す複数の国フィルターを作成することはできません。 ただし、国フィルターの再帰的な性質のため、フォルダーには複数の国フィルターを含めることができます。 つまり、以前に構成されているフォルダーのサブフォルダーに、別の国フィルターを割り当てることができます。

