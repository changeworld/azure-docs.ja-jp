<properties
   pageTitle="Azure リソース マネージャーによるロード バランサーのサポート - プレビュー | Microsoft Azure"
   description="Azure リソース マネージャー (ARM) でのロード バランサーに対する PowerShell の使用はプレビュー段階です。ロード バランサーにテンプレートを使用します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/16/2015"
   ms.author="joaoma" />


# Azure リソース マネージャーによるロード バランサーのサポート 

Azure リソース マネージャー (ARM) は、Azure 上のサービス向けの新しい管理フレームワークです。Azure リソース マネージャー ベースの API とツールを使用して Azure ロード バランサーを管理できるようになりました。Azure リソース マネージャーの詳細については、[リソース グループを使用した Azure リソースの管理](../azure-preview-portal-using-resource-groups.md)に関するページを参照してください。

## 概念

ARM を使用する場合、Azure ロード バランサーには次の子リソースが含まれています。

- フロントエンド IP 構成: ロード バランサーには、仮想 IP (VIP) としても知られている 1 つ以上のフロントエンド IP アドレスを含めることができます。これらの IP アドレスは、受信トラフィック用として機能します。

- バックエンド アドレス プール: 負荷分散先の仮想マシンのネットワーク インターフェイス カード (NIC) に関連付けられた IP アドレスです。

- 負荷分散規則: 規則のプロパティでは、特定のフロントエンド IP とポートの組み合わせを、一連のバックエンド IP アドレスとポートの組み合わせにマップします。ロード バランサーのリソースの 1 つの定義によって複数の負荷分散規則を定義できます。各規則は、フロント エンド IP とポートと、VM に関連付けられたバック エンド IP とポートとの組み合わせを反映します。

- プローブ: プローブを使用すると、VM インスタンスの正常性を追跡できます。正常性プローブが失敗した場合、VM インスタンスは自動的にローテーションから除外されます。

- 受信 NAT 規則: フロントエンド IP を通過し、バックエンド IP に配信される受信トラフィックを定義する NAT 規則です。


![](./media/load-balancer-arm/load-balancer-arm.png)



## クイック スタート テンプレート
Azure リソース マネージャーでは、宣言型のテンプレートを使用してアプリケーションをプロビジョニングすることができます。1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用して繰り返しアプリケーションをデプロイします。

テンプレートには、仮想マシン、仮想ネットワーク、可用性セット、ネットワーク インターフェイス (NIC)、ストレージ アカウント、ロード バランサー、ネットワーク セキュリティ グループ、パブリック IP が含まれます。テンプレートによって、チェックインして共同作業できる簡単なファイルを使用して、複雑なアプリケーションに必要なすべてのものを作成できます。

[テンプレートの詳細](http://go.microsoft.com/fwlink/?LinkId=544798)

[ネットワーク リソースの詳細](../resource-groups-networking)

Azure ロード バランサーを使用したテンプレートは、[GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)にあります。ここでは、コミュニティで作成された一連のテンプレートがホストされています。

テンプレートの例を次に示します。

- [ロード バランサー内の 2 台の VM と負荷分散規則](http://go.microsoft.com/fwlink/?LinkId=544799)

- [内部ロード バランサーを使用した VNET 内の 2 台の VM と負荷分散規則](http://go.microsoft.com/fwlink/?LinkId=544800)

- [ロード バランサー内の 2 台の VM と LB の NAT 規則の構成](http://go.microsoft.com/fwlink/?LinkId=544801)


## CLI または PowerShell を使用した Azure ロード バランサーの設定

[Azure ネットワークのコマンドレット](https://msdn.microsoft.com/library/azure/mt163510.aspx)を使用して、ロード バランサーを作成できます。ARM コマンドレットと REST API を使って作業を開始します。

- [Azure リソース マネージャーを使用したロード バランサーの作成方法](../load-balancer-arm-powershell)

- [Azure リソース管理での Azure CLI の使用](../xplat-cli-azure-resource-manager)

- [Load Balancer REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## 関連項目

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=Oct15_HO4-->