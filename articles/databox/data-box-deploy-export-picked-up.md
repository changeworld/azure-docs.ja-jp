---
title: エクスポート注文での Azure Data Box の返送に関するチュートリアル | Microsoft Docs
description: エクスポート注文の完了後に Azure Data Box を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.openlocfilehash: 544454e4022da67db50fb194d7aea72a6bd716bd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449976"
---
# <a name="tutorial-return-azure-data-box"></a>チュートリアル:Azure Data Box を返送する

このチュートリアルでは、Azure Data Box を返送する方法について説明します。デバイスが Azure データに届くと、データは消去されます。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
>
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

* 「[チュートリアル: Azure Data Box からデータをコピーする](data-box-deploy-export-copy-data.md)」を完了していること。
* コピージョブが完了していること。 コピージョブが進行中の場合、発送準備を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

次の手順は、デバイスを返送する場所によって決まります。

## <a name="ship-data-box-back"></a>Data Box を返送する

デバイスからのデータ コピーを確実に完了しさせ、 **[発送の準備をする]** の実行を正常終了させます。

デバイスを発送する地域によって手順が異なります。 多くの国、地域では、Microsoft が提供している配送方法を利用するか、または、自分で行うそれ以外の方法で配送できます。

### <a name="microsoft-managed-shipping"></a>Microsoft 管理の出荷

Microsoft 管理による出荷を使用している場合は、出荷元のリージョンのガイドラインに従います。

## <a name="us--canada"></a>[米国およびカナダ](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-eu)

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

### <a name="self-managed-shipping"></a>自己管理の出荷

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

---

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

デバイスが Azure データセンターに到着すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のようなトピックを学習しました。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Data Box からデータを消去する

次の記事に進んで、Data Box の管理方法を学習してください。

> [!div class="nextstepaction"]
> [Azure portal で Data Box を管理する](./data-box-portal-admin.md)
