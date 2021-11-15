---
title: Azure NetApp Files のアプリケーションの回復性に関する FAQ | Microsoft Docs
description: Azure NetApp Files のアプリケーションの回復性についてのよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 3a0b243203c0edb01bc5b647a15093ee52b78e7c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270067"
---
# <a name="application-resilience-faqs-for-azure-netapp-files"></a>Azure NetApp Files のアプリケーションの回復性に関する FAQ

この記事では、Azure NetApp Files のアプリケーションの回復性についてのよくあるご質問 (FAQ) に回答します。

## <a name="what-do-you-recommend-for-handling-potential-application-disruptions-due-to-storage-service-maintenance-events"></a>ストレージ サービスのメンテナンス イベントによって発生する可能性のあるアプリケーションの中断に対処するために推奨されることは何ですか?

Azure NetApp Files では、時々、計画メンテナンス (プラットフォームの更新、サービスやソフトウェアのアップグレードなど) が行われることがあります。 ファイル プロトコル (NFS/SMB) の観点からは、これらのイベント中に一時的に発生する可能性がある IO の一時停止をアプリケーションで処理できる限り、メンテナンス操作による中断はありません。 I/O の一時停止は通常、数秒から 30 秒程度の短い時間です。 NFS プロトコルは特に堅牢で、クライアントとサーバー間のファイル操作は通常通り行われます。 一部のアプリケーションでは、30 秒から 45 秒程の IO の一時停止に対応するためのチューニングが必要な場合があります。 そのため、ストレージ サービスのメンテナンス イベントに対処するために、アプリケーションの回復性設定を確認するようにしてください。 SMB プロトコルを利用する、ユーザー介入型のアプリケーションの場合は、通常、標準のプロトコル設定で十分です。 

## <a name="do-i-need-to-take-special-precautions-for-smb-based-applications"></a>SMB ベースのアプリケーションには、特別な予防措置を講じる必要がありますか?

