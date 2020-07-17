---
title: FarmBeats のディザスター リカバリー
description: この記事では、データの損失をデータ回復によって防ぐ方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683888"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats のディザスター リカバリー

データ回復によって、Azure リージョンの崩壊などの事態でデータを失うことを防ぐことができます。 このような事態の場合、フェールオーバーを開始し、FarmBeats デプロイに格納されているデータを回復できます。

データ回復は、Azure FarmBeats の既定の機能ではありません。 この機能は、Azure のペアのリージョンにデータを格納するために FarmBeats によって使用される必要な Azure リソースを構成することによって、手動で構成することができます。 アクティブ - パッシブアプローチを使用して、回復を有効にします。

次のセクションで、Azure FarmBeats でデータ回復を構成する方法について説明します。

- [データの冗長性を有効にする](#enable-data-redundancy)
- [オンライン バックアップからサービスを復元する](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>データの冗長性を有効にする

FarmBeats では、3 つの Azure ファースト パーティ サービス (**Azure Storage**、**Cosmos DB**、**Time Series Insights**) にデータが格納されます。 次の手順に従って、これらのサービスのデータの冗長性を、ペアの Azure リージョンに対して有効にします。

1.  **Azure Storage** - このガイドラインに従って、FarmBeats デプロイの各ストレージ アカウントに対してデータの冗長性を有効にします。
2.  **Azure Cosmos DB** - このガイドラインに従って、FarmBeats デプロイの Cosmos DB アカウントに対してデータの冗長性を有効にします。
3.  **Azure Time Series Insights (TSI)** - TSI では現在、データの冗長性は提供されていません。 Time Series Insights データを回復するには、センサーまたは天気パートナーに移動し、FarmBeats デプロイにデータをもう一度プッシュします。

## <a name="restore-service-from-online-backup"></a>オンライン バックアップからサービスを復元する

FarmBeats デプロイの前述の各データ ストアに対して、フェールオーバーを開始し、格納されているデータを回復することができます。 Azure Storage と Cosmos DB のデータを回復したら、Azure のペアのリージョンに別の FarmBeats デプロイを作成します。その後、次の手順に従って、回復されたデータ ストア (つまり、Azure Storage と Cosmos DB) のデータを使用するように新しいデプロイを構成します。

1. [Cosmos DB を構成する](#configure-cosmos-db)
2. [ストレージ アカウントを構成する](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB を構成する

回復された Cosmos DB のアクセスキーをコピーし、新しい FarmBeats Datahub Key Vault を更新します。


  ![ディザスター リカバリー](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 回復された Cosmos DB の URL をコピーし、新しい FarmBeats Datahub App Service 構成で更新します。 これで、新しい FarmBeats デプロイで Cosmos DB アカウントを削除することができます。

  ![ディザスター リカバリー](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>ストレージ アカウントを構成する

回復されたストレージ アカウントのアクセスキーをコピーし、新しい FarmBeats Datahub Key Vault 内で更新します。

![ディザスター リカバリー](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 新しい FarmBeats Batch VM 構成ファイルでストレージ アカウント名を確実に更新します。

![ディザスター リカバリー](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同様に、アクセラレータ ストレージ アカウントのデータ回復を有効にした場合は、手順 2 に従って、新しい FarmBeats インスタンスでアクセラレータ ストレージ アカウントのアクセス キーと名前を更新します。
