# [Virtual Machine Scale Sets のドキュメント](index.yml)

# 概要
## [仮想マシン スケール セットとは](overview.md)

# クイック スタート
## [Azure Portal で作成する](quick-create-portal.md)
## [Azure CLI 2.0 を使って作成する](quick-create-cli.md)
## [Azure PowerShell を使って作成する](quick-create-powershell.md)
## テンプレートを使って作成する
### [Linux スケール セット](quick-create-template-linux.md)
### [Windows スケール セット](quick-create-template-windows.md)

# チュートリアル
## 1 - スケール セットの作成/管理
### [Azure CLI 2.0](tutorial-create-and-manage-cli.md)
### [Azure PowerShell](tutorial-create-and-manage-powershell.md)
## 2 - データ ディスクの使用
### [Azure CLI 2.0](tutorial-use-disks-cli.md)
### [Azure PowerShell](tutorial-use-disks-powershell.md)
## 3 - カスタム VM イメージの使用
### [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
### [Azure PowerShell](tutorial-use-custom-image-powershell.md)
## 4 - スケール セットへのアプリのデプロイ
### [Azure CLI 2.0](tutorial-install-apps-cli.md)
### [Azure PowerShell](tutorial-install-apps-powershell.md)
### [テンプレート](tutorial-install-apps-template.md)
## 5 - スケール セットの自動スケーリング
### [Azure CLI 2.0](tutorial-autoscale-cli.md)
### [Azure PowerShell](tutorial-autoscale-powershell.md)
### [テンプレート](tutorial-autoscale-template.md)
## カスタム Packer イメージでの Azure 仮想マシン スケール セットへのアプリのデプロイ
### [Azure CLI 2.0](https://docs.microsoft.com/learn/deploy-custom-vmss-app/index)

# サンプル
## [Azure CLI 2.0](cli-samples.md)
## [PowerShell](powershell-samples.md)

# 方法
## 計画と設計
### [設計上の考慮事項](virtual-machine-scale-sets-design-overview.md)
### [インスタンス ID について](virtual-machine-scale-sets-instance-ids.md)

## テンプレートの作成
### [スケール セット テンプレートについて](virtual-machine-scale-sets-mvss-start.md)
### [既存の仮想ネットワークの使用](virtual-machine-scale-sets-mvss-existing-vnet.md)
### [カスタム イメージの使用](virtual-machine-scale-sets-mvss-custom-image.md)
### [Linux スケール セット テンプレートによるゲストベースの自動スケールの使用](virtual-machine-scale-sets-mvss-guest-based-autoscale-linux.md)

## デプロイ
### [Visual Studio での作成](virtual-machine-scale-sets-vs-create.md)
### [可用性ゾーンの使用](virtual-machine-scale-sets-use-availability-zones.md)
### [スケール セットの自動スケール](virtual-machine-scale-sets-autoscale-overview.md)
#### [Azure Portal の使用](virtual-machine-scale-sets-autoscale-portal.md)
#### [高度な自動スケール](../monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [スケール セット上のアプリケーション](virtual-machine-scale-sets-deploy-app.md)
### [スケール セットでデータ ディスクを使用する](virtual-machine-scale-sets-attached-disks.md)
### スケール セット内のディスクの暗号化
#### [PowerShell の使用](virtual-machine-scale-sets-encrypt-disks-ps.md)
#### [Azure CLI 2.0 の使用](virtual-machine-scale-sets-encrypt-disks-cli.md)
### [大規模なスケール セットを処理する](virtual-machine-scale-sets-placement-groups.md)
### [スケール セット テンプレートを変換して管理ディスクを使用する](virtual-machine-scale-sets-convert-template-to-md.md)
### [優先順位の低い VM を使用する](virtual-machine-scale-sets-use-low-priority.md)

## [管理]
### 一般的な管理タスク
#### [Azure CLI 2.0 の使用](virtual-machine-scale-sets-manage-cli.md)
#### [Azure PowerShell の使用](virtual-machine-scale-sets-manage-powershell.md)
### [スケール セットの垂直スケール](virtual-machine-scale-sets-vertical-scale-reprovision.md)
### [OS の自動アップグレード](virtual-machine-scale-sets-automatic-upgrade.md)
### [スケール セットの変更](virtual-machine-scale-sets-upgrade-scale-set.md)
### [DSC とスケール セットの使用](virtual-machine-scale-sets-dsc.md)
### [スケール セットのネットワーク](virtual-machine-scale-sets-networking.md)
### [テンプレートを管理ディスクに変換する](virtual-machine-scale-sets-convert-template-to-md.md)

## トラブルシューティング
### [Autoscale](virtual-machine-scale-sets-troubleshoot.md)

## FAQ
### [スケール セットの FAQ](virtual-machine-scale-sets-faq.md)

# リファレンス
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](../virtual-machines/azure-cli-arm-commands.md)
## [REST](/rest/api/virtualmachinescalesets/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=compute)
## 価格 
### [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)
### [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-vm-scale-set)
