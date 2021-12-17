---
title: Azure Data Box の返送に関するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Data Box を返送する方法について説明します。これには、発送の準備、Data Box の発送、データ アップロードの検証、Data Box からのデータの消去などが含まれます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/16/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c3dd0d64542dd9d25643fb811accde5d882af870
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556593"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="docs"

このチュートリアルでは、Azure Data Box を返送し、Azure にアップロードされたデータを確認する方法について説明します。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
>
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

* 「[チュートリアル: Azure Data Box にデータをコピーして確認する](data-box-deploy-copy-data.md)」を完了していることを確認してください。
* コピー ジョブが完了し、 **[接続とコピー]** ページにエラーがないこと。 コピー ジョブが進行中の場合、または **[接続とコピー]** ページにエラーがある場合、**発送準備** を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

データのコピーが完了したら、デバイスを準備して発送します。 デバイスが Azure データセンターに到着すると、データが自動的に Azure にアップロードされます。

## <a name="prepare-to-ship"></a>発送の準備をする

発送を準備する前に、コピー ジョブが完了していることを確認します。

1. ローカル Web UI の **[出荷準備]** ページに移動し、出荷準備を開始します。
2. ローカル Web UI からデバイスをオフにします。 ケーブルをデバイスから取り外します。

次の手順は、デバイスを返送する場所によって決まります。

::: zone-end


## <a name="ship-data-box-back"></a>Data Box を返送する

デバイスへのデータ コピーが完了し、**発送準備** が正常に実行されたことを確認します。 

デバイスを発送する地域によって手順が異なります。 多くの国、地域では、[Microsoft が提供している配送方法](#microsoft-managed-shipping)を利用するか、または、[自分で行うそれ以外の方法](#self-managed-shipping)で配送できます。

### <a name="microsoft-managed-shipping"></a>Microsoft 管理の出荷

Microsoft 管理による出荷を使用している場合は、出荷元のリージョンのガイドラインに従います。

## <a name="us--canada"></a>[米国およびカナダ](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-europe)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**ドイツまたはスイスの Azure データセンターに返送する場合** は、[自己管理型の発送を使用](#self-managed-shipping)することもできます。

## <a name="uk"></a>[英国](#tab/in-uk)

[!INCLUDE [data-box-shipping-in-uk](../../includes/data-box-shipping-in-uk.md)]

## <a name="australia"></a>[オーストラリア](#tab/in-australia)

[!INCLUDE [data-box-shipping-in-australia](../../includes/data-box-shipping-in-australia.md)]

## <a name="japan"></a>[日本](#tab/in-japan)

[!INCLUDE [data-box-shipping-in-japan](../../includes/data-box-shipping-in-japan.md)]

## <a name="singapore"></a>[シンガポール](#tab/in-singapore)

[!INCLUDE [data-box-shipping-in-singapore](../../includes/data-box-shipping-in-singapore.md)]

## <a name="hong-kong"></a>[香港特別行政区](#tab/in-hk)

[!INCLUDE [data-box-shipping-in-hk](../../includes/data-box-shipping-in-hk.md)]

## <a name="korea"></a>[韓国](#tab/in-korea)

[!INCLUDE [data-box-shipping-in-korea](../../includes/data-box-shipping-in-korea.md)]

## <a name="s-africa"></a>[南アフリカ](#tab/in-sa)

[!INCLUDE [data-box-shipping-in-sa](../../includes/data-box-shipping-in-sa.md)]

## <a name="uae"></a>[アラブ首長国連邦](#tab/in-uae)

[!INCLUDE [data-box-shipping-in-uae](../../includes/data-box-shipping-in-uae.md)]

---

### <a name="self-managed-shipping"></a>自己管理の出荷

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end


::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

