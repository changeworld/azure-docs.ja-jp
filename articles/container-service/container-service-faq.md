---
title: Azure Container Service - FAQ | Microsoft Docs
description: "Docker コンテナー アプリを実行するための仮想マシンのクラスターを簡単に作成、構成、および管理できるサービス、Azure Container Service に関してよく寄せられる質問と回答です。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Mesos, Azure, Kubernetes"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 3bb83f231d16819e5f5da7edbc9fc3f38baff011
ms.openlocfilehash: b0c5efa595b0377d7ae2d936d0394667356d18c9


---
# <a name="frequently-asked-questions-azure-container-service"></a>よく寄せられる質問: Azure Container Service


## <a name="orchestrators"></a>オーケストレーター

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service では、どのコンテナー オーケストレーターがサポートされていますか。 

オープンソースの DC/OS、Docker Swarm、および Kubernetes がサポートされています。 DC/OS と Docker Swarm のサポートは一般に提供されていますが、Kubernetes のサポートは現時点ではプレビュー段階です。 詳細については、[概要](container-service-intro.md)に関するページを参照してください。
 
### <a name="do-you-support-swarm-mode"></a>Swarm モードはサポートされていますか。 

現時点では Swarm モードはサポートされていませんが、サービス ロードマップの一部として予定されています。 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service では Windows コンテナーはサポートされていますか。  

現時点でサポートされているのは、Linux コンテナーのみです。 サービス ロードマップでは、オーケストレーターとして DC/OS、Docker Swarm、Kubernetes を使用する Windows コンテナーのサポートが予定されています。 

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service では、特定のオーケストレーターが推奨されますか。 
基本的には、推奨される特定のオーケストレーターはありません。 サポートされているオーケストレーターのいずれかを使用した経験をお持ちの場合は、その経験を Azure Container Service でも活かすことができます。 ただし、データのトレンドから判断すると、ビッグ データや IoT のワークロードについては DC/OS に運用環境での実績があり、クラウド ネイティブのワークロードには Kubernetes が適しているようです。また、Docker Swarm は Docker ツールとの統合や習得の容易さに定評があります。

また、シナリオによっては、他の Azure サービスを使用してカスタムのコンテナー ソリューションを構築し、管理することもできます。 使用できる Azure サービスには、[Virtual Machines](../virtual-machines/virtual-machines-linux-azure-overview.md)、[Service Fabric](../service-fabric/service-fabric-overview.md)、[Web Apps](../app-service-web/app-service-web-overview.md)、[Batch](../batch/batch-technical-overview.md) などがあります。  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service と ACS Engine の違いは何ですか。 
Azure Container Service は SLA による保証が付いた Azure サービスで、Azure Portal の各種機能、Azure コマンドライン ツール、Azure API が付属しています。 Azure Container Service を使用すると、比較的少ない選択肢を選んで構成するだけで、標準のコンテナー オーケストレーション ツールを実行するクラスターを簡単に実装して管理できます。 

[ACS Engine](http://github.com/Azure/acs-engine) はオープンソース プロジェクトで、パワー ユーザーがクラスター構成を各レベルでカスタマイズするのに適しています。 インフラとソフトウェアの両方の構成に変更を加えることができるため、ACS Engine には SLA が用意されていません。 サポートは、Microsoft の公式チャネルではなく、GitHub のオープンソース プロジェクトを通じて提供されます。 

## <a name="cluster-management"></a>クラスターの管理

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>クラスターの SSH キーはどのようにして作成するのですか。

クラスターの Linux 仮想マシンへの認証に使用する SSH RSA 公開キーと秘密キーのペアは、オペレーティング システムに搭載された標準のツールを使用して作成できます。 作成方法については、[OS X と Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) または [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) のガイダンスを参照してください。 

[Azure CLI 2.0 (プレビュー) のコマンド](container-service-create-acs-cluster-cli.md)を使用してコンテナー サービス クラスターをデプロイする場合は、クラスター用の SSH キーを自動的に生成することができます。

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes クラスターのサービス プリンシパルはどのようにして作成するのですか。

Azure Active Directory サービス プリンシパルの ID とパスワードが、Azure Container Service で Kubernetes クラスターを作成する際にも必要です。 詳細については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。


[Azure CLI 2.0 (プレビュー) のコマンド](container-service-create-acs-cluster-cli.md)を使用して Kubernetes クラスターをデプロイする場合は、クラスターのサービス プリンシパルを自動的に生成することができます。


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>クラスターの作成後にマスターの数を増やすにはどうすればよいですか。 
クラスターを作成すると、マスターの数は固定され、変更できなくなります。 マスターの数は、高可用性を確保するために、クラスターの作成時に&3; つまたは&5; つを選択するのが理想です。

> [!NOTE]
> プレビューでは、Azure Container Service の Kubernetes クラスターに設定できるマスターは&1; つのみです。
>

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>クラスターの作成後にエージェントの数を増やすにはどうすればよいですか。 
クラスター内のエージェントの数は、Azure Portal またはコマンドライン ツールを使用して増やすことができます。 詳細については、[Azure Container Service クラスターのスケーリング](container-service-scale.md)に関するページを参照してください。

> [!NOTE]
> プレビューでは、Azure Container Service の Kubernetes クラスターに設定されたエージェントの数は変更できません。 
>

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>マスターとクラスターの URL は何ですか。 
Azure Container Service クラスター リソースの URL は、ユーザーが指定した DNS 名のプレフィックスと、ユーザーがデプロイ時に選択した Azure リージョンに基づいて設定されます。 たとえば、マスター ノードの完全修飾ドメイン名 (FQDN) は次のような形式になります。

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Azure Portal や Azure Resource Explorer などの Azure ツールではクラスターの URL が広く使用されています。
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>クラスターへの SSH 接続文字列はどこで確認できますか。

接続文字列は、Azure Portal か Azure コマンドライン ツールで確認できます。 

1. Azure Portal の場合は、クラスター デプロイのリソース グループに移動します。  

2. **[概要]** をクリックし、**[要点]** にある **[デプロイ]** のリンクをクリックします。 

3. **[デプロイ履歴]** ブレードで、名前が **microsoft-acs** で始まり、その後にデプロイ日が付加されているデプロイをクリックします。 例: microsoft-acs-201701310000  

4. **[Summary (サマリー)]** ページの **[出力]** に、クラスター リンクがいくつか<provided></provided>。 **SSHMaster0** は、コンテナー サービス クラスター内の&1; つ目のマスターに対する SSH 接続文字列です。 

前述のとおり、Azure ツールを使用して、マスターの FQDN を確認することもできます。 マスターへの SSH 接続は、マスターの FQDN と、クラスターの作成時に指定したユーザー名を使用して作成します。 次に例を示します。

```bash
ssh userName@masterFQDN –A –p 22 
```

詳細については、「[Azure Container Service クラスターに接続する](container-service-connect.md)」を参照してください。




## <a name="next-steps"></a>次のステップ

* Azure Container Service の[概要を確認する](container-service-intro.md)。
* [Azure Portal](container-service-deployment.md) または [Azure CLI 2.0 (プレビュー)](container-service-create-acs-cluster-cli.md) を使用して、コンテナー サービス クラスターをデプロイする。


<!--HONumber=Feb17_HO3-->


