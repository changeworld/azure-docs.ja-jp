---
title: Azure Backup のプライベート エンドポイントの作成と使用
description: Azure Backup のプライベート エンドポイントを作成するプロセスについて説明します。プライベート エンドポイントを使用することで、リソースのセキュリティが維持しやすくなります。
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: e474ae10e1461e834312e4ff52b36ece57bcc30e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137925"
---
# <a name="create-and-use-private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイントの作成と使用

この記事では、[Azure Backup のプライベート エンドポイント](private-endpoints-overview.md)を作成するプロセスと、プライベート エンドポイントによってリソースのセキュリティが維持しやすくなるシナリオに関する情報を提供します。

## <a name="before-you-start"></a>開始する前に

プライベート エンドポイントの作成に進む前に、[前提条件](private-endpoints-overview.md#before-you-start)と[サポートされるシナリオ](private-endpoints-overview.md#recommended-and-supported-scenarios)を確認してください。

これらの詳細は、コンテナーのプライベート エンドポイントを作成する前に満たす必要がある制限事項と条件を理解するのに役立ちます。

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Backup 用のプライベート エンドポイントの作成の概要

このセクションでは、ご自身の仮想ネットワーク内に Azure Backup 用のプライベート エンドポイントを作成して使用するステップを説明します。

>[!IMPORTANT]
> このドキュメントに示されているのと同じ順序で手順に従うことを強くお勧めします。 そうしないと、コンテナーにプライベート エンドポイントを使用するための互換性がなくなり、新しいコンテナーを使ってプロセスをやり直すことが必要になる可能性があります。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Backup 用のプライベート エンドポイントは、項目が保護されていない (または、以前に保護または登録を試みた項目がない) Recovery Services コンテナーに対してのみ作成できます。 開始するには、新しいコンテナーを作成することをお勧めします。 新しいコンテナーの作成の詳細については、「[Recovery Services コンテナーを作成して構成する](backup-create-rs-vault.md)」を参照してください。

Azure Resource Manager クライアントを使用してコンテナーを作成する方法については、[こちらのセクション](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)を参照してください。 これにより、マネージド ID が既に有効になっているコンテナーが作成されます。

## <a name="enable-managed-identity-for-your-vault"></a>コンテナーのマネージド ID を有効にする

マネージド ID を使用すると、コンテナーでプライベート エンドポイントを作成して使用できます。 このセクションでは、ご自身のコンテナーのマネージド ID を有効にする方法について説明します。

1. ご自身の Recovery Services コンテナー > **[ID]** に移動します。

    ![ID の状態をオンに変更する](./media/private-endpoints/identity-status-on.png)

1. **[状態]** を **[On]** に変更し、 **[保存]** を選択します。

1. **オブジェクト ID** が生成されます。これがコンテナーのマネージド ID です。

    >[!NOTE]
    >マネージド ID は、いったん有効にしたら (一時的にであっても) 無効に **しないでください**。 マネージド ID を無効にすると、動作に一貫性がなくなる可能性があります。

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>必要なプライベート エンドポイントを作成するためのアクセス許可をコンテナーに付与する

Azure Backup に必要なプライベート エンドポイントを作成するには、コンテナー (コンテナーのマネージド ID) に次のリソース グループへのアクセス許可が必要です。

- 対象の VNet が含まれているリソース グループ
- プライベート エンドポイントが作成されるリソース グループ
- プライベート DNS ゾーンが含まれているリソース グループ (詳細については、[こちら](#create-private-endpoints-for-azure-backup)で説明しています)

これら 3 つのリソース グループの **共同作成者** ロールをコンテナー (マネージド ID) に付与することをお勧めします。 次の手順では、特定のリソース グループに対してこれを実行する方法について説明します (これは、3 つのリソース グループそれぞれに対して実行する必要があります)。

1. リソース グループに移動し、左側のバーの **[アクセス制御 (IAM)]** に移動します。
1. **[アクセス制御]** に移動したら、 **[ロールの割り当てを追加する]** に進みます。

    ![ロールの割り当てを追加する](./media/private-endpoints/add-role-assignment.png)

1. **[ロールの割り当ての追加]** ウィンドウで、 **[ロール]** として **[共同作成者]** を選択し、コンテナーの **名前** を **[プリンシパル]** として使用します。 コンテナーを選択し、完了したら **[保存]** を選択します。

    ![ロールとプリンシパルの選択](./media/private-endpoints/choose-role-and-principal.png)

より詳細なレベルでアクセス許可を管理するには、「[ロールとアクセス許可を手動で作成する](#create-roles-and-permissions-manually)」を参照してください。

## <a name="create-private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイントを作成する

このセクションでは、コンテナーのプライベート エンドポイントを作成する方法について説明します。

1. 上記で作成したコンテナーに移動し、左側のナビゲーション バーの **[プライベート エンドポイント接続]** に移動します。 上部にある **[+ プライベート エンドポイント]** を選択して、このコンテナーの新しいプライベート エンドポイントの作成を開始します。

    ![新しいプライベート エンドポイントを作成する](./media/private-endpoints/new-private-endpoint.png)

1. **[プライベート エンドポイントの作成]** のプロセスに入ったら、プライベート エンドポイント接続を作成するための詳細を指定する必要があります。
  
    1. **[基本]** :プライベート エンドポイントの基本的な詳細を入力します。 リージョンは、バックアップするコンテナーおよびリソースと同じである必要があります。

        ![基本的な詳細を入力する](./media/private-endpoints/basics-tab.png)

    1. **リソース**:このタブでは、接続を作成する対象の PaaS リソースを選択する必要があります。 目的のサブスクリプションのリソースの種類から、 **[Microsoft.RecoveryServices/vaults]** を選択します。 完了したら、 **[リソース]** として Recovery Services コンテナーの名前を選択し、**AzureBackup** を **[対象サブリソース]** としてします。

        ![接続に使用するリソースを選択する](./media/private-endpoints/resource-tab.png)

    1. **構成**:[構成] では、プライベート エンドポイントを作成する仮想ネットワークとサブネットを指定します。 これは、VM が存在する Vnet です。

        プライベートに接続するには、目的の DNS レコードが必要です。 ネットワークの設定に応じて、以下のいずれかを選択することができます。

          - プライベート エンドポイントをプライベート DNS ゾーンを統合する: 統合する場合は、 **[はい]** を選択します。
          - カスタム DNS サーバーを使用する: ご自分の DNS サーバーを使用する場合は、 **[いいえ]** を選択します。

        これらの両方の DNS レコードの管理については、[後で説明します](#manage-dns-records)。

          ![仮想ネットワークとサブネットを指定する](./media/private-endpoints/configuration-tab.png)

    1. 必要に応じて、プライベート エンドポイントの **タグ** を追加できます。
    1. 詳細の入力が完了したら、 **[確認および作成]** に進みます。 検証が完了したら、 **[作成]** を選択してプライベート エンドポイントを作成します。

## <a name="approve-private-endpoints"></a>プライベート エンドポイントを承認する

プライベート エンドポイントを作成しているユーザーが Recovery Services コンテナーの所有者でもある場合、上記で作成したプライベート エンドポイントは自動承認されます。 それ以外の場合、プライベート エンドポイントが使用できるようになるには、その前にコンテナーの所有者によって承認される必要があります。 このセクションでは、Azure portal を使用してプライベート エンドポイントを手動で承認する方法について説明します。

Azure Resource Manager クライアントを使用してプライベート エンドポイントを承認する方法については、「[Azure Resource Manager クライアントを使用したプライベート エンドポイントの手動による承認](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)」を参照してください。

1. Recovery Services コンテナーで、左側のバーの **[プライベート エンドポイント接続]** に移動します。
1. 承認するプライベート エンドポイントを選択します。
1. 上部バーにある **[承認]** を選択します。 エンドポイント接続を拒否または削除する場合は、 **[拒否]** または **[削除]** を選択することもできます。

    ![プライベート エンドポイントを承認する](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>DNS レコードの管理

前述のように、プライベートに接続するには、プライベート DNS ゾーンまたはサーバーに目的の DNS レコードが必要です。 プライベート エンドポイントを Azure プライベート DNS ゾーンと直接統合することも、カスタム DNS サーバーを使用してネットワークの設定に基づいてこれを実現することもできます。 これは、次の 3 つのサービスすべてで行う必要があります: Backup、BLOB、キュー。

また、DNS ゾーンまたはサーバーが、プライベート エンドポイントを含むものとは異なるサブスクリプションに存在する場合は、「[DNS サーバーまたは DNS ゾーンが別のサブスクリプションに存在する場合に DNS エントリを作成する](#create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription)」も参照してください。 

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>プライベート エンドポイントを Azure プライベート DNS ゾーンと統合する場合

プライベート エンドポイントとプライベート DNS ゾーンを統合することを選択した場合は、必要な DNS レコードがバックアップによって追加されます。 使用されているプライベート DNS ゾーンは、プライベート エンドポイントの **[DNS 構成]** で表示できます。 これらの DNS ゾーンが存在しない場合は、プライベート エンドポイントの作成時に自動的に作成されます。 ただし、次に示すように、仮想ネットワーク (バックアップ対象のリソースを含む) が 3 つすべてのプライベート DNS ゾーンと適切にリンクされていることを確認する必要があります。

![Azure プライベート DNS ゾーンの DNS 構成](./media/private-endpoints/dns-configuration.png)

>[!Note]
>プロキシ サーバーを使用している場合、プロキシ サーバーをバイパスするか、プロキシ サーバー経由でバックアップを実行できます。 プロキシ サーバーのバイパス方法については、次のセクションに進んでください。 プロキシ サーバーを使用してバックアップする方法については、[Recovery Services コンテナーのプロキシ サーバー セットアップの詳細](#set-up-proxy-server-for-recovery-services-vault-with-private-endpoint)に関するページを参照してください。
#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>プライベート DNS ゾーン内の仮想ネットワーク リンクを検証する

上記の **それぞれのプライベート DNS** ゾーン (Backup、BLOB、キュー) に対して、次の手順を行います。

1. 左側のナビゲーション バーで、それぞれの **[仮想ネットワーク リンク]** オプションに移動します。
1. 次に示すように、プライベート エンドポイントを作成した仮想ネットワークのエントリを確認できます。

    ![プライベート エンドポイントの仮想ネットワーク](./media/private-endpoints/virtual-network-links.png)

1. エントリが表示されない場合は、仮想ネットワークのリンクを、それを持っていないすべての DNS ゾーンに追加します。

    ![仮想ネットワークリンクの追加](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>カスタム DNS サーバーまたはホスト ファイルを使用する場合

カスタム DNS サーバーを使用している場合は、必要な DNS ゾーンを作成し、プライベート エンドポイントに必要な DNS レコードを DNS サーバーに追加する必要があります。 BLOB とキューの場合は、条件付きフォワーダーを使用することもできます。

#### <a name="for-the-backup-service"></a>Backup サービスの場合

1. DNS サーバーで、次の名前付け規則に従って、Backup 用の DNS ゾーンを作成します。

    |ゾーン |サービス |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  バックアップ        |

    >[!NOTE]
    > 上のテキストで、`<geo>` はリージョン コードを示します (たとえば、米国東部と北ヨーロッパはそれぞれ *eus* と *ne*)。 リージョン コードについては、次の一覧を参照してください。
    >
    > - [すべてのパブリック クラウド](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [ドイツ](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov ](../azure-government/documentation-government-developer-guide.md)
    > - [geo コード リスト - サンプル XML](scripts/geo-code-list.md)

1. 次に、必要な DNS レコードを追加する必要があります。 Backup DNS ゾーンに追加する必要があるレコードを表示するには、上で作成したプライベート エンドポイントに移動し、左側のナビゲーション バーの下にある **[DNS 構成]** オプションに移動します。

    ![カスタム DNS サーバーの DNS 構成](./media/private-endpoints/custom-dns-configuration.png)

1. Backup 用の DNS ゾーンに A タイプのレコードとして表示される FQDN と IP ごとに 1 つのエントリを追加します。 名前解決にホスト ファイルを使用している場合は、次の形式に従って、各 IP および FQDN のホスト ファイルで対応するエントリを作成します。

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>上のスクリーンショットに示されているように、FQDN は `xxxxxxxx.<geo>.backup.windowsazure.com` を表します。`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ではありません。 このような場合は、記載されている形式に従って、`.privatelink.` を含める (必要な場合には追加する) 必要があります。

#### <a name="for-blob-and-queue-services"></a>BLOB および Queue サービスの場合

BLOB とキューの場合は、条件付きフォワーダーを使用するか、DNS サーバーで DNS ゾーンを作成することができます。

##### <a name="if-using-conditional-forwarders"></a>条件付きフォワーダーを使用している場合

条件付きフォワーダーを使用している場合は、次のように、BLOB とキューの FQDN のフォワーダーを追加します。

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>プライベート DNS ゾーンを使用している場合

BLOB とキューに DNS ゾーンを使用している場合は、最初にこれらの DNS ゾーンを作成してから、必要な A レコードを追加する必要があります。

|ゾーン |サービス  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  BLOB     |
|`privatelink.queue.core.windows.net`     | キュー        |

現時点では、カスタム DNS サーバーを使用する場合にのみ、BLOB とキューのゾーンを作成します。 DNS レコードの追加は、次の 2 つのステップで、後から行います。

1. 最初のバックアップ インスタンスを登録するとき、つまり、初めてバックアップを構成するとき
1. 最初のバックアップを実行するとき

これらのステップは、以下のセクションで実行します。

## <a name="use-private-endpoints-for-backup"></a>Backup 用のプライベート エンドポイントを使用する

ご自身の VNet 内のコンテナー用に作成したプライベート エンドポイントが承認されたら、バックアップと復元を実行するためにそれらの使用を開始できます。

>[!IMPORTANT]
>続行する前に、ドキュメントに記載された前述のすべての手順を確実に完了してください。 まとめると、次のチェックリストの手順を完了している必要があります。
>
>1. (新しい) Recovery Services コンテナーを作成しました
>2. システム割り当てマネージド ID をコンテナーで使用できるようにしました
>3. コンテナーのマネージド ID に、関連するアクセス許可を割り当てました
>4. コンテナーのプライベート エンドポイントを作成しました
>5. プライベート エンドポイントを承認しました (自動承認されない場合)
>6. すべての DNS レコードが適切に追加されていることを確認します (以下のセクションで説明するカスタム サーバーの BLOB とキューのレコードは除く)

### <a name="check-vm-connectivity"></a>VM の接続を確認する

ロックダウンされたネットワーク内の VM で、次のことを確認します。

1. VM が AAD にアクセスできる。
2. VM からバックアップ URL (`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`) で **nslookup** を実行して、接続を確保する。 これにより、仮想ネットワークに割り当てられたプライベート IP が返されます。

### <a name="configure-backup"></a>バックアップの構成

上記のチェックリストを確認し、アクセスが正常に完了したら、コンテナーへのワークロードのバックアップを引き続き構成できます。 カスタム DNS サーバーを使用している場合は、最初のバックアップを構成した後で使用可能な BLOB およびキューの DNS エントリを追加する必要があります。

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>最初の登録後の BLOB とキューの DNS レコード (カスタム DNS サーバーとホスト ファイルの場合のみ)

プライベート エンドポイントが有効になっているコンテナーに少なくとも 1 つのリソースのバックアップを構成した後、次に示すように、BLOB とキューに必要な DNS レコードを追加します。

1. リソース グループに移動し、作成したプライベート エンドポイントを検索します。
1. 自分が指定したプライベート エンドポイント名以外に、2 つのプライベート エンドポイントが作成されていることがわかります。 これらは、`<the name of the private endpoint>_ecs` で始まり、それぞれに `_blob` と `_queue` のサフィックスが付けられています。

    ![プライベート エンドポイントのリソース](./media/private-endpoints/private-endpoint-resources.png)

1. これらのプライベート エンドポイントに移動します。 2 つのプライベート エンドポイントそれぞれの DNS 構成オプションで、FQDN および IP アドレスを持つレコードが表示されます。 前に説明したものに加えて、これらの両方をカスタム DNS サーバーに追加します。
ホスト ファイルを使用している場合は、次の形式に従って、各 IP および FQDN のホスト ファイルで対応するエントリを作成します。

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![BLOB の DNS 構成](./media/private-endpoints/blob-dns-configuration.png)

上記に加えて、最初のバックアップの後に必要なもう 1 つのエントリがあります。これについては[後で](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)説明します。

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Azure VM でのワークロードのバックアップと復元 (SQL と SAP HANA)

プライベート エンドポイントが作成され、承認されたら、クライアント側でプライベート エンドポイントを使用するために必要な変更はありません (SQL 可用性グループを使用している場合については、このセクションの後半で説明します)。 セキュリティで保護されたネットワークからコンテナーへのすべての通信とデータ転送は、プライベート エンドポイントを介して実行されます。 ただし、サーバー (SQL または SAP HANA) がボールトに登録された後にそのプライベート エンドポイントを削除した場合は、コンテナーをボールトに再登録する必要があります。 それらに対する保護を停止する必要はありません。

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>最初のバックアップ後の BLOB の DNS レコード (カスタム DNS サーバーとホスト ファイルの場合のみ)

最初のバックアップを実行した後に (条件付きの転送を行わずに) カスタム DNS サーバーを使用していると、バックアップが失敗するおそれがあります。 その場合は、以下の手順を行ってください。

1. リソース グループに移動し、作成したプライベート エンドポイントを検索します。
1. 前に説明した 3 つのプライベート エンドポイントとは別に、`<the name of the private endpoint>_prot` から始まる名前で、`_blob` のサフィックスが付いている、4 番目のプライベート エンドポイントが表示されます。

    ![サフィックス "prot" を持つプライベート エンドポイント](./media/private-endpoints/private-endpoint-prot.png)

1. この新しいプライベート エンドポイントに移動します。 DNS 構成オプションには、FQDN と IP アドレスを含むレコードが表示されます。 前に説明したものに加えて、これらをプライベート DNS サーバーに追加します。

    ホスト ファイルを使用している場合は、次の形式に従って、各 IP および FQDN のホスト ファイルで対応するエントリを作成します。

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>この時点で、VM から **nslookup** を実行して、コンテナーのバックアップとストレージの URL で実行したときにプライベート IP アドレスに解決できるようになります。

### <a name="when-using-sql-availability-groups"></a>SQL 可用性グループを使用する場合

SQL 可用性グループ (AG) を使用する場合は、次に示すように、カスタム AG DNS で条件付き転送をプロビジョニングする必要があります。

1. ドメイン コントローラーにサインインします。
1. DNS アプリケーションの下で、必要に応じて、3 つすべての DNS ゾーン (Backup、BLOB、キュー) の条件付きフォワーダーをホスト IP 168.63.129.16 またはカスタム DNS サーバーの IP アドレスに追加します。 次のスクリーンショットは、Azure ホスト IP に転送する場合を示しています。 独自の DNS サーバーを使用している場合は、お使いの DNS サーバーの IP に置き換えます。

    ![DNS マネージャーの条件付きフォワーダー](./media/private-endpoints/dns-manager.png)

    ![新しい条件付きフォワーダー](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>MARS エージェントを使用したバックアップと復元

MARS エージェントを使用してオンプレミスのリソースをバックアップする場合は、オンプレミスのネットワーク (バックアップ対象のリソースが含まれている) が Azure VNet (コンテナーのプライベート エンドポイントが含まれている) と確実にピアリングされているようにしてください。これにより、コンテナーを使用できるようになります。 その後、MARS エージェントをインストールして、ここで詳しく説明しているように、バックアップを構成できます。 ただし、バックアップ用のすべての通信が、ピアリングされたネットワークのみを介して行われるようにする必要があります。

MARS エージェントの登録後にそのボールト用のプライベート エンドポイントを削除した場合は、コンテナーをボールトに再登録する必要があります。 それらに対する保護を停止する必要はありません。

## <a name="deleting-private-endpoints"></a>プライベート エンドポイントを削除する

プライベート エンドポイントを削除する方法については、[こちらのセクション](/rest/api/virtualnetwork/privateendpoints/delete)を参照してください。

## <a name="additional-topics"></a>関連トピック

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを作成する

Azure Resource Manager クライアントを使用して、Recovery Services コンテナーを作成し、そのマネージド ID を有効にすることができます (後で説明するように、マネージド ID の有効化は必ず行う必要があります)。 これを行うサンプルを次に示します。

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

上記の JSON ファイルには、次の内容が含まれています。

要求 JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

応答 JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>この例で Azure Resource Manager クライアントを使用して作成されたコンテナーは、システム割り当てマネージド ID を使用して既に作成されています。

### <a name="managing-permissions-on-resource-groups"></a>リソース グループに対するアクセス許可の管理

コンテナーのマネージド ID には、プライベート エンドポイントが作成されるリソース グループと仮想ネットワークにおいて、次のアクセス許可が必要です。

- `Microsoft.Network/privateEndpoints/*` これは、リソース グループ内のプライベート エンドポイントで CRUD を実行するために必要です。 リソース グループに割り当てられている必要があります。
- `Microsoft.Network/virtualNetworks/subnets/join/action` これは、プライベート IP がプライベート エンドポイントに接続される仮想ネットワークに対して必要です。
- `Microsoft.Network/networkInterfaces/read` これは、プライベート エンドポイント用に作成されたネットワーク インターフェイスを取得するために、リソース グループに対して必要です。
- プライベート DNS ゾーン共同作成者ロール: このロールは既に存在し、`Microsoft.Network/privateDnsZones/A/*` および `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` のアクセス許可を付与するために使用できます。

次のいずれかの方法を使用して、必要なアクセス許可を持つロールを作成できます。

#### <a name="create-roles-and-permissions-manually"></a>ロールとアクセス許可を手動で作成する

次の JSON ファイルを作成し、セクションの最後にある PowerShell コマンドを使用してロールを作成します。

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>スクリプトを使用する

1. Azure portal で **Cloud Shell** を開始し、PowerShell ウィンドウで **[ファイルのアップロード]** を選択します。

    ![PowerShell ウィンドウで [ファイルのアップロード] を選択する](./media/private-endpoints/upload-file-in-powershell.png)

1. 次のスクリプトをアップロードします。[VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. ホーム フォルダー (例: `cd /home/user`) に移動します。

1. 次のスクリプトを実行します。

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    パラメーターは次のとおりです。

    - **subscription**: コンテナーのプライベート エンドポイントが作成されるリソース グループと、コンテナーのプライベート エンドポイントが接続されるサブネットを持つ SubscriptionId

    - **vaultPEResourceGroup**: コンテナーのプライベート エンドポイントが作成されるリソース グループ

    - **vaultPESubnetResourceGroup**: プライベート エンドポイントの参加先となるサブネットのリソース グループ

    - **vaultMsiName**: コンテナーの MSI の名前 (**VaultName** と同じ)

1. 認証を完了すると、スクリプトによって上記で指定されたサブスクリプションのコンテキストが取得されます。 テナントに存在しない場合は適切なロールが作成され、コンテナーの MSI にロールが割り当てられます。

### <a name="creating-private-endpoints-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する

#### <a name="auto-approved-private-endpoints"></a>プライベート エンドポイントの自動承認

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用したプライベート エンドポイントの手動による承認

1. **GetVault** を使用して、ご自身のプライベート エンドポイントのプライベート エンドポイント接続 ID を取得します。

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    これにより、プライベート エンドポイント接続 ID が返されます。 接続 ID の最初の部分を次のように使用して、接続の名前を取得できます。

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 次のサンプルに示すように、**プライベート エンドポイント接続 ID** (および必要に応じて **プライベート エンドポイント名**) を応答から取得し、次の JSON および Azure Resource Manager URI 内でそれを置き換えて、状態を "Approved、Rejected、Disconnected" のいずれかに変更します。

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="set-up-proxy-server-for-recovery-services-vault-with-private-endpoint"></a>プライベート エンドポイントを使用して Recovery Services コンテナー用のプロキシ サーバーを設定する

Azure VM またはオンプレミスのマシン用のプロキシ サーバーを構成するには、次の手順を実行します。

1. プロキシ サーバーからアクセスする必要がある次のドメインを追加します。
   
   | サービス | ドメイン名 | Port |
   | ------- | ------ | ---- |
   | Azure Backup | *.backup.windowsazure.com | 443 |
   | Azure Storage | *.blob.core.windows.net <br><br> *.queue.core.windows.net <br><br> *.blob.storage.azure.net | 443 |
   | Azure Active Directory <br><br> 「[Microsoft 365 Common および Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online)」のセクション 56 および 59 に記載されている更新されたドメイン URL。 | *.msftidentity.com、*.msidentity.com、account.activedirectory.windowsazure.com、accounts.accesscontrol.windows.net、adminwebservice.microsoftonline.com、api.passwordreset.microsoftonline.com、autologon.microsoftazuread-sso.com、becws.microsoftonline.com、clientconfig.microsoftonline-p.net、companymanager.microsoftonline.com、device.login.microsoftonline.com、graph.microsoft.com、graph.windows.net、login.microsoft.com、login.microsoftonline.com、login.microsoftonline-p.com、login.windows.net、logincert.microsoftonline.com、loginex.microsoftonline.com、login-us.microsoftonline.com、nexus.microsoftonline-p.com、passwordreset.microsoftonline.com、provisioningapi.microsoftonline.com <br><br> 20.190.128.0/18、40.126.0.0/18、2603:1006:2000::/48、2603:1007:200::/48、2603:1016:1400::/48、2603:1017::/48、2603:1026:3000::/48、2603:1027:1::/48、2603:1036:3000::/48、2603:1037:1::/48、2603:1046:2000::/48、2603:1047:1::/48、2603:1056:2000::/48、2603:1057:2::/48 <br><br> *.hip.live.com、*.microsoftonline.com、*.microsoftonline-p.com、*.msauth.net、*.msauthimages.net、*.msecnd.net、*.msftauth.net、*.msftauthimages.net、*.phonefactor.net、enterpriseregistration.windows.net、management.azure.com、policykeyservice.dc.ad.msft.net | 該当する場合。 |

1. プロキシ サーバー内でこれらのドメインへのアクセスを許可し、プロキシ サーバーが作成された VNET にプライベート DNS ゾーン (`*.privatelink.<geo>.backup.windowsazure.com`、`*.privatelink.blob.core.windows.net`、`*.privatelink.queue.core.windows.net`) をリンクするか、それぞれの DNS エントリと共にカスタム DNS サーバーを使用します。 <br><br> プロキシ サーバーが実行されている VNET と、プライベート エンドポイント NIC が作成された VNET をピアリングする必要があります。これにより、プロキシ サーバーがプライベート IP に要求をリダイレクトできるようになります。 

   >[!NOTE]
   >上のテキストで、`<geo>` はリージョン コードを示します (たとえば、米国東部と北ヨーロッパはそれぞれ *eus* と *ne*)。 リージョン コードについては、次の一覧を参照してください。
   >
   >- [すべてのパブリック クラウド](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
   >- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
   >- [ドイツ](../germany/germany-developer-guide.md#endpoint-mapping)
   >- [US Gov ](../azure-government/documentation-government-developer-guide.md)
   >- [geo コード リスト - サンプル XML](scripts/geo-code-list.md)

次の図は、必須の DNS エントリを含むプライベート DNS ゾーンに VNet がリンクされているプロキシ サーバーがあるセットアップ (Azure プライベート DNS ゾーンの使用) を示しています。 プロキシ サーバーには独自のカスタム DNS サーバーを含めることもでき、上記のドメインは条件付きで 168.63.129.16 に転送できます。 DNS 解決にカスタム DNS サーバー/ホスト ファイルを使用している場合、[DNS エントリの管理](#manage-dns-records)と[保護の構成](#configure-backup)に関するセクションを参照してください。

:::image type="content" source="./media/private-endpoints/setup-with-proxy-server-inline.png" alt-text="プロキシ サーバーがあるセットアップを示す図。" lightbox="./media/private-endpoints/setup-with-proxy-server-expanded.png":::

### <a name="create-dns-entries-when-the-dns-serverdns-zone-is-present-in-another-subscription"></a>DNS サーバーまたは DNS ゾーンが別のサブスクリプションに存在する場合に DNS エントリを作成する

このセクションでは、サブスクリプションに存在する DNS ゾーン、またはハブ アンド スポーク トポロジなど、Recovery Services コンテナーのプライベート エンドポイントを含むものとは異なるリソース グループを使用するケースについて説明します。 プライベート エンドポイント (および DNS エントリ) の作成に使用されるマネージド ID には、プライベート エンドポイントが作成されるリソース グループに対してのみアクセス許可があるため、必須の DNS エントリも追加で必要になります。 DNS エントリを作成するには、次の PowerShell スクリプトを使用します。
  
>[!Note]
>必要な結果を得るには、以下で説明するプロセス全体を参照してください。 プロセスは、2 回繰り返す必要があります (初回検出中に 1 回 (通信ストレージ アカウントに必要な DNS エントリを作成するため) と、初回バックアップ中に 1 回 (バックエンド ストレージ アカウントに必要な DNS エントリを作成するため))。

#### <a name="step-1-get-required-dns-entries"></a>手順 1: 必須の DNS エントリを取得する

[PrivateIP.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/PrivateIP.ps1) スクリプトを使用して、作成する必要があるすべての DNS エントリを一覧表示します。

>[!Note]
>構文内の `subscription` は、コンテナーのプライベート エンドポイントが作成されるサブスクリプションを示しています。

**スクリプトを使用するための構文**

```azurepowershell
./PrivateIP.ps1 -Subscription "<VaultPrivateEndpointSubscriptionId>" -VaultPrivateEndpointName "<vaultPrivateEndpointName>" -VaultPrivateEndpointRGName <vaultPrivateEndpointRGName> -DNSRecordListFile dnsentries.txt
```

**サンプル出力**

```
ResourceName                                                                 DNS                                                                       PrivateIP
<vaultId>-ab-pod01-fc1         privatelink.eus.backup.windowsazure.com         10.12.0.15
<vaultId>-ab-pod01-fab1        privatelink.eus.backup.windowsazure.com         10.12.0.16
<vaultId>-ab-pod01-prot1       privatelink.eus.backup.windowsazure.com         10.12.0.17
<vaultId>-ab-pod01-rec2        privatelink.eus.backup.windowsazure.com         10.12.0.18
<vaultId>-ab-pod01-ecs1        privatelink.eus.backup.windowsazure.com         10.12.0.19
<vaultId>-ab-pod01-id1         privatelink.eus.backup.windowsazure.com         10.12.0.20
<vaultId>-ab-pod01-tel1        privatelink.eus.backup.windowsazure.com         10.12.0.21
<vaultId>-ab-pod01-wbcm1       privatelink.eus.backup.windowsazure.com         10.12.0.22
abcdeypod01ecs114        privatelink.blob.core.windows.net       10.12.0.23
abcdeypod01ecs114        privatelink.queue.core.windows.net      10.12.0.24
abcdeypod01prot120       privatelink.blob.core.windows.net       10.12.0.28
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.32
abcdepod01prot110       privatelink.blob.core.windows.net       10.12.0.36
abcdeypod01prot121       privatelink.blob.core.windows.net       10.12.0.30
abcdeypod01prot122       privatelink.blob.core.windows.net       10.12.0.34
abcdepod01prot120       privatelink.blob.core.windows.net       10.12.0.26

```

#### <a name="step-2-create--dns-entries"></a>手順 2: DNS エントリを作成する

上記に対応する DNS エントリを作成します。 使用している DNS の種類に基づいて、DNS エントリを作成するための代替手段が 2 つあります。

**ケース 1**: カスタム DNS サーバーを使用している場合は、上記のスクリプトからレコードごとにエントリを手動で作成し、FQDN (ResourceName.DNS) が VNET 内のプライベート IP に解決されることを確認する必要があります。

**ケース 2**: Azure プライベート DNS ゾーンを使用している場合は、[CreateDNSEntries.ps1](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/CreateDNSEntries.ps1) スクリプトを使用してプライベート DNS ゾーンに DNS エントリを自動的に作成できます。 次の構文では、`subscription` にプライベート DNS ゾーンが存在します。

**スクリプトを使用するための構文**

```azurepowershell
/CreateDNSEntries.ps1 -Subscription <PrivateDNSZoneSubId> -DNSResourceGroup <PrivateDNSZoneRG> -DNSRecordListFile dnsentries.txt
```

#### <a name="summary-of-the-entire-process"></a>プロセス全体の概要

この回避策を使用して RSV のプライベート エンドポイントを正しく設定するには、以下が必要です。

1. コンテナーのプライベート エンドポイントを作成します (この記事で既に説明したとおり)。
1. 検出をトリガーします。 通信ストレージ アカウントの DNS エントリが存在しないため、SQL/HANA の検出は _UserErrorVMInternetConnectivityIssue_ で失敗します。
1. スクリプトを実行して DNS エントリを取得し、このセクションで前述した通信ストレージ アカウントの対応する DNS エントリを作成します。
1. 検出を再びトリガーします。 今回は、検出に成功します。
1. バックアップをトリガーします。 このセクションで前述したように、バックエンド ストレージ アカウントの DNS エントリが存在しないため、SQL/HANA と MARS のバックアップが失敗する可能性があります。
1. スクリプトを実行して、バックエンド ストレージ アカウントの DNS エントリを作成します。
1. バックアップを再びトリガーします。 今回は、バックアップが成功します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-create-a-private-endpoint-for-an-existing-backup-vaultbr"></a>既存のバックアップ コンテナー用にプライベート エンドポイントを作成できますか?<br>

いいえ。プライベート エンドポイントは、新しいバックアップ コンテナーに対してのみ作成できます。 そのため、コンテナーには、保護されている項目があってはなりません。 実際に、プライベート エンドポイント作成前にコンテナーに項目を保護することはできません。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-create-private-endpoints-for-this-vaultbr"></a>コンテナーに項目を保護しようとしましたが失敗し、コンテナーには保護された項目がまだ含まれていません。 このコンテナーのプライベート エンドポイントを作成できますか?<br>

いいえ。コンテナーに項目を保護しようとしたことが過去にあってはなりません。

### <a name="i-have-a-vault-thats-using-private-endpoints-for-backup-and-restore-can-i-later-add-or-remove-private-endpoints-for-this-vault-even-if-i-have-backup-items-protected-to-itbr"></a>バックアップと復元のためにプライベート エンドポイントを使用しているコンテナーがあります。 保護されているバックアップ項目がある場合でも、このコンテナーのプライベート エンドポイントを後で追加または削除できますか?<br>

はい。 コンテナーのプライベート エンドポイントと、そのコンテナーに保護されているバックアップ項目を既に作成済みの場合、必要に応じて、後でプライベート エンドポイントを追加または削除できます。

### <a name="can-the-private-endpoint-for-azure-backup-also-be-used-for-azure-site-recoverybr"></a>Azure Backup のプライベート エンドポイントを Azure Site Recovery にも使用することはできますか?<br>

いいえ。Backup のプライベート エンドポイントは、Azure Backup にのみ使用できます。 Azure Site Recovery 用の新しいプライベート エンドポイントを作成する必要があります (そのサービスでサポートされている場合)。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-private-endpointsbr"></a>この記事にある手順の 1 つを実行しないまま続行し、データ ソースを保護しました。 それでもプライベート エンドポイントを使用できますか?<br>

記事の手順に従わずに項目の保護まで続行した場合、コンテナーでプライベート エンドポイントを使用できなくなる可能性があります。 このため、項目の保護に進む前に、このチェックリストを参照することをお勧めします。

### <a name="can-i-use-my-own-dns-server-instead-of-using-the-azure-private-dns-zone-or-an-integrated-private-dns-zonebr"></a>Azure プライベート DNS ゾーンや統合されたプライベート DNS ゾーンを使用するのではなく、独自の DNS サーバーを使用できますか?<br>

はい。独自の DNS サーバーを使用できます。 ただし、このセクションで提示されているように、必要なすべての DNS レコードを確実に追加してください。

### <a name="do-i-need-to-perform-any-additional-steps-on-my-server-after-ive-followed-the-process-in-this-articlebr"></a>この記事の手順に従った後に、使用しているサーバーで追加の手順を実行する必要がありますか?<br>

この記事で詳しく説明されているプロセスに従った後、バックアップと復元にプライベート エンドポイントを使用するために追加の作業を行う必要はありません。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のセキュリティ機能](security-overview.md)に関する記事を確認します。