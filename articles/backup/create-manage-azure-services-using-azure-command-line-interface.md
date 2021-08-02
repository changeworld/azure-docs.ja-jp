---
title: Azure CLI を使用した Azure サービスの作成と管理
description: Azure CLI を使用して、Azure Backup 用の Azure サービスを作成および管理します。
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 3581be20082d1c579caad4cf82ce6b2408f01a92
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483343"
---
# <a name="create-and-manage-azure-backup-services-using-azure-command-line-interface-azure-cli"></a>Azure コマンドライン インターフェイス (Azure CLI) を使用して Azure Backup サービスを作成および管理する

Azure コマンド ライン インターフェイス (Azure CLI) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は、自動化や Azure Backup の操作に役立ちます。

この記事では、Azure Backup の Azure CLI をサポートするサービスについての説明と、対応する記事へのリンクを掲載しています。

## <a name="document-references-for-cli-supported-azure-services"></a>CLI がサポートされている Azure サービスのドキュメント リファレンス

次の表は、サポートされている Azure サービスで使用できる Azure CLI ドキュメント レファレンスをまとめたものです。

Azure サービス | CLI ドキュメント レファレンス
-------------------------- | ---------------------------------
Azure Vault | [Recovery Services コンテナーの削除](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-using-cli)
Azure 仮想マシン (VM) | <li>[Azure VM のバックアップ](quick-backup-vm-cli.md)</li><li>[Azure VM の復元](tutorial-restore-disk.md)</li><li>[Azure VM のバックアップからのファイルの復元](tutorial-restore-files.md)</li><li>[既存の VM バックアップ ポリシーの更新](modify-vm-policy-cli.md)</li><li>[Azure VM の選択的なディスクのバックアップと復元](selective-disk-backup-restore.md#using-azure-cli)</li>
Azure ファイル共有 | <li>[Azure ファイル共有のバックアップ](backup-afs-cli.md)</li><li>[Azure ファイル共有を復元する](restore-afs-cli.md)</li><li>[Azure ファイル共有のバックアップを管理する](manage-afs-backup-cli.md)</li>
SAP HANA | <li>[Azure VM での SAP HANA データベースのバックアップ](tutorial-sap-hana-backup-cli.md)</li><li>[Azure VM での SAP HANA データベースの復元](tutorial-sap-hana-restore-cli.md)</li><li>[Azure VM での SAP HANA データベースの管理](tutorial-sap-hana-manage-cli.md)</li>

