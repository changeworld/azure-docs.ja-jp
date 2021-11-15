---
title: キー コンテナーを別のリージョンに移動する - Azure Key Vault | Microsoft Docs
description: この記事では、キー コンテナーを別のリージョンに移動するためのガイダンスを提供します。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2021
ms.author: mbaldwin
ms.custom: subject-moving-resources
ms.openlocfilehash: b8364ae34c8fcef3b65b83d5f2e1851920634edd
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370141"
---
# <a name="move-an-azure-key-vault-across-regions"></a>リージョン間で Azure キー コンテナーを移動する

Azure Key Vault では、リージョン間でキー コンテナーを移動できません。 ただし、新しいリージョンでキー コンテナーを作成し、個々のキー、シークレット、証明書を既存のキー コンテナーから新しいキー コンテナーに手動でコピーしてから元のキー コンテナーを削除することは可能です。

## <a name="prerequisites"></a>前提条件

以上の回避策の意味を理解したうえで、運用環境で適用してみることが重要です。

## <a name="prepare"></a>準備

まず、移動先のリージョンで新しいキー コンテナーを作成する必要があります。 これは [Azure portal](quick-create-portal.md)、[Azure CLI](quick-create-cli.md)、または [Azure PowerShell](quick-create-powershell.md) を利用して実行できます。

次の概念に注意してください。

* キー コンテナーの名前はグローバルに一意です。 コンテナー名を再利用することはできません。
* 新しいキー コンテナー内に、アクセス ポリシーとネットワーク構成設定を再構成する必要があります。
* 新しいキー コンテナー内に、論理的な削除と消去保護を再構成する必要があります。
* バックアップと復元の操作では、オートローテーション設定は保持されません。 場合によっては、設定を再構成する必要があります。

## <a name="move"></a>詳細ビュー

古いキー コンテナーからキー、シークレット、証明書をエクスポートし、新しいコンテナーにインポートします。 

バックアップ コマンドを使用して、コンテナー内の個々のシークレット、キー、および証明書をバックアップできます。 シークレットは暗号化された BLOB としてダウンロードされます。  詳細な手順については、[Azure Key Vault のバックアップと復元](backup.md)に関するページを参照してください。

あるいは、特定のシークレットの種類を手動でダウンロードできます。 たとえば、証明書を PFX ファイルとしてダウンロードできます。 このオプションを使用すると、証明書などの一部のシークレットの種類に対する地域の制限はなくなります。 任意のリージョンの任意のキー コンテナーに PFX ファイルをアップロードできます。 シークレットは、パスワードで保護されていない形式でダウンロードされます。 移動中のシークレットの保護は、お客様が責任を負うものとします。

キー、シークレット、証明書をダウンロードしたら、それらを新しいキー コンテナーで復元します。 

バックアップ コマンドと復元コマンドの使用には、次の 2 つの制限があります。

* ある地域のキー コンテナーをバックアップし、別の地域に復元することはできません。 詳しくは、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」をご覧ください。

* バックアップ コマンドでは、各シークレットのすべてのバージョンがバックアップされます。 以前のバージョンが多数 (11 個以上) 存在するシークレットがある場合は、要求のサイズが許容される最大値を超え、操作が失敗する可能性があります。

## <a name="verify"></a>確認

古いキー コンテナーを削除する前に、必要なすべてのキー、シークレット、証明書が新しいコンテナーに必ず含まれているようにします。 


## <a name="next-steps"></a>次の手順

- [Azure Key Vault のバックアップと復元](backup.md)
- [リソース グループ間での Azure Key Vault の移動](move-resourcegroup.md)
- [Azure Key Vault を別のサブスクリプションに移動する](move-subscription.md)