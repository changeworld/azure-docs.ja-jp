---
title: "Azure Storage で安全な転送が必要 | Microsoft Docs"
description: "Azure Storage の [安全な転送が必須] 機能、およびこの機能を有効にする方法について説明します。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 516618653064fd4e334197bba767a013a805260a
ms.contentlocale: ja-jp
ms.lasthandoff: 06/21/2017

---
# <a name="require-secure-transfer"></a>安全な転送が必要

[安全な転送が必須] オプションは、セキュリティで保護された接続からストレージ アカウントへの要求を許可するだけで、ストレージ アカウントのセキュリティを強化することができます。 たとえば、ストレージ アカウントにアクセスするために REST API を呼び出している場合、HTTPS を使用して接続する必要があります。 [安全な転送が必須] が有効になっている場合、HTTP を使用した要求はすべて拒否されます。

Azure Files サービスを使用している場合、[安全な転送が必須] を有効にすると、暗号化をしない接続は失敗します。 これには、暗号化なしの SMB 2.1、SMB 3.0、および Linux SMB クライアントのフレーバーを使用するシナリオも含まれます。 

既定では、[安全な転送が必須] オプションは無効になっています。

> [!NOTE]
> Azure Storage ではカスタム ドメイン名の HTTPS はサポートされないため、カスタム ドメイン名を使用している場合、このオプションは適用されません。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Azure Portal で [安全な転送が必須] を有効にする

[Azure Portal](https://portal.azure.com) でストレージ アカウントを作成する場合も、既存のストレージ アカウントの場合も、[安全な転送が必須] 設定を有効にすることができます。

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>ストレージ アカウントの作成時の安全な転送が必須

1. Azure Portal で [**ストレージ アカウントの作成**] ブレードを開きます。
1. [**安全な転送が必須**] で、[**有効**] を選択します。

  ![スクリーンショット](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>既存のストレージ アカウントの安全な転送が必須

1. Azure Portal で既存のストレージ アカウントを選択します。
1. ストレージ アカウント メニュー ブレードの [**設定**] で [**構成**] を選択します。
1. [**安全な転送が必須**] で、[**有効**] を選択します。

  ![スクリーンショット](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="next-steps"></a>次のステップ
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳細については、 [Storage セキュリティ ガイド](storage-security-guide.md)に関する記事を参照してください。

