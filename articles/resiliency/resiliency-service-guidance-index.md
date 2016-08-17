<properties
   pageTitle="サービスの回復性ガイダンス | Microsoft Azure"
   description="Microsoft Azure サービスの障害復旧、予防的回復性、可用性のガイダンスのリンク。"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="aglick"/>

# Microsoft Azure サービスの回復性ガイダンス
Microsoft Azure は、必要なときに必要なリソースを提供できるように設計されています。設計と運用を適切に実践するには、高い可用性を実現するように Azure サービスの利用を節制する方法と、サービス停止の影響をアプリケーションが受けたときの措置の両方を知っておく必要があります。このプロセスを支援する目的で、このドキュメントには、障害復旧ガイダンスとさまざまな Azure サービスの設計ガイダンスのリンクが含まれています。

##障害復旧ガイダンス
下の障害復旧ガイダンスのリンクでは、Azure サービスが停止し、影響が出た場合に、アプリケーションをオンラインに戻すために必要な情報があります。これらのリンクは、「Azure サービスが停止し、影響が出ていますか。どうすればよいでしょうか。」という質問に答えるために作られました。

##設計ガイダンス
下の設計ガイダンスのリンクからは、設計上の指南にアクセスできます。それぞれの Azure サービスを最大限に利用し、アプリケーションのアップタイムを最大化する方法を理解できます。これらのリンクは、「バグ、ハードウェアの故障、サービスの停止、その他の障害が発生しても、アプリケーションの全体的可用性に影響を与えないようにするにはどうすればよいでしょうか。」という質問に答えるために作られました。 現在探しているサービスに関する具体的なガイダンスがない場合、「[High availability for applications built on Microsoft Azure (Microsoft Azure 上に構築されたアプリケーションの高可用性)](./resiliency-high-availability-azure-applications.md)」を参照すると、設計に役立つ追加情報が得られる可能性があります。　

##サービス ガイダンス
| サービス | 障害復旧ガイダンス | 設計ガイダンス |
|:---------|:--------------------------:|:------------------:|
| [Cloud Services](https://azure.microsoft.com/services/cloud-services/ "Azure Cloud Services (Azure Cloud Services)") | [link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Azure Cloud Services の障害復旧ガイダンス") | 利用不可 |
| [Key Vault](https://azure.microsoft.com/services/key-vault/ "Azure Key Vault") | [link](../key-vault/key-vault-disaster-recovery-guidance.md "Azure Key Vault の障害復旧ガイダンス") | 利用不可 |
| [Storage](https://azure.microsoft.com/services/storage/ "Azure Storage (Azure Storage)") | [link](../storage/storage-disaster-recovery-guidance.md "Azure Storage の障害復旧ガイダンス") | 利用不可 |
| [SQL データベース](https://azure.microsoft.com/services/sql-database/ "Azure SQL Database") | [link](../sql-database/sql-database-disaster-recovery.md "Azure SQL Database の障害復旧ガイダンス") | [link](../sql-database/sql-database-business-continuity-design.md "Azure SQL Databases の設計ガイダンス") |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/ "Azure Virtual Machines") | [link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Azure Virtual Machines の障害復旧ガイダンス") | 利用不可 |
| [Virtual Network](https://azure.microsoft.com/services/virtual-network/ "Azure 仮想ネットワーク") | [link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Azure Virtual Network の障害復旧ガイダンス") | 利用不可 |

##次のステップ
システムとソリューションをより包括的に取り上げたガイダンスが必要であれば、「[Disaster recovery and high availability for applications built on Microsoft Azure (Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性)](https://aka.ms/drtechguide)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->