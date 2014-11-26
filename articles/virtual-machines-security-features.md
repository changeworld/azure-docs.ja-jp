<properties title="Security offerings for Azure Virtual Machines" pageTitle="Security offerings for Azure Virtual Machines" description="Quick overview of key security features for Azure VMs and links to details" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Azure Virtual Machines のセキュリティに関する提言

Azure Virtual Machines のセキュリティ タスクと構成は、ほとんどが他の仮想マシンや物理コンピューターの場合と同様です。これには、強力なパスワードを使用すること、よく知られたユーザー名を使用しないこと、オペレーティング システムとアプリケーションの更新に関するポリシーとスケジュールを確立することなどの基本事項が含まれます。加えて、以下の実践をお勧めすると共に、これらを実施するための機能を示します。

-   ウイルス対策またはマルウェア対策を実行する

-   エンドポイントに対するネットワーク アクセス制御リスト (ACL) を使用する

## ウイルス対策またはマルウェア対策を実行する

Azure はウイルスまたはマルウェアへの対策に使用できる数種類のソリューションを提供していますが、その管理はユーザーが行います。たとえば、スキャンの実行や更新のインストールを実行する時期は、ユーザーが決定します。仮想マシンの作成時やそれ以降に、ウイルス対策を追加することができます。現在、セキュリティ拡張機能として次の 3 つのソリューションが提供されており、新規および既存の仮想マシンにインストールできます。

-   [How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)][How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)]
-   [How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)][How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)]
-   [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ][Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ]

## 仮想マシン エンドポイントに対するネットワーク アクセス制御リスト (ACL) を使用する

ネットワーク アクセス制御リスト (ACL) を使用すると、仮想マシン エンドポイントで受信するトラフィックを選択的に許可または拒否できます。このパケット フィルタリング機能は、セキュリティ レイヤーを追加します。この機能の詳細と手順のリンクについては、「[ネットワーク アクセス制御リスト (ACL) について][ネットワーク アクセス制御リスト (ACL) について]」を参照してください。

  [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ]: http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/
