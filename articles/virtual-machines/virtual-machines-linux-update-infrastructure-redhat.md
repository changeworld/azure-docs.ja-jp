<properties
   pageTitle="Red Hat Enterprise Linux イメージ向けの更新インフラストラクチャ | Microsoft Azure"
   description="Azure でオンデマンドの Red Hat Enterprise Linux インスタンス向けの yum 更新サービスを導入します"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="kyliel"/>

# Red Hat Enterprise Linux イメージ向けの更新インフラストラクチャ

Azure で Red Hat Update Infrastructure (RHUI) を使用すると、Azure Red Hat Enterprise Linux (RHEL) イメージ用に yum リポジトリ コンテンツを管理できます。Azure Marketplace から作成したオンデマンド RHEL インスタンスは、リージョンの yum リポジトリへのアクセス権があります。これにより、RHEL インスタンスは増分更新を受信することができます。

RHUI によって管理される yum リポジトリの一覧は、プロビジョニング中に RHEL インスタンスで構成されます。そのため、追加の構成を行う必要はありません。RHEL インスタンスを実行してから、`yum update` を実行するだけです。

## RHUI の概要
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) では、Red Hat 認定クラウド プロバイダーがホストしている Red Hat Enterprise Linux クラウド インスタンス用に yum リポジトリ コンテンツを管理するために、拡張性の高いソリューションを提供します。アップストリームの Pulp プロジェクトに基づいて、RHUI では、クラウド プロバイダーが Red Hat でホストされるリポジトリ コンテンツをローカルでミラーし、独自のコンテンツを使ったカスタムのリポジトリを作成し、これらのリポジトリを負荷分散されたコンテンツ配信システムを介してエンド ユーザーの大規模グループが利用できるようにします。

## RHUI を使用できるリージョン
RHUI は、[Azure の状態ダッシュ ボード](https://azure.microsoft.com/status/)に一覧されるすべてのパブリック リージョンで使用できます。つまり、これらのリージョンで追加料金を支払うことなく、yum 更新サービスを取得できることを意味します。今後、この情報は更新されます。

## その他の更新リポジトリから更新プログラムを入手する (Red Hat Network Satellite など)

その他の更新リポジトリから更新プログラムを入手するには、Red Hat Cloud Access ライセンスと Azure に対する既存の Red Hat サブスクリプションを入手する必要があります。

次に、RHUI の登録を解除し、更新インフラストラクチャを再登録する必要があります。詳細な手順を次に示します。

1.	/Etc/yum.repos.d/rh-cloud.repo を編集し、`enabled=1` をすべて `enabled=0` に変更します。次に例を示します。

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	/etc/yum/pluginconf.d/rhnplugin.conf を編集し、`enabled=0` を `enabled=1` に変更します。
3.	次に、Red Hat ネットワーク (RHN) に登録します。

        rhn_register

    または

        rhnreg_ks


> [AZURE.NOTE] 送信データ転送の料金が請求されます (「[料金の詳細](http://azure.microsoft.com/pricing/details/data-transfers/)」を参照)。既定の RHUI を使用して、追加料金を負担することなく増分更新を取得することをお勧めします。

## 次のステップ
Azure の RHEL を理解しましたので、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) から RHEL イメージを作成し、RHEL インスタンスで `yum update` を使用することができます。

<!---HONumber=AcomDC_0323_2016-->