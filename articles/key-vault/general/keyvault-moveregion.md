---
title: Azure Key Vault のコンテナーを別のリージョンに移動する | Microsoft Docs
description: キー コンテナーを別のリージョンに移動するためのガイダンス。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254072"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>リージョン間での Azure キー コンテナーの移動

## <a name="overview"></a>概要

Key Vault では、キー コンテナーを別のリージョンに移動することを許可するリソース移動操作はサポートされていません。 この記事では、業務上、キー コンテナーを別のリージョンに移動する必要がある場合の回避策について説明します。 各オプションには制限があり、これらの回避策の意味を十分に理解したうえで、運用環境で実行することが重要です。

キー コンテナーを別のリージョンに移動する必要がある場合の解決策は、目的のリージョンに新しいキー コンテナーを作成し、既存のキー コンテナーから新しいキー コンテナーに個々のシークレットを手動でコピーすることです。 この操作は、次に示す方法のいずれかで行うことができます。

## <a name="design-considerations"></a>デザインに関する考慮事項

* キー コンテナーの名前はグローバルに一意です。 同じコンテナー名を再利用することはできません。

* 新しいキー コンテナー内に、アクセス ポリシーとネットワーク構成設定を再構成する必要があります。

* 新しいキー コンテナー内に、論理的な削除と消去保護を再構成する必要があります。

* バックアップと復元の操作でのオートローテーション設定は維持されません。これらの設定の再構成が必要である可能性があります。

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>オプション 1 - キー コンテナーのバックアップ コマンドと復元コマンドを使用する

バックアップ コマンドを使用して、コンテナー内の個々のシークレット、キー、および証明書をバックアップできます。 シークレットは暗号化された BLOB としてダウンロードされます。 その後、その BLOB を新しいキー コンテナー内に復元できます。 これらのコマンドは、以下のリンクに記載されています。

[Azure Key Vault のコマンド](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>制限事項

* ある地域のキー コンテナーをバックアップし、別の地域に復元することはできません。 Azure の地域の詳細を確認してください。 [リンク](https://azure.microsoft.com/global-infrastructure/geographies/)

* バックアップ コマンドでは、各シークレットのすべてのバージョンがバックアップされます。 以前のバージョンが多数 (10 個以上) 存在するシークレットがある場合は、要求で許容される最大要求サイズを超えることで操作が失敗する可能性があります。

## <a name="option-2---manually-download-and-upload-secrets"></a>オプション 2 - シークレットを手動でダウンロードしてアップロードする

特定のシークレットの種類は手動でダウンロードできます。 たとえば、証明書を .pfx ファイルとしてダウンロードできます。 このオプションを使用すると、証明書などの一部のシークレットの種類に対する地域の制限はなくなります。 任意のリージョンの任意のキー コンテナーに .pfx ファイルをアップロードできます。 シークレットは、パスワードで保護されていない形式でダウンロードされます。 移行を実行している間、Key Vault の管理外になったシークレットを保護する責任はユーザーにあります。
