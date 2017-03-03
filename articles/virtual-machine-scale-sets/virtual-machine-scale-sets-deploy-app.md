---
title: "仮想マシン スケール セットでのアプリのデプロイ | Microsoft Docs"
description: "仮想マシン スケール セットへのアプリケーションのデプロイ"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: f13545d753690534e0e645af67efcf1b524837eb
ms.openlocfilehash: dad27b11b5f02ed41826b82882cc5089eb69cb04
ms.lasthandoff: 02/09/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>仮想マシン スケール セットへのアプリケーションのデプロイ
VM スケール セットで実行されているアプリケーションは、通常、次の&3; つのいずれかの方法でデプロイされます。

* デプロイするときにプラットフォーム イメージに新しいソフトウェアをインストールする。 このコンテキストでのプラットフォーム イメージは、Ubuntu 16.04、Windows Server 2012 R2 など、Azure Marketplace からのオペレーティング システム イメージです。

新しいソフトウェアをプラットフォーム イメージにインストールするには、[VM 拡張機能](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を使用します。 VM 拡張機能は、VM をデプロイするときに実行されるソフトウェアです。 カスタム スクリプト拡張機能を使用すると、デプロイ時に好きなコードを実行できます。 [こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)の Azure Resource Manager テンプレートの例には、2 つの VM 拡張機能が用意されています。1 つは Apache や PHP をインストールするための Linux カスタム スクリプト拡張機能、もう&1; つは Azure 自動スケールで使用されるパフォーマンス データを出力する診断拡張機能です。

このアプローチのメリットは、アプリケーション コードと OS の間に分離レベルがあり、アプリケーションを個別に管理できる点です。 もちろん、これは動的なパーツが多くなることを意味しており、ダウンロードして構成するスクリプトに対する動的パーツが多いと、VM デプロイに時間がかかる可能性があります。

**カスタム スクリプト拡張機能コマンドで、パスワードなどの機密情報を渡す場合は、必ずカスタム スクリプト拡張機能の `protectedSettings` 属性で `commandToExecute` を指定してください。`settings` 属性ではありません。**

* 1 つの VHD で OS とアプリケーションの両方を含むカスタム VM イメージを作成する。 この場合、スケール設定は、ご自身で作成したイメージからコピーされた一連の VM で構成されており、これを管理する必要があります。 このアプローチでは、VM をデプロイするときに追加の構成が不要です。 ただし、VM スケール セット `2016-03-30` 以前のバージョンでは、そのスケール セットの VM に対する OS ディスクが&1; つのストレージ アカウントに制限されます。 このため、スケール セットでは最大 40 VM しか使用できません。一方、プラットフォーム イメージにおけるスケール セットあたりの制限は 100 VM です。 詳細については、[スケール セットの設計の概要](virtual-machine-scale-sets-design-overview.md)に関するページをご覧ください。

    >[!NOTE]
    >VM Scale Sets API バージョン `2016-04-30-preview` では、オペレーティング システム ディスクと追加データ ディスクでの Azure Managed Disks の使用がサポートされます。 詳しくは、[Managed Disks の概要](../storage/storage-managed-disks-overview.md)と[接続されたデータ ディスクの使用](virtual-machine-scale-sets-attached-disks.md)に関するページをご覧ください。 

* 基本的にはコンテナー ホストであるプラットフォームまたはカスタム イメージをデプロイし、アプリケーションを、オーケストレーターまたは構成管理ツールで管理する&1; つ以上のコンテナーとしてインストールする。 このアプローチで便利なのは、アプリケーション レイヤーからクラウド インフラストラクチャを抽象化し、切り離して管理できる点です。

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>VM スケール セットがスケール アウトすると、どうなりますか。
容量を増やすことで&1; つ以上の VM をスケール セットに追加すると、手動または自動スケールのどちらを使用した場合でも、アプリケーションは自動的にインストールされます。 たとえば、スケール セットで拡張機能が定義されていると、その拡張機能は、新しい VM が作成されるたびに実行されます。 スケール セットがカスタム イメージに基づいている場合は、新しい VM すべてがソース カスタム イメージのコピーです。 スケール セット VM がコンテナー ホストの場合は、カスタム スクリプト拡張機能にコンテナーを読み込むためのスタートアップ コードが用意されている可能性があります。また、拡張機能によって、クラスター オーケストレーター (Azure Container Service など) に登録するエージェントがインストールされる場合もあります。

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>VM スケール セットではどのようにアプリケーション更新プログラムを管理しますか。
VM スケール セットのアプリケーション更新プログラムでは、前述の&3; つのアプリケーション デプロイ方法から&3; つ方法でアプローチして管理できます。

* VM 拡張機能を更新する。 VM スケール セットに対して定義されている VM 拡張機能はすべて、新しい VM をデプロイする、既存の VM を再イメージ化する、または VM 拡張機能を更新するたびに実行されます。 アプリケーションを更新する必要がある場合は、拡張機能を使用してアプリケーションを直接更新する手法が有効です。この場合、拡張機能の定義を更新するだけですみます。 これを簡単に行う&1; つの方法は、新しいソフトウェアを指定する fileUris を変更することです。

* 変更できないカスタム イメージ アプローチ。 アプリケーション (またはアプリケーション コンポーネント) を VM イメージに組み込むと、信頼性の高いパイプラインを構築して、イメージのビルド、テスト、およびデプロイを自動化することに専念できます。 ステージングされたスケール セットを、迅速かつ容易に本番環境にスワップできるようにアーキテクチャを設計することができます。 このアプローチの典型的な例が、[Azure Spinnaker ドライバーの動作](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)です。

Packer および Terraform Azure でも Azure Resource Manager がサポートされているため、画像を "コードとして" 定義し、Azure でビルドしてから、スケール セットで VHD を使用することもできます。 ただし、Marketplace イメージでは、これが問題になることがあります。Marketplace からはビットを直接操作しないため、拡張機能/カスタム スクリプトがより重要になります。

* コンテナーを更新する。 たとえば、アプリケーションおよびアプリケーション コンポーネントをコンテナーにカプセル化することで、アプリケーションのライフサイクル管理を、クラウド インフラストラクチャの上のレベルに抽象化し、そのコンテナーを、Chif/Puppet などのコンテナーのオーケストレーターおよび構成マネージャーで管理します。

スケール セット VM はコンテナーの安定したサブストレートとなり、セキュリティと OS 関連の更新プログラムも稀にしか必要になりません。 前に説明したように、このアプローチを採用し、それに基づいてサービスを構築している典型的な例が Azure Container Service です。

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>OS の更新プログラムはどのように更新ドメインに展開するのでしょうか。
VM スケール セットを実行しながら、OS イメージを更新する必要があるとします。 これを行う&1; つの方法が、VM イメージを&1; つの VM ごとに更新することです。 それには、PowerShell または Azure CLI を使用します。 VM スケール セット モデル (その構成の定義方法) を更新して、個別の VM で "手動アップグレード" 呼び出しを発行するコマンドも別にあります。 Azure のドキュメント「[仮想マシン スケール セットのアップグレード](./virtual-machine-scale-sets-upgrade-scale-set.md)」でも、VM スケール セット全体で OS をアップグレードするときに利用できるオプションについて詳しく説明しています。


