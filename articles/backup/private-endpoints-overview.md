---
title: プライベート エンドポイントの概要
description: Azure Backup のプライベート エンドポイントの使用について、およびプライベート エンドポイントを使用することでリソースのセキュリティを維持しやすくなるシナリオについて理解を深めます。
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c9ff432c1de5ebdb44ae8f5fa0d4889026a32252
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136487"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイントの概要と概念

Azure Backup で[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して、Recovery Services コンテナーから安全にデータをバックアップおよび復元できます。 プライベート エンドポイントでは、ご使用の Azure 仮想ネットワーク (VNet) の 1 つ以上のプライベート IP アドレスが使用され、サービスが実質的に VNet に取り込まれます。

この記事は、Azure Backup のプライベート エンドポイントのしくみと、プライベート エンドポイントを使用することでリソースのセキュリティを維持しやすくなるシナリオについて理解するのに役立ちます。

## <a name="before-you-start"></a>開始する前に

- プライベート エンドポイントは、新しい Recovery Services コンテナー (そのコンテナーに登録されている項目がない) に対してのみ作成できます。 そのため、コンテナーに項目を保護する前に、プライベート エンドポイントを作成する必要があります。
- 1 つの仮想ネットワークに複数の Recovery Services コンテナーのプライベート エンドポイントを含めることができます。 また、1 つの Recovery Services コンテナーに対して複数の仮想ネットワークにプライベート エンドポイントを含めることができます。 ただし、1 つのコンテナーに対して作成できるプライベート エンドポイントの最大数は 12 です。
- コンテナーに対してプライベート エンドポイントが作成されると、コンテナーはロックダウンされます。 そのコンテナーのプライベート エンドポイントを含むネットワーク以外のネットワークからは、そのコンテナーに (バックアップと復元のために) アクセスできません。 コンテナーのすべてのプライベート エンドポイントが削除されると、そのコンテナーはすべてのネットワークからアクセスできるようになります。
- Backup 用のプライベート エンドポイント接続には、ストレージ用に Azure Backup で使用されるものなど、サブネットで合計 11 個のプライベート IP が使用されます。 一部の Azure リージョンでは、この数がさらに多い場合があります (最大 25)。 そのため、Backup 用のプライベート エンドポイントを作成する場合は、十分な数のプライベート IP を用意することをお勧めします。
- Recovery Services コンテナーは Azure Backup と Azure Site Recovery (の両方) に使用されますが、この記事では、Azure Backup だけのためにプライベート エンドポイントを使用する場合について説明します。
- Backup のプライベート エンドポイントには、Azure Active Directory (Azure AD) へのアクセスが含まれていないため、同じことを個別に確認する必要があります。 したがって、Azure AD がリージョンで機能するために必要な IP と FQDN には、Azure VM でのデータベースのバックアップ、および MARS エージェントを使用したバックアップを実行するときに、セキュリティで保護されたネットワークからの送信アクセスが許可される必要があります。 また、必要に応じて、NSG タグと Azure Firewall タグを使用して、Azure AD へのアクセスを許可することもできます。
- ネットワーク ポリシーが適用されている仮想ネットワークは、プライベート エンドポイント用にサポートされません。 続行する前に、[ネットワーク ポリシーを無効にする](../private-link/disable-private-endpoint-network-policy.md)必要があります。
- Recovery Services リソース プロバイダーをサブスクリプションに 2020 年 5 月 1 日より前に登録した場合は、再登録する必要があります。 プロバイダーを再登録するには、Azure portal のサブスクリプションに移動し、左側のナビゲーションバーで **[リソース プロバイダー]** に移動し、 **[Microsoft.RecoveryServices]** を選択し、 **[再登録]** を選択します。
- コンテナーでプライベート エンドポイントが有効になっている場合、SQL および SAP HANA データベース バックアップの[リージョンをまたがる復元](backup-create-rs-vault.md#set-cross-region-restore)はサポートされていません。
- 既にプライベート エンドポイントを使用している Recovery Services コンテナーを新しいテナントに移動する場合、Recovery Services コンテナーを更新して、コンテナーのマネージド ID を再作成および再構成し、新しいプライベート エンドポイント (新しいテナント内にあるはずです) を作成する必要があります。 これを実行しないと、バックアップおよび復元操作が失敗するようになります。 また、サブスクリプション内に設定されているすべての Azure ロールベースのアクセス制御 (Azure RBAC) アクセス許可も再構成する必要があります。

## <a name="recommended-and-supported-scenarios"></a>推奨されるシナリオとサポートされるシナリオ

プライベート エンドポイントは、コンテナーに対して有効になっている間、Azure VM と MARS エージェントのバックアップにおいてのみ、SQL および SAP HANA のワークロードのバックアップと復元に使用されます。 コンテナーは、他のワークロードのバックアップにも使用できます (ただし、プライベート エンドポイントは必要ありません)。 SQL および SAP HANA のワークロードのバックアップと、MARS エージェントを使用したバックアップに加えて、プライベート エンドポイントを使用して Azure VM バックアップ用のファイルの復旧を実行することもできます。 詳細については、後の表を参照してください。

| シナリオ | Recommendations |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure VM でのワークロードのバックアップ (SQL、SAP HANA)、MARS エージェントを使用したバックアップ | プライベート エンドポイントの使用をお勧めします。これにより、お使いの仮想ネットワークから、Azure Backup または Azure Storage の IP と FQDN を許可リストに追加する必要なくバックアップと復元ができます。 このシナリオでは、SQL データベースをホストする VM から Azure AD の IP または FQDN に接続できることを確認します。 |
| **Azure VM バックアップ**                                         | VM バックアップでは、どの IP または FQDN へのアクセスも許可する必要はありません。 そのため、ディスクのバックアップと復元のためにプライベート エンドポイントは必要ありません。  <br><br>   ただし、プライベート エンドポイントを含むコンテナーからのファイルの復旧は、コンテナーのプライベート エンドポイントを含む仮想ネットワークに制限されます。 <br><br>    ACL に記載されたアンマネージド ディスクを使用する場合は、ディスクが格納されているストレージ アカウントで、**信頼された Microsoft サービス** へのアクセスが許可されていることを確認してください。 |
| **Azure Files バックアップ**                                      | Azure Files バックアップは、ローカル ストレージ アカウントに格納されます。 そのため、バックアップと復元にプライベート エンドポイントは必要ありません。 |

>[!Note]
>プライベート エンドポイントは、DPM および MABS サーバーではサポートされていません。 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>プライベート エンドポイントによるネットワーク接続の違い

前述のとおり、プライベート エンドポイントは、Azure VM でのワークロード (SQL、SAP HANA) のバックアップおよび MARS エージェントによるバックアップに特に役立ちます。 すべてのシナリオ (プライベート エンドポイントの有無にかかわらず) では、ワークロード拡張機能 (Azure VM 内で実行されている SQL および SAP HANA インスタンスのバックアップ用) と MARS エージェントの両方で、AAD ([Microsoft 365 Common および Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) のセクション 56 および 59 で言及されている FQDN) への接続呼び出しが行われます。

ワークロード拡張機能または MARS エージェントがプライベート エンドポイントの "_ない_" Recovery Services コンテナーにインストールされている場合のこれらの接続に加えて、次のドメインへの接続も必要です。

| サービス | ドメイン名 |
| ------- | ------------ |
| Azure Backup  | *.backup.windowsazure.com |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

ワークロード拡張機能または MARS エージェントがプライベート エンドポイントを備えた Recovery Services コンテナーにインストールされている場合、次のエンドポイントにヒットします。

| サービス | ドメイン名 |
| ------- | ------------ |
| Azure Backup  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>上のテキストで、`<geo>` はリージョン コードを示します (たとえば、米国東部の場合は **eus**、北ヨーロッパの場合は **ne** です)。 リージョン コードについては、次の一覧を参照してください。
>- [すべてのパブリック クラウド](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [ドイツ](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov ](../azure-government/documentation-government-developer-guide.md)

どちらのシナリオでも、ヒットされるストレージの FQDN は同じです。 ただし、プライベート エンドポイントが設定された Recovery Services コンテナーの場合、これらの名前解決では、プライベート IP アドレスを返す必要があります。 これは、プライベート DNS ゾーンを使用する、ホスト ファイルにストレージ アカウントの DNS エントリを作成する、またはそれぞれの DNS エントリでカスタム DNS への条件付きフォワーダーを使用することで実現できます。 ストレージ アカウントのプライベート IP マッピングは、portal のストレージ アカウントのプライベート エンドポイント ブレードに一覧表示されます。

>BLOB とキューのプライベート エンドポイントは標準の名前付けパターンに従い、 **\<the name of the private endpoint>_ecs** または **\<the name of the private endpoint>_prot** で始まり、末尾にそれぞれ  **\_blob** および  **\_queue**  が付きます。

Azure Backup サービスのエンドポイントは、プライベート エンドポイント対応コンテナー向けに変更されます。  
サードパーティのプロキシ サーバーとファイアウォールを使用して DNS プロキシ サーバーを構成した場合、上記のドメイン名を許可して、これらのプライベート IP アドレス マッピングを使用するプライベート DNS ゾーンへの仮想ネットワーク リンクを使用して、カスタム DNS (プライベート IP アドレス マッピングを使用) または 168.63.129.16 にリダイレクトする必要があります。

次の例は、Recovery Services コンテナー、BLOB、キュー、AAD のドメイン名クエリを 168.63.129.16 にリダイレクトするために DNS プロキシとして使用される Azure ファイアウォールを示しています。

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="ドメイン名クエリをリダイレクトするために DNS プロキシとして Azure ファイアウォールを使用する方法を示す図。" lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

詳細については、[プライベート エンドポイントの作成と使用](private-endpoints.md)に関するページを参照してください。

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>プライベート エンドポイントが設定されたコンテナーのネットワーク接続の設定

回復サービスのプライベート エンドポイントは、プライベート IP を持つネットワーク インターフェイス (NIC) に関連付けられます。 プライベート エンドポイント接続が機能する (Azure バックボーンを介してサービスへのすべてのトラフィックをルーティングし、VNet 内のクライアントへのサービス アクセスを制限する) ためには、サービスのすべての通信トラフィックをそのネットワーク インターフェイスにリダイレクトする必要があります。 これは、拡張機能およびエージェントが実行されているマシン上の VNet またはホスト ファイル エントリにリンクされた DNS を使用することで実現できます。

ワークロード バックアップ拡張機能と MARS エージェントは、VNet 内の Azure VM または VNet とピアリングされたオンプレミス VM で実行されます。 プライベート エンドポイントがこの VNet に参加している Recovery Services コンテナーに登録すると、拡張機能とエージェントの Azure Backup クラウド サービスのサービス URL は `<azure_backup_svc >.<geo>.backup.windowsazure.com` から `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com** に変更されます。

>[!Note]
>上のテキストで、`<geo>` はリージョン コードを示します (たとえば、米国東部の場合は **eus**、北ヨーロッパの場合は **ne** です)。 リージョン コードについては、次の一覧を参照してください。
>- [すべてのパブリック クラウド](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [ドイツ](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov ](../azure-government/documentation-government-developer-guide.md)

変更された URL は、コンテナーに固有です。  URL 名の `<vault_id>` を参照してください。 このコンテナーに登録されている拡張機能とエージェントのみが、これらのエンドポイントを介して Azure Backup と通信できます。 これにより、この VNet 内のクライアントへのアクセスが制限されます。 拡張機能とエージェントは `*.privatelink.<geo>.backup.windowsazure.com` を介して通信し、これは、NIC 内の対応するプライベート IP を解決する必要があります。

Azure portal で **[プライベート DNS ゾーンと統合する]** オプションを使用して、Recovery Services コンテナーのプライベート エンドポイントを作成すると、リソースが割り当てられるたびに、Azure Backup サービス (`*.privatelink.<geo>backup.windowsazure.com`) のプライベート IP アドレスに必要な DNS エントリが自動的に作成されます。 それ以外の場合は、カスタム DNS またはホスト ファイルでこれらの FQDN の DNS エントリを手動で作成する必要があります。

通信チャネルの VM 検出後に BLOB またはキューの DNS レコードを手動で管理する方法については、「[最初の登録後の BLOB とキューの DNS レコード (カスタム DNS サーバーとホスト ファイルの場合のみ)](./private-endpoints.md#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration)」を参照してください。 最初のバックアップ後にバックアップ ストレージ アカウント BLOB の DNS レコードを手動で管理する方法については、「[最初のバックアップ後の BLOB の DNS レコード (カスタム DNS サーバーとホスト ファイルの場合のみ)](./private-endpoints.md#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)」を参照してください。

>FQDN のプライベート IP アドレスは、Recovery Services コンテナー用に作成されたプライベート エンドポイントのプライベート エンドポイント ブレードにあります。

次の図は、プライベート DNS ゾーンを使用して、これらの変更されたサービス FQDN を解決する場合の解決のしくみを示しています。 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="プライベート DNS ゾーンを使用して、変更されたサービス FQDN を解決する場合の解決のしくみを示す図。" lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

ワークロード拡張機能とエージェントには、Azure Backup クラウド サービスへの接続に加えて、Azure ストレージ アカウントと Azure Active Directory への接続も必要です。 Azure VM で実行されているワークロード拡張機能には、少なくとも 2 つのストレージ アカウントへの接続が必要です。1 つ目は通信チャネルとして使用され (キュー メッセージを使用)、2 つ目はバックアップ データを格納するために使用されます。 MARS エージェントは、バックアップ データの格納に使用される 1 つのストレージ アカウントにアクセスできる必要があります。

プライベート エンドポイントが有効になっているコンテナーの場合、Azure Backup により、これらのストレージ アカウントのプライベート エンドポイントが作成されます。これは、Azure バックボーン ネットワークを介して通信チャネルとバックアップ データのトラフィックをルーティングします。 これにより、Azure Backup に関連するすべてのネットワーク トラフィックが仮想ネットワークから離れるのを防ぐことができます。

前提条件として、Recovery Services コンテナーには、同じリソース グループに追加のプライベート エンドポイントを作成するためのアクセス許可が必要です。 また、Recovery Services コンテナーにプライベート DNS ゾーン (privatelink.blob.core.windows.net、privatelink.queue.core.windows.net) に DNS エントリを作成するためのアクセス許可を与えることをお勧めします。 Recovery Services コンテナーにより、VNet とプライベート エンドポイントが作成されているリソース グループでプライベート DNS ゾーンがを検索されます。 これらのゾーンに DNS エントリを追加するためのアクセス許可がある場合は、コンテナーによって作成されます。それ以外の場合は、ユーザーが、カスタム DNS 内、または VNet にリンクされたプライベート DNS ゾーン内に手動で作成する必要があります。

>プライベート IP マッピングは、portal の BLOB およびキューのプライベート エンドポイント ブレードで使用できます。

次の図は、プライベート DNS ゾーンを使用したストレージ アカウントの名前解決のしくみを示しています。

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="プライベート DNS ゾーンを使用したストレージ アカウントの名前解決のしくみを示す図。" lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>次の手順

- [プライベート エンドポイントを作成して使用する](private-endpoints.md)。