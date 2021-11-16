---
title: Azure Arc 対応サーバー用の拡張機能の自動アップグレード (プレビュー)
description: Azure Arc 対応サーバー用の拡張機能の自動アップグレード (プレビュー) を有効にする方法について説明します。
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: aea00dbe8609d0a8481d9ca742c12e6d0215f189
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132033753"
---
# <a name="automatic-extension-upgrade-preview-for-azure-arc-enabled-servers"></a>Azure Arc 対応サーバー用の拡張機能の自動アップグレード (プレビュー)

サポートされている VM 拡張機能がインストールされている Azure Arc 対応サーバーで、拡張機能の自動アップグレード拡張機能 (プレビュー) を使用できます。 マシンで拡張機能の自動アップグレード (プレビュー) が有効になっていると、拡張機能の発行元がその拡張機能の新しいバージョンをリリースするたびに、拡張機能が自動的にアップグレードされます。

 拡張機能の自動アップグレードには、次の機能があります。

- 自動アップグレードは、いつでもオプトインとオプトアウトが可能です。
- サポートされている各拡張機能は個別に登録され、どの拡張機能を自動的にアップグレードするかを選択できます。
- すべてのパブリック クラウド リージョンでサポートされています。

> [!NOTE]
> このリリースでは、拡張機能の自動アップグレードを構成するためにサポートされているのは Azure CLI のみです。

## <a name="how-does-automatic-extension-upgrade-work"></a>拡張機能の自動アップグレードのしくみ

拡張機能のアップグレード プロセスにより、Azure Arc 対応サーバーによってサポートされている既存の Azure VM 拡張機能のバージョンが、拡張機能の発行元によって発行されたときと同じ拡張機能の新しいバージョンに置き換えられます。

拡張機能の更新が失敗した場合は、自動的に再試行されます。 再試行は、ユーザーの介入なしに、数日に 1 回試行されます。

### <a name="availability-first-updates"></a>可用性優先の更新

プラットフォームの調整された更新の可用性優先モデルにより、Azure の可用性構成が複数の可用性レベルで尊重されます。

更新が進行中の Arc 対応サーバーのグループの場合、[Automation での拡張機能のアップグレード](../../virtual-machines/automatic-extension-upgrade.md#availability-first-updates)に関するページで説明されているモデルに従って、Azure プラットフォームによって更新のオーケストレーションが行われます。 ただし、Arc 対応サーバーと Azure VM には重要な違いがいくつかあります。

**複数のリージョン間:**

- geo ペア リージョンは適用できません。

**リージョン内:**

- 可用性ゾーンは適用できません。
- サブスクリプション内の Arc 対応サーバーに登録されているすべてのマシンで同時に更新が行われないように、マシンはベスト エフォート方式でバッチ処理されます。  

## <a name="supported-extensions"></a>サポートされる拡張機能

拡張機能の自動アップグレード (プレビュー) では、以下の拡張機能がサポートされています (定期的にさらに多くが追加されます)。

- Azure Monitor エージェント - Linux と Windows
- Azure Security エージェント - Linux と Windows
- Dependency エージェント – Linux と Windows
- Key Vault 拡張機能 - Linux のみ
- Log Analytics エージェント (OMS エージェント) - Linux のみ

## <a name="enabling-automatic-extension-upgrade-preview"></a>拡張機能の自動アップグレード (プレビュー) の有効化

拡張機能の自動アップグレード (プレビュー) を拡張機能に対して有効にするには、プロパティ `enable-auto-upgrade` が `true` に設定されていて、どの拡張機能の定義にも個々に追加されていることを確認する必要があります。

`--name`、`--machine-name`、`--enable-auto-upgrade`、`--resource-group` の各パラメーターを指定して、[az connectedmachine extension](/cli/azure/connectedmachine/extension) コマンドレットを使用します。

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade true
```

拡張機能の自動アップグレード (プレビュー) の状態を、Arc 対応サーバー上のすべての拡張機能について確認するには、次のコマンドを実行します。

```azurecli
az connectedmachine extension list --resource-group resourceGroupName --machine-name machineName --query "[].{Name:name, AutoUpgrade:properties.enableAutoUpgrade}" --output table
```

## <a name="extension-upgrades-with-multiple-extensions"></a>複数の拡張機能での拡張機能のアップグレード

Arc 対応サーバーによって管理されるマシンでは、拡張機能の自動アップグレードを有効にして複数の拡張機能を使用できます。 同じマシンに、拡張機能の自動アップグレードを有効にしないで他の拡張機能を追加することもできます。

1 つのマシンで拡張機能のアップグレードを複数使用できる場合、アップグレードをバッチにまとめることができる場合がありますが、マシンでは各拡張機能のアップグレードが個々に適用されます。 1 つの拡張機能でエラーが発生しても、アップグレードされる他の拡張機能に影響は及びません。 たとえば、2 つの拡張機能のアップグレードがスケジュールされていて、最初の拡張機能のアップグレードが失敗した場合でも、2 つ目の拡張機能はアップグレードされます。

## <a name="disable-automatic-extension-upgrade"></a>拡張機能の自動アップグレードを無効にする

拡張機能の自動アップグレード (プレビュー) を拡張機能に対して無効にするには、プロパティ `enable-auto-upgrade` が `false` に設定されていて、どの拡張機能の定義にも個々に追加されていることを確認する必要があります。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

`--name`、`--machine-name`、`--enable-auto-upgrade`、`--resource-group` の各パラメーターを指定して、[az connectedmachine extension ](/cli/azure/connectedmachine/extension) コマンドレットを使用します。

```azurecli
az connectedmachine extension update \
    --resource-group resourceGroupName \
    --machine-name machineName \
    --name DependencyAgentLinux \
    --enable-auto-upgrade false
```

## <a name="next-steps"></a>次の手順

- [Azure CLI](manage-vm-extensions-cli.md)、[PowerShell](manage-vm-extensions-powershell.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、および削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。