はい。特定の SMB ベースのアプリケーションでは、SMB 透過フェールオーバーが必要です。 SMB 透過フェールオーバーを使用すると、SMB ボリュームにデータを格納してアクセスするサーバー アプリケーションへの接続を中断することなく、Azure NetApp Files サービスでメンテナンス操作を行うことができます。 特定のアプリケーションの SMB 透過フェールオーバーをサポートするために、Azure NetApp Files では、[SMB 継続的可用性共有オプション](azure-netapp-files-create-volumes-smb.md#continuous-availability)がサポートされるようになりました。 

## <a name="i-am-running-ibm-mq-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>IBM MQ を Azure NetApp Files で実行しています。 NFS プロトコルを使用しているにもかかわらずストレージ サービスのメンテナンス イベントによって発生する中断を回避するには、どのような予防措置を講じることができますか?

[IBM MQ アプリケーションを共有ファイル構成](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-sharing-mq-files)で実行しており、IBM MQ データとログが Azure NetApp Files ボリュームに保存されている場合、ストレージ サービスのメンテナンス イベント時の回復力を高めるために、以下の考慮事項が推奨されます。

* NFS v4.1 プロトコルのみを使用する必要があります。
* 高可用性を実現するためには、[共有 NFS v4.1 ボリュームを使用した IBM MQ マルチインスタンス構成を使用する必要があります](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=manager-create-multi-instance-queue-linux)。 
* [共有 NFS v4.1 ボリュームを使用して、IBM マルチインスタンス構成](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-verifying-shared-file-system-behavior)の機能を確認する必要があります。 
* 1 つの大規模なマルチインスタンス IBM MQ 構成を使用するのではなく、スケールアウト IBM MQ アーキテクチャを実装する必要があります。 複数の IBM MQ マルチインスタンス ペアにメッセージ処理負荷を分散することで、各 MQ マルチインスタンス ペアで処理するメッセージの数が減るため、サービスが中断される可能性が減ることが考えられます。

> [!NOTE] 
> 各 MQ マルチインスタンス ペアが処理する必要があるメッセージの数は、ユーザーごとの特定の環境に大きく依存します。 ユーザーは、必要な MQ マルチインスタンス ペアの数や、スケールアップまたはスケールダウン ルールの内容を決定する必要があります。

スケールアウト アーキテクチャでは、Azure Load Balancer の背後に複数の IBM MQ マルチインスタンス ペアがデプロイされています。 IBM MQ と通信するように構成されたアプリケーションは、Azure Load Balancer を介して IBM MQ インスタンスと通信するように構成されます。 共有 NFS ボリューム上の IBM MQ に関連するサポートについては、IBM でベンダー サポートを受け取る必要があります。

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>LevelDB または KahaDB を使用して Apache ActiveMQ を Azure NetApp Files で実行しています。 *NFS* プロトコルを使用しているにもかかわらずストレージ サービスのメンテナンス イベントによって発生する中断を回避するには、どのような予防措置を講じることができますか?

>[!NOTE]
> このセクションには、Microsoft では使用されなくなった "*スレーブ*" と "*マスター*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

Apache ActiveMQ を実行している場合は、[Pluggable Storage Locker 付きの ActiveMQ High Availability](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) を導入することをお勧めします。 

ActiveMQ の高可用性 (HA) モデルを使用すると、ブローカー インスタンスが常にオンラインで、メッセージ トラフィックを処理できることが保証されます。 ActiveMQ の HA モデルで最も一般的なものは、ネットワーク上でファイルシステムを共有するものです。 これは、アクティブおよびパッシブなブローカー インスタンスに、LevelDB または KahaDB のいずれかを提供することが目的です。 これらの HA モデルでは、LevelDB または KahaDB ディレクトリ内のファイルに対して、"ロック" と呼ばれる OS レベルのロックを取得して維持する必要があります。 この ActiveMQ HA モデルには、いくつかの問題があります。 まず、"スレーブ" がファイルをロックできることを認識していない、"マスター不在" の状況になる可能性があります。  また、インデックスやジャーナルが破損し、最終的にメッセージが失われてしまう "マスターマスター" 構成につながる可能性もあります。 これらの問題の多くは、ActiveMQ の制御外の要因に起因します。 例えば、NFS クライアントの最適化が不十分だと、負荷がかかったときにロックされたデータが古くなり、フェールオーバー時に "マスターなし" のダウンタイムが発生する可能性があります。 

この HA ソリューションに関する問題の大部分は、OS レベルの不正確なファイル ロックに起因しているため、ActiveMQ コミュニティにより、ブローカーのバージョン 5.7 で [Pluggable Storage Locker という概念](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq)が導入されました。 この方法を使用すると、ユーザーは OS レベルのファイルシステム ロックではなく、行レベルの JDBC データベース ロックを使用することで、異なる共有ロックの手段を利用することができます。 ActiveMQ の HA アーキテクチャやデプロイに関するサポートやコンサルティングについては、[Perforce が提供する OpenLogic](https://www.openlogic.com/contact-us) にお問い合わせください。

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despites-using-the-smb-protocol"></a>LevelDB または KahaDB を使用して Apache ActiveMQ を Azure NetApp Files で実行しています。 *SMB* プロトコルを使用しているにもかかわらずストレージ サービスのメンテナンス イベントによって発生する中断を回避するには、どのような予防措置を講じることができますか?

業界の一般的な推奨事項は、[KahaDB の共有ストレージを CIFS/SMB で運用しない](https://www.openlogic.com/blog/activemq-community-deprecates-leveldb-what-you-need-know)ことです。 正確なロック状態の維持に問題がある場合は、より信頼性の高いロック メカニズムを提供する JDBC Pluggable Storage Locker をチェックしてください。 ActiveMQ の HA アーキテクチャやデプロイに関するサポートやコンサルティングについては、[Perforce が提供する OpenLogic](https://www.openlogic.com/contact-us) にお問い合わせください。

## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [統合に関する FAQ](faq-integration.md)

