---
title: Azure API Management でのプロトコルと暗号の管理 |Microsoft Docs
description: Azure API Management でプロトコル (TLS、SSL) および暗号 (DES) を管理する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441114"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Azure API Management でプロトコルと暗号を管理する

Azure API Management では、3DES 暗号と同様にクライアントとバックエンドの両側の複数のバージョンの TLS プロトコルをサポートしています。

このガイドでは、Azure API Management インスタンスのプロトコルと暗号の構成を管理する方法について説明します。

![APIM でプロトコルと暗号を管理する](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

* API Management インスタンス

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS プロトコルと 3DES 暗号を管理する方法

1. Azure portal で **API Management インスタンス**に移動します。
2. メニューで **[SSL]** を選択します。  
    ![APIM でプロトコルおよび暗号を管理する - メニュー](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. プロトコルまたは暗号を有効または無効にします。
4. **[Save]** をクリックします。 変更は 1 時間以内に適用されます。  
    ![APIM でプロトコルおよび暗号を管理する - 保存](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>次の手順

* [TLS (トランスポート層セキュリティ)](https://docs.microsoft.com/dotnet/framework/network-programming/tls)の詳細を参照してください。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。