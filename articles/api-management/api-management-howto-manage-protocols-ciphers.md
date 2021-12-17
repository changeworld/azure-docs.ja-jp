---
title: Azure API Management でのプロトコルと暗号の管理 |Microsoft Docs
description: Azure API Management でプロトコル (TLS) と暗号 (DES) を管理する方法について説明します。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 5fc47f2238427c7875d2af29324e06d8b6487f1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001791"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Azure API Management でプロトコルと暗号を管理する

Azure API Management では、次のために複数バージョンのトランスポート層セキュリティ (TLS) プロトコルがサポートされています。
* クライアント側
* バックエンド側
* 3DES 暗号

このガイドでは、Azure API Management インスタンスのプロトコルと暗号の構成を管理する方法について説明します。

![APIM でプロトコルと暗号を管理する](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>前提条件

* API Management インスタンス。 [まだない場合は、作成してください](get-started-create-service-instance.md)。

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS プロトコルと 3DES 暗号を管理する方法

1. Azure portal で **API Management インスタンス** に移動します。
1. サイド メニューの **[セキュリティ]** セクションまでスクロールします。
1. [セキュリティ] セクションで、 **[プロトコルと暗号]** を選択します。  
1. プロトコルまたは暗号を有効または無効にします。
1. **[保存]** をクリックします。 変更は 1 時間以内に適用されます。  

> [!NOTE]
> 一部のプロトコルや暗号スイート (バックエンド側 TLS 1.2 など) は、Azure portal からは有効にしたり無効にしたりすることができません。 代わりに、REST 呼び出しを適用する必要があります。 [API Management サービス REST API の作成または更新](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update)に関する記事にある、`properties.customProperties` 構造を使用します。

## <a name="next-steps"></a>次のステップ

* [TLS](/dotnet/framework/network-programming/tls) の詳細を参照してください。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
