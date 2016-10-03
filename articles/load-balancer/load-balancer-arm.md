<properties
   pageTitle="Azure リソース マネージャーによる Load Balancer のサポート - プレビュー | Microsoft Azure "
   description="Azure Resource Manager での Load Balancer に対する PowerShell の使用はプレビュー段階です。ロード バランサーにテンプレートを使用します。"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2016"
   ms.author="sewhee" />


# Azure リソース マネージャーによる Load Balancer のサポート

Azure Resource Manager は、Azure のサービスの優先管理フレームワークです。Azure Resource Manager ベースの API とツールを使用して Azure Load Balancer を管理できます。

## 概念

Resource Manager を使用する場合、Azure Load Balancer には次の子リソースが含まれています。

- フロントエンド IP 構成: ロード バランサーには、仮想 IP (VIP) とも呼ばれる 1 つ以上のフロントエンド IP アドレスを含めることができます。これらの IP アドレスは、受信トラフィック用として機能します。

- バックエンド アドレス プール: 負荷分散先の仮想マシンのネットワーク インターフェイス カード (NIC) に関連付けられた IP アドレスです。

- 負荷分散規則: 規則のプロパティでは、特定のフロントエンド IP とポートの組み合わせを、一連のバックエンド IP アドレスとポートの組み合わせにマップします。1 つのロード バランサーで複数の負荷分散規則を使用できます。各規則は、VM に関連付けられた、フロントエンド IP/ポートとバックエンド IP/ポートの組み合わせです。

- プローブ: プローブを使用すると、VM インスタンスの正常性を追跡できます。正常性プローブが失敗した場合、VM インスタンスは自動的にローテーションから除外されます。

- 受信 NAT 規則: フロントエンド IP を通過し、バックエンド IP に配信される受信トラフィックを定義する NAT 規則です。

![](./media/load-balancer-arm/load-balancer-arm.png)

## クイック スタート テンプレート

Azure リソース マネージャーでは、宣言型のテンプレートを使用してアプリケーションをプロビジョニングすることができます。1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。

テンプレートには、Virtual Machines、Virtual Network、可用性セット、ネットワーク インターフェイス (NIC)、ストレージ アカウント、Load Balancer、ネットワーク セキュリティ グループ、パブリック IP の定義を含めることができます。テンプレートを使用して、複雑なアプリケーションに必要なあらゆるものを作成できます。テンプレート ファイルは、バージョン管理とコラボレーション用のコンテンツ管理システムにチェックインできます。

[テンプレートの詳細](http://go.microsoft.com/fwlink/?LinkId=544798)

[ネットワーク リソースの詳細](../virtual-network/resource-groups-networking.md)

Azure Load Balancer を使用したクイックスタート テンプレートは、[GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)にあります。ここでは、コミュニティで作成された一連のテンプレートがホストされています。

テンプレートの例を次に示します。

- [ロード バランサー内の 2 台の VM と負荷分散規則](http://go.microsoft.com/fwlink/?LinkId=544799)
- [内部ロード バランサーを使用した VNET 内の 2 台の VM と負荷分散規則](http://go.microsoft.com/fwlink/?LinkId=544800)
- [ロード バランサー内の 2 台の VM と LB の NAT 規則の構成](http://go.microsoft.com/fwlink/?LinkId=544801)


## CLI または PowerShell を使用した Azure Load Balancer の設定

Azure Resource Manager コマンドレット、コマンド ライン ツール、REST API を使用する場合は、以下をご覧ください。

- [Azure ネットワークのコマンドレット](https://msdn.microsoft.com/library/azure/mt163510.aspx)を使用して、ロード バランサーを作成できます。
- [Azure リソース マネージャーを使用したロード バランサーの作成方法](load-balancer-get-started-ilb-arm-ps.md)
- [Azure リソース管理での Azure CLI の使用](../xplat-cli-azure-resource-manager.md)
- [Load Balancer REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## 次のステップ

[インターネットに接続するロード バランサーの作成を開始](load-balancer-get-started-internet-arm-ps.md)し、特定のロード バランサーのネットワーク トラフィックの動作に適した種類の[分散モード](load-balancer-distribution-mode.md)を構成することもできます。

[ロード バランサーの TCP アイドル タイムアウト設定](load-balancer-tcp-idle-timeout.md)を管理する方法を確認します。これは、ロード バランサーの背後にあるサーバーに対してアプリケーションの接続を維持する必要がある場合に重要となります。

<!---HONumber=AcomDC_0921_2016-->