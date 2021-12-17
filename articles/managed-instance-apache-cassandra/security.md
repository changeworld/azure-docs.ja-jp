---
title: Azure Managed Instance for Apache Cassandra のセキュリティ - 概要
description: Azure Managed Instance for Apache Cassandra が提供するデータベース セキュリティのベストプラクティスと主な機能について説明します。 これらは、データベース侵害を防止、検出、対応するために役立ちます。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 96b03347adcabdd5bd25d4f57c5ea80946832610
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723325"
---
# <a name="security-in-azure-managed-instance-for-apache-cassandra---overview"></a>Azure Managed Instance for Apache Cassandra のセキュリティ - 概要

この記事では、データベース侵害の防止、検出、対応に役立つ、Azure Managed Instance for Apache Cassandra が提供するデータベース セキュリティのベスト プラクティスと主な機能について説明します。

## <a name="how-do-i-secure-my-database"></a>データベースをセキュリティ保護する方法

データのセキュリティは、顧客自身とデータベース プロバイダーの共同責任です。 顧客として選択したデータベース プロバイダーによって、引き受ける責任の範囲は変化する可能性があります。 オンプレミス ソリューションを選択した場合、エンドポイントの保護からハードウェアの物理的なセキュリティに至るまでのすべてを顧客自身が提供する必要がありますが、これは簡単な仕事ではありません。 Azure Managed Instance for Apache Cassandra などの管理サービスを選択すると、問題になる領域が少なくなります。 

## <a name="how-does-azure-managed-instance-secure-my-database"></a>Azure Managed Instance によるデータベースのセキュリティ保護方法


|セキュリティ要件|Azure Managed Instance for Apache Cassandra のセキュリティ手法|
|---|---|
|ネットワークのセキュリティ| Azure Managed Instances for Apache Cassandra のリソースは Microsoft テナントでホストされ、各リソースのネットワーク インターフェイス カード (NIC) は、プライベート IP を使用して仮想ネットワークに排他的に挿入されます。 このサービスでは、パブリック IP は公開されません。|
|オンライン バックアップの自動化|Azure Managed Instance for Apache Cassandra データセンターは、4 時間ごとにバックアップされ、2 日間保持されます。 バックアップは、ローカル ストレージ アカウントに保持されます。|
|削除されたデータの復元|自動化されたオンライン バックアップを使用して、誤って削除したデータを回復することができます。 削除イベント後の約 2 日以内の任意の時点までデータをバックアップできます。|
|HTTPS/SSL/TLS およびディスクの暗号化 | Azure Managed Instance for Apache Cassandra では、すべてのデータが保存時に暗号化されます。 サーバー SSL (TLS 1.2) とノード間の暗号化が適用されます。 クライアント SSL はオプション構成です。 ディスク上のデータを暗号化するためのカスタマー マネージド キーがサポートされています。詳細については、[こちら](customer-managed-keys.md)の記事をご覧ください。 |
|攻撃の監視|Azure Managed Instance for Apache Cassandra は [Azure Monitor](../azure-monitor/overview.md) と統合されています。 監査ログとアクティビティ ログを使用することで、アカウントの通常のアクティビティと異常なアクティビティを監視できます。 リソースに対して実行された操作、操作を開始した人物、操作の発生日時、操作の状態、その他の多くのタスクを確認できます。|
|攻撃への対応|Azure のサポートに連絡して攻撃の可能性を報告すると、5 段階のインシデント対応プロセスが開始されます。 この 5 段階のプロセスの目標は、問題が検出され、調査が開始された後、通常のサービスのセキュリティと操作を可能な限り早急に復元することです。|
|サーバーへの修正プログラムの適用|管理されたデータベースとして Azure Managed Instance for Apache Cassandra を使用すると、サーバーの管理と修正プログラムの適用を行う必要がなくなり、それらの作業が自動的に行われます。|
|認定資格| 認定の最新の一覧については、全般的な [Azure コンプライアンスのサイト](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)をご覧ください。


## <a name="next-steps"></a>次のステップ

Microsoft の認定の詳細については、[Azure トラスト センター](https://azure.microsoft.com/support/trust-center/)に関するページを参照してください。
