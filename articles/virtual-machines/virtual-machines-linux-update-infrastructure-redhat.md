<properties
   pageTitle="Red Hat Update Infrastructure (RHUI) | Microsoft Azure"
   description="Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure (RHUI) について説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure (RHUI)

Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた Red Hat Update Infrastructure (RHUI) にアクセスするよう登録されています。オンデマンド RHEL インスタンスは、地域の yum リポジトリへのアクセス権を持ち、増分更新を受信することができます。

RHUI によって管理される yum リポジトリの一覧は、プロビジョニング中に RHEL インスタンスで構成されます。特別な構成を行う必要はありません。RHEL インスタンスを実行して最新の更新を取得した後に `yum update` を実行するだけです。

## RHUI の概要
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) では、Red Hat 認定クラウド プロバイダーがホストしている Red Hat Enterprise Linux クラウド インスタンス用に yum リポジトリ コンテンツを管理するために、拡張性の高いソリューションを提供します。アップストリームの Pulp プロジェクトに基づいて、RHUI では、クラウド プロバイダーが Red Hat でホストされるリポジトリ コンテンツをローカルでミラーし、独自のコンテンツを使ったカスタムのリポジトリを作成し、これらのリポジトリを負荷分散されたコンテンツ配信システムを介してエンド ユーザーの大規模グループが利用できるようにします。

## RHUI を使用できるリージョン
RHUI は、RHEL のオンデマンド イメージが提供されているすべてのリージョンで利用できます。これには、現在 [Azure の状態ダッシュボード](https://azure.microsoft.com/status/) ページに掲載されているすべてのパブリック リージョンが含まれます。RHEL のオンデマンド イメージからプロビジョニングされた VM の料金には、RHUI の利用料が含まれています。地域/国ごとのクラウド製品リリースは、RHEL のオンデマンド提供が今後拡大されていくにつれて変化します。

> [AZURE.NOTE] Azure でホストされている RHUI の利用は、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。

## 更新プログラムを他の更新リポジトリから取得する

更新プログラムを別の (Azure でホストされている RHUI 以外の) 更新リポジトリから取得する必要がある場合、RHUI からインスタンスの登録をいったん解除した後、必要な更新インフラストラクチャ (Red Hat Satellite、Red Hat カスタマー ポータル CDN など) に登録し直す必要があります。そうしたサービスと [Azure の Red Hat Cloud Access](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure) の登録には、適切な Red Hat サブスクリプションが必要となります。

RHUI の登録を解除し、目的の更新インフラストラクチャに登録し直すには、次の手順に従います。

1.	/etc/yum.repos.d/rh-cloud.repo を編集し、`enabled=1` をすべて `enabled=0` に変更します。次に例を示します。

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf を編集し、`enabled=0` を `enabled=1` に変更します。
3.	次に、目的のインフラストラクチャ (Red Hat カスタマー ポータルなど) に登録します。[Red Hat カスタマー ポータルにシステムを登録してサブスクライブする方法](https://access.redhat.com/solutions/253273)については、Red Hat ソリューション ガイドに従ってください。

> [AZURE.NOTE] RHEL Pay-As-You-Go (PAYG: 従量課金) イメージの料金には、Azure でホストされる RHUI の利用料が含まれています。Azure でホストされている RHUI から PAYG RHEL VM の登録を解除しても、仮想マシンが BYOL (Bring-Your-Own-License: ライセンス持ち込み) タイプの VM に変換されるわけではありません。そのため同じ VM を別の更新ソースに登録した場合、二重に料金が発生する可能性があります。
> 
> Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、[Azure 用の Red Hat ベースの仮想マシンを作成してアップロードする方法](virtual-machines-linux-redhat-create-upload-vhd.md)についての記事を参照して、独自の (BYOL タイプの) イメージを作成し、デプロイすることを検討してください。

## 次のステップ
Azure Marketplace の従量課金イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を利用するには、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) にアクセスしてください。特別な設定を行わなくても、RHEL インスタンスで `yum update` を使用することができます。

<!---HONumber=AcomDC_0713_2016-->