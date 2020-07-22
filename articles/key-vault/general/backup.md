---
title: Azure Key Vault のバックアップ | Microsoft Docs
description: このドキュメントは、Azure Key Vault に格納されたシークレット、キー、証明書をバックアップする際の参考としてご利用ください。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156356"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault のバックアップ

このドキュメントでは、Key Vault に格納された個々のシークレット、キー、証明書のバックアップを実行する方法について説明します。 このバックアップは、万一キー コンテナーにアクセスできなくなるなどの事態が発生した場合に、すべてのシークレットのオフライン コピーを提供することを意図したものです。

## <a name="overview"></a>概要

Key Vault は、可用性を維持し、データの損失を防ぐためのいくつかの機能をそれ自体で備えています。 シークレットのバックアップを維持する業務上の正当かつ重要な理由がある場合にのみ、このバックアップを試行してください。 キー コンテナーにシークレットをバックアップした場合、シークレットの有効期限が切れたりシークレットのローテーションを行ったりする際に、ログとアクセス許可とバックアップ一式を複数維持しなければならないなど、運用上の負担が増えます。

Key Vault は、障害状況下で可用性を維持し、ペア リージョンに対して要求を自動的にフェールオーバーします。ユーザーが介入する必要はありません。 詳細については、次のリンクを参照してください。 [Azure Key Vault のディザスター リカバリー](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault は論理的な削除と消去保護を通じて、不注意による削除や悪意による削除からシークレットを保護します。 不注意による削除や悪意による削除からシークレットを保護したい場合は、キー コンテナーに論理的な削除と消去保護の機能を構成してください。 詳細については、次のドキュメントを参照してください。 [Azure Key Vault の回復](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>制限事項

Azure Key Vault では現在、キー コンテナー全体を 1 回の操作でバックアップすることはできません。 このドキュメントに記載されているコマンドを使用してキー コンテナーの自動バックアップを実行する方法は、Microsoft でも Azure Key Vault チームでもサポートしていません。

以下のドキュメントで紹介されているコマンドを使用してカスタムの自動化機構を作成しようとすると、エラーが発生する可能性があります。

* 複数のバージョンがあるシークレットをバックアップしようとするとタイムアウト エラーが発生する可能性があります。
* バックアップでは、ポイントインタイム スナップショットが作成されます。 バックアップ中にシークレットが更新されて、暗号化キーの不一致が生じることがあります。
* 1 秒あたりの要求数に関するキー コンテナー サービスの制限を超えると、キー コンテナーがスロットルされ、バックアップが失敗する原因となります。

## <a name="design-considerations"></a>設計上の考慮事項

キー コンテナーに格納されたオブジェクト (シークレット、キー、証明書) をバックアップするとき、そのオブジェクトは、バックアップ操作によって、暗号化された BLOB としてダウンロードされます。 Azure の外部でこの BLOB の暗号化を解除することはできません。 この BLOB から有効なデータを取得するには、同じ Azure サブスクリプションと Azure 地域内のキー コンテナーに BLOB を復元する必要があります。
[Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションに対する共同作成者レベル以上のアクセス許可
* バックアップ対象のシークレットを格納するプライマリ キー コンテナー
* シークレットの復元先となるセカンダリ キー コンテナー

## <a name="back-up-and-restore-with-azure-portal"></a>Azure portal を使用したバックアップと復元

### <a name="back-up"></a>バックアップ

1. Azure Portal に移動します。
2. キー コンテナーを選択します。
3. バックアップするオブジェクト (シークレット、キー、または証明書) に移動します。

    ![Image](../media/backup-1.png)

4. オブジェクトを選択します。
5. [バックアップのダウンロード] を選択します。

    ![Image](../media/backup-2.png)
    
6. [ダウンロード] ボタンをクリックします。

    ![Image](../media/backup-3.png)
    
7. 暗号化された BLOB を安全な場所に保存します。

### <a name="restore"></a>復元

1. Azure Portal に移動します。
2. キー コンテナーを選択します。
3. 復元するオブジェクトの種類 (シークレット、キー、または証明書) に移動します。
4. [バックアップの復元] を選択します。

    ![Image](../media/backup-4.png)
    
5. 暗号化された BLOB の保存先に移動します。
6. [OK] を選択します。

## <a name="back-up-and-restore-with-the-azure-cli"></a>Azure CLI を使用したバックアップと復元

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>次のステップ

Azure Key Vault のログと監視を有効にします。 [Azure Key Vault のログ記録](https://docs.microsoft.com/azure/key-vault/general/logging)
