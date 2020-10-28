---
title: StorSimple 8000 シリーズから Azure File Sync への移行
description: StorSimple 8100 または 8600 アプライアンスを Azure File Sync に移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331116"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 および 8600 から Azure File Sync への移行

StorSimple 8000 シリーズは、8100 または 8600 の物理オンプレミス アプライアンス、およびそれらのクラウド サービス コンポーネントによって表されます。 これらのアプライアンスのいずれかから Azure File Sync 環境にデータを移行することができます。 Azure File Sync は、StorSimple アプライアンスの移行先となる、既定の戦略的な長期 Azure サービスです。

StorSimple 8000 シリーズは 2022 年 12 月に[提供終了](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series)となります。 できるだけ早く移行の計画を開始することが重要です。 この記事では、Azure File Sync への移行を成功させるために必要な背景知識と移行手順について説明します。 

## <a name="phase-1-prepare-for-migration"></a>フェーズ 1:移行を準備する

このセクションでは、StorSimple ボリュームから Azure ファイル共有への移行の開始時に実行する必要がある手順について説明します。

### <a name="inventory"></a>インベントリ
移行の計画を始めるときは、まず、移行する必要がある StorSimple のすべてのアプライアンスとボリュームを明らかにします。 それが終わると、最適な移行パスを決定できます。

* StorSimple 物理アプライアンス (8000 シリーズ) の場合は、この移行ガイドを使用します。 
* 仮想アプライアンスである [StorSimple 1200 シリーズの場合は、別の移行ガイドを使用します](storage-files-migration-storsimple-1200.md)。

### <a name="migration-cost-summary"></a>移行コストの概要
StorSimple Data Manager リソースでのデータ変換ジョブによる StorSimple ボリュームから Azure ファイル共有への移行は無料です。 ただし、移行中および移行後に、次のような他のコストが発生する可能性があります。

* **ネットワーク エグレス:** StorSimple のファイルは、特定の Azure リージョン内のストレージ アカウントに存在します。 移行先の Azure ファイル共有を、同じ Azure リージョン内にあるストレージ アカウントにプロビジョニングする場合は、エグレス コストは発生しません。 この移行の一環として、異なるリージョン内のストレージ アカウントにファイルを移動することができます。 その場合は、エグレス コストがかかります。
* **Azure ファイル共有のトランザクション:** ファイルを Azure ファイル共有にコピーするとき (移行の一部として、またはその外部で)、ファイルとメタデータが書き込まれるときに、トランザクション コストがかかります。 ベスト プラクティスとしては、移行の間にトランザクション最適化レベルで Azure ファイル共有を開始します。 移行が完了した後で、目的のレベルに切り替えます。 以下のフェーズでは、適切なポイントでこれが示されています。
* **Azure ファイル共有レベルの変更:** Azure ファイル共有のレベルを変更すると、トランザクションのコストがかかります。 ほとんどの場合、前のポイントのアドバイスに従う方がコスト効率が高くなります。
* **ストレージ コスト:** この移行で Azure ファイル共有へのファイルのコピーが開始されるとき、Azure Files ストレージが使用され、課金されます。 
* **StorSimple:** StorSimple のデバイスとストレージ アカウントをプロビジョニング解除できるようになるまで、ストレージ、バックアップ、アプライアンスに対する StorSimple のコストが引き続き発生します。

### <a name="direct-share-access-vs-azure-file-sync"></a>共有への直接アクセスと Azure File Sync
Azure ファイル共有を使用すると、ファイル サービスのデプロイを構築するためのまったく新しい機会が開けます。 Azure ファイル共有はクラウド内の SMB 共有であり、使い慣れた Kerberos 認証と既存の NTFS アクセス許可 (ファイルとフォルダーの ACL) を使用してネイティブで動作する SMB プロトコルを介してユーザーが直接アクセスできるように設定できます。 [Azure ファイル共有への ID ベースのアクセスの詳細について参照してください](storage-files-active-directory-overview.md)。

直接アクセスの代わりに、[Azure File Sync](https://aka.ms/AFS) を使用できます。Azure File Sync は、頻繁に使用されるファイルをオンプレミスにキャッシュする StorSimple の機能に直接相当します。 

Azure File Sync は、次の 2 つの主要なコンポーネントに基づく Microsoft のクラウド サービスです。

* ファイルの同期とクラウドを使った階層化。
* Azure のネイティブ ストレージとしてのファイル共有。SMB や file REST などの複数のプロトコルを介してアクセスできます。 

Azure ファイル共有により、属性、アクセス許可、タイムスタンプなど、格納されるファイルに対する重要なファイルの忠実性が保持されます。 Azure ファイル共有を使用すると、クラウドに格納されているファイルやフォルダーをアプリケーションやサービスで解釈する必要がなくなります。 使い慣れたプロトコルと Windows エクスプローラーなどのクライアントを使用してネイティブにそれらにアクセスできます。 Azure ファイル共有を使用すると、汎用のファイル サーバー データとアプリケーション データをクラウドに格納することができます。 Azure ファイル共有のバックアップは組み込みの機能であり、Azure Backup によってさらに拡張できます。

この記事では、移行手順を中心に説明します。 移行前に Azure File Sync について詳しく知りたい場合は、次の記事をお勧めします。

* [Azure File Sync - 概要](https://aka.ms/AFS "概要")
* [Azure File Sync - デプロイ ガイド](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple のサービス データ暗号化キー
最初に StorSimple アプライアンスを設定するときに、" *サービス データ暗号化キー* " が生成され、キーを安全に格納するように指示されます。
このキーは、関連付けられている Azure ストレージ アカウント内のすべてのデータを暗号化するために使用され、そこに StorSimple アプライアンスによってファイルが格納されます。

移行が成功するには、"サービス データ暗号化キー" が必要です。 インベントリ内の各アプライアンスに対するこのキーをレコードから取得するには、今がよいタイミングです。

レコード内にキーが見つからない場合は、アプライアンスからキーを取得できます。 各アプライアンスには一意の暗号化キーがあります。

* Azure portal で Microsoft Azure にサポート リクエストを提出します。 リクエストの内容には、StorSimple デバイスのシリアル番号と、"サービス データ暗号化キー" を取得する要求が含まれている必要があります。 
* StorSimple サポート エンジニアから、画面共有会議についてお願いする連絡があります。
* 会議が始まる前に、[シリアル コンソール](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)または[リモート PowerShell セッション](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)を使用して、StorSimple アプライアンスに接続してください。 

> [!CAUTION]
> StorSimple アプライアンスに接続する方法を決定するときは、次の点を考慮してください。
> * HTTPS セッション経由での接続は、最も安全であり、推奨されるオプションです。
> * デバイスのシリアル コンソールへの直接接続はセキュリティで保護されていますが、ネットワーク スイッチ経由での接続は保護されていません。 
> * HTTP セッションで接続することもできますが、 **暗号化されない** ため、閉じた信頼できるネットワーク内で使用する場合を除き、推奨されません。

### <a name="storsimple-volume-backups"></a>StorSimple ボリュームのバックアップ
StorSimple により、ボリューム レベルでの差分バックアップが提供されます。 Azure ファイル共有にもこの機能があり、共有スナップショットと呼ばれます。

移行の一環として、バックアップを移動する義務もあるかどうかを判断します。

> [!CAUTION]
> StorSimple ボリュームからバックアップを移行する必要がある場合は、ここで終了してください。
>
> 現在は、最新のボリュームのバックアップのみを移行できます。 バックアップの移行のサポートは、2020 年の末から始まります。
> 今始めると、後でバックアップを "追加" できなくなります。
> 次のバージョンでは、バックアップを最も古いものから最新のものまで Azure ファイル共有に対して "再生" し、その間に Azure ファイル共有のスナップショットを取得する必要があります。

ライブ データだけを移行する必要があり、バックアップは必要ない場合は、このガイドに従って続けることができます。
必要なバックアップ保持期間が非常に短い場合は (たとえば、1 または 2 か月)、今すぐ移行を継続し、その期間の後で StorSimple リソースのプロビジョニングを解除することができます。 この方法を使用すると、必要なだけの量のバックアップ履歴を Azure ファイル共有側に作成できます。 両方のシステムを実行している期間は、追加のコストがかかるため、バックアップの保持期間が非常に短い場合以外は、この方法を考慮すべきではありません。

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>既存の StorSimple ボリュームを Azure ファイル共有にマップする
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>ストレージ アカウントの数
複数のストレージ アカウントをデプロイし、それぞれに少数の Azure ファイル共有を保持すると、移行にとってメリットになる可能性があります。

ファイル共有が非常にアクティブである (多くのユーザーやアプリケーションによって使用されている) 場合、2 つの Azure ファイル共有だけでストレージ アカウントのパフォーマンス制限に達する可能性があります。 このため、ベスト プラクティスは、それぞれが個別に独自のファイル共有を持つ複数のストレージ アカウントに移行し、通常はストレージ アカウントごとに 2 または 3 個以下の共有を使用することです。

ベスト プラクティスは、それぞれ 1 つのファイル共有を持つストレージ アカウントをデプロイすることです。 アーカイブできる共有がある場合は、複数の Azure ファイル共有を同じストレージ アカウントにプールすることができます。

これらの考慮事項は、Azure File Sync の場合より、(Azure VM またはサービス経由での) [クラウドへの直接アクセス](#direct-share-access-vs-azure-file-sync)の場合に、よりいっそう適用されます。これらの共有でのみ Azure File Sync を使用する場合は、いくつかを 1 つの Azure ストレージ アカウントにグループ化するのが適切です。 また、アプリをクラウドにリフト アンド シフトしてから、ファイル共有に直接アクセスすることも考えられます。 または、より高い IOPS とスループットの値を利用できるようにすることで、Azure のサービスの使用を始めることもできます。 

ご利用の共有のリストを作成した場合は、各共有を、それらが配置されるストレージ アカウントにマップする必要があります。

> [!IMPORTANT]
> Azure リージョンを決定した後、各ストレージ アカウントと Azure File Sync リソースが選択した同じリージョンと一致しするようにします。

### <a name="phase-1-summary"></a>フェーズ 1 の概要
「Phase 1:
* StorSimple のデバイスとボリュームの概要を理解しました。
* 各 StorSimple デバイスのサービス データ暗号化キーを取得したので、データ変換サービスでクラウド内の StorSimple ボリュームにアクセスする準備ができています。
* 移行する必要があるボリュームだけでなく、適切な数の Azure ファイル共有とストレージ アカウントにボリュームをマップする方法についても計画があります。

> [!CAUTION]
> StorSimple ボリュームからバックアップを移行する必要がある場合は、 **ここで止めておきます** 。
>
> この移行方法は、現在はバックアップを移行できない新しいデータ変換サービス機能に依存しています。 バックアップの移行のサポートは、2020 年の末から始まります。
> 現在は、ライブ データだけを移行できます。 今始めると、後でバックアップを "追加" できなくなります。
> バックアップを最も古いものから最新そしてライブ データまで Azure ファイル共有に対して "再生" し、その間に Azure ファイル共有のスナップショットを取得する必要があります。

ライブ データだけを移行する必要があり、バックアップは必要ない場合は、このガイドに従って続けることができます。

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>フェーズ 2:Azure Storage と移行リソースのデプロイ

このセクションでは、Azure で必要なさまざまなリソースの種類のデプロイに関する考慮事項について説明します。 一部は移行後にデータを保持し、一部は移行にだけ必要です。 デプロイ計画が完成するまで、リソースのデプロイを始めないでください。 デプロイした後で Azure リソースの特定の側面を変更することは困難です (場合によっては不可能です)。

### <a name="deploy-storage-accounts"></a>ストレージ アカウントをデプロイする
多くの場合、複数の Azure ストレージ アカウントをデプロイする必要があります。 この記事の前のセクションで作成したデプロイ計画に従って、それぞれで少数の Azure ファイル共有を保持します。 Azure portal で[計画したストレージ アカウントをデプロイします](../common/storage-account-create.md#create-a-storage-account)。 新しいストレージ アカウントについては、次の基本的な設定に従って検討してください。

##### <a name="subscription"></a>サブスクリプション
StorSimple のデプロイに使用したものと同じサブスクリプションを使用することも、別のサブスクリプションを使用することもできます。 唯一の制限は、サブスクリプションが StorSimple サブスクリプションと同じ Azure AD テナントに存在する必要があることです。 移行を始める前に、StorSimple サブスクリプションを正しいテナントに移動することを検討してください。 サブスクリプション全体を移動することだけができます。個々の StorSimple リソースを別のテナントまたはサブスクリプションに移動することはできません。

##### <a name="resource-group"></a>Resource group
リソース グループは、リソースと管理アクセス許可の編成を支援します。 詳細については、[Azureでのリソース グループ](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)に関するページを参照してください。

##### <a name="storage-account-name"></a>ストレージ アカウント名
ストレージ アカウントの名前は URL の一部になり、特定の文字制限があります。 名前付け規則では、ストレージ アカウント名は世界中で一意にする必要があり、小文字と数字のみを使用でき、3 から 24 文字で指定する必要があり、ハイフンやアンダースコアなどの特殊文字は使用できないことを考慮してください。 関連項目:[Azure Storage リソースの名前付け規則](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)。

##### <a name="location"></a>場所
ストレージ アカウントの "場所" つまり Azure リージョンは非常に重要です。 Azure File Sync を使用する場合は、すべてのストレージ アカウントがストレージ同期サービス リソースと同じリージョンに存在する必要があります。 選択する Azure リージョンは、ローカル サーバーやユーザーの近くにあるか、中央にある必要があります。 リソースをデプロイした後で、そのリージョンを変更することはできません。

StorSimple のデータ (ストレージ アカウント) が現在存在する場所とは別のリージョンを選択できます。

> [!IMPORTANT]
> 現在の StorSimple ストレージ アカウントの場所とは別のリージョンを選択した場合は、移行中に[エグレス料金が発生します](https://azure.microsoft.com/pricing/details/bandwidth)。 データは StorSimple のリージョンから出て、新しいストレージ アカウントのリージョンに入ります。 同じ Azure リージョン内にある場合は、帯域幅の料金はかかりません。

##### <a name="performance"></a>パフォーマンス
Azure ファイル共有には、Premium Storage (SSD) または Standard Storage を選択できます。 Standard Storage には、[ファイル共有用の複数の階層](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share)が含まれています。 StorSimple から移行するほとんどのお客様には、Standard Storage が適切なオプションです。

まだ決められない場合は、次のようにしてください。
* [Premium Azure ファイル共有のパフォーマンス](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)を必要とする場合は、Premium Storage を選択します。
* ホット データやアーカイブ データなど、汎用的なファイル サーバーのワークロードには、Standard Storage を選択します。 また、クラウド内の共有上のワークロードが Azure File Sync だけである場合も、Standard Storage を選択します。

##### <a name="account-kind"></a>アカウントの種類
* Standard Storage の場合は以下を選択します。 *StorageV2 (汎用 v2)*
* Premium ファイル共有の場合は以下を選択します。 *FileStorage*

##### <a name="replication"></a>レプリケーション
いくつかのレプリケーション設定を使用できます。 さまざまなレプリケーションの種類を確認してください。

次の 2 つのオプションのいずれかのみを選択してください。
* *ローカル冗長ストレージ (LRS)*
* *ゾーン冗長ストレージ (ZRS)* - すべての Azure リージョンで使用できるわけではありません。

> [!NOTE]
> LRS と ZRS の冗長の種類のみが、大きな 100 TiB の Azure ファイル共有と互換性があります。

グローバル冗長ストレージ (すべてのバリエーション) は現在サポートされていません。 冗長性の種類は後で切り替えることができ、Azure でサポートされるようになったら GRS に変更できます。

##### <a name="enable-100tib-capacity-file-shares"></a>100 TiB のファイル共有を有効にする

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="ストレージ アカウントを作成するための Azure portal の [詳細] タブを示す画像。":::
    :::column-end:::
    :::column:::
        Azure portal の新しいストレージ アカウント ウィザードの *[詳細]* セクションで、このストレージ アカウントでの *[大きいファイルの共有]* のサポートを有効にすることができます。 このオプションが使用できない場合は、通常、正しくない冗長性の種類を選択しています。 このオプションを使用できるようにするには、LRS または ZRS だけを選択してください。
    :::column-end:::
:::row-end:::

大きな 100 TiB のファイル共有を選択すると、次のような利点があります。
* 小さい容量 5 TiB のファイル共有と比較して、パフォーマンスが大幅に向上します。 (例: IOPS が 10 倍)
* 移行の完了にかかる時間が大幅に短縮されます。
* 移行するすべてのデータを保持するのに十分な容量がファイル共有にあることが保証されます。
* 将来の拡張に対応できます。

### <a name="azure-file-shares"></a>Azure ファイル共有
ストレージ アカウントが作成されたら、ストレージ アカウントの " *ファイル共有* " セクション に移動し、フェーズ 1 の移行プランに従って、適切な数の Azure ファイル共有をデプロイできます。 Azure での新しいファイル共有については、次の基本的な設定に従って検討してください。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="ストレージ アカウントを作成するための Azure portal の [詳細] タブを示す画像。":::
    :::column-end:::
    :::column:::
        </br>**名前**</br>小文字、数字、ハイフンがサポートされています。</br></br>**[クォータ]**</br>ここでのクォータは、Windows Server での SMB ハード クォータに相当します。 ベスト プラクティスとして、ここではクォータを設定しないでください。クォータに達すると、移行や他のサービスが失敗します。</br></br>**レベル**</br>新しいファイル共有に対して *[トランザクションが最適化されました]* を選択します。 移行の間には多くのトランザクションが発生し、後でワークロードに最適なレベルに変更する方がコスト効率が高くなります。
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
移行ジョブを保持する Azure リソースは、 *"StorSimple Data Manager"* という名前です。 新しいリソースをクリックしてそれを検索し、 *[作成]* をクリックします。

これは、オーケストレーション用に使用される一時的なリソースであり、移行が完了した後でプロビジョニングを解除します。 StorSimple ストレージ アカウントと同じサブスクリプション、リソース グループ、リージョンに、デプロイする必要があります。

### <a name="azure-file-sync"></a>Azure File Sync
Azure File Sync (AFS) を使用すると、最も頻繁にアクセスされるファイルのオンプレミス キャッシュを追加できます。 StorSimple のキャッシュ機能と同様に、AFS のクラウドを使った階層化機能を使用すると、Windows Server とマルチサイトの同期で使用可能なキャッシュ容量に対する制御の強化との組み合わせで、ローカル アクセスの待機時間が向上します。オンプレミスのキャッシュを使用することが目的である場合は、ローカル ネットワークに十分な "直接接続ストレージ (DAS)" 容量を備えた Windows Server VM を準備します (物理サーバーとフェールオーバー クラスターもサポートされます)。 

> [!IMPORTANT]
> Azure File Sync はまだ設定しないでください。共有の移行が完了した後で Azure File Sync を設定するのが最善です。 移行のフェーズ 4 より前に、AFS のデプロイを始めないでください。

### <a name="phase-2-summary"></a>フェーズ 2 の概要
フェーズ 2 が終了すると、ストレージ アカウントとすべての Azure ファイル共有がデプロイされ、StorSimple Data Manager リソースが作成されています。 フェーズ 3 で移行ジョブを実際に構成するとき、後者を使用します。

## <a name="phase-3-create-and-run-a-migration-job"></a>フェーズ 3: 移行ジョブを作成して実行する
このセクションでは、移行ジョブを設定し、選択したターゲットの Azure ファイル共有にコピーする必要がある StorSimple ボリューム上のディレクトリを、慎重にマップする方法について説明します。 作業を始めるには、StorSimple Data Manager に移動し、メニューで **[ジョブ定義]** を見つけて、 **[+ ジョブ定義]** を選択します。 ターゲット ストレージの種類は既定値です: *[Azure ファイル共有]* 。

![StorSimple 8000 シリーズの移行ジョブの種類。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "新しいジョブ定義ダイアログが開いた状態でジョブの種類が要求されている Azure portal のジョブ定義のスクリーンショット。ファイル共有または BLOB コンテナーにコピーします。")

> [!IMPORTANT]
> 移行ジョブを実行する前に、StorSimple ボリュームのスケジュールされた自動バックアップをすべて停止します。

:::row:::
    :::column:::
        ![StorSimple 8000 シリーズの移行ジョブ。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "データ変換ジョブの新しいジョブ作成フォームのスクリーンショット。")
    :::column-end:::
    :::column:::
        **Job definition name (ジョブ定義名)**</br>この名前は、移動しているファイルのセットを示すものである必要があります。 Azure ファイル共有に似た名前を付けることをお勧めします。 </br></br>**ジョブを実行する場所**</br>リージョンを選択するときは、StorSimple のストレージ アカウントと同じリージョンを選択する必要があります。または、利用できない場合は、それに近いリージョンを選択します。 </br></br><h3>source</h3>**ソース サブスクリプション**</br>StorSimple デバイス マネージャー リソースを格納するサブスクリプションを選択します。 </br></br>**StorSimple リソース**</br>アプライアンスが登録されている StorSimple デバイス マネージャーを選択します。 </br></br>**サービス データ暗号化キー**</br>レコードでキーが見つからない場合は、[この記事の前のセクション](#storsimple-service-data-encryption-key)を確認してください。 </br></br>**[デバイス]**</br>移行するボリュームが保持されている StorSimple デバイスを選択します。 </br></br>**数量**</br>ソース ボリュームを選択します。 後で、ボリューム全体またはサブディレクトリをターゲットの Azure ファイル共有に移行するかどうかを決定します。 </br></br><h3>移行先</h3>この移行ジョブのターゲットとして、サブスクリプション、ストレージ アカウント、Azure ファイル共有を選択します。
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 移行の実行には、最新のボリューム バックアップが使用されます。 ボリューム バックアップが少なくとも 1 つは存在すること確認します。ないと、ジョブは失敗します。 また、ライブ共有への差分ができるだけ小さくなるように、最新のバックアップが十分に最近のものであることを確認します。 作成したジョブを実行する **前** に、別のボリューム バックアップ を手動でトリガーして完了することをお勧めします。

### <a name="directory-mapping"></a>ディレクトリのマッピング
これは、移行ジョブでは省略可能です。 空のままにすると、StorSimple ボリュームのルートにある **すべての** ファイルとフォルダーが、ターゲットの Azure ファイル共有のルートに移動されます。 ほとんどの場合、ボリュームの内容全体を 1 つの Azure ファイル共有に格納することは、最適な方法ではありません。 多くの場合、Azure の複数のファイル共有にボリュームの内容を分割することをお勧めします。 プランをまだ作成していない場合は、最初に次のセクションを確認してください: 「[既存の StorSimple ボリュームを Azure ファイル共有にマップする](#map-your-existing-storsimple-volumes-to-azure-file-shares)」

移行計画の一環として、StorSimple ボリューム上のフォルダーを複数の Azure ファイル共有に分割する必要があると決定している場合があります。 その場合は、次のように分割することができます。
1. 1 つのボリューム上のフォルダーを移行するために複数のジョブを定義します。それぞれの StorSimple ボリューム ソースは同じですが、ターゲットとしての Azure ファイル共有は異なります。
1. ジョブ作成フォームの " *ディレクトリ マッピング* " セクションを使用し、特定の[マッピング セマンティクス](#semantic-elements)に従って、StorSimple ボリュームのフォルダーを指定したファイル共有に移行する必要があることを正確に指定します。

> [!IMPORTANT]
> このフォームのパスとマッピング式を、フォームの送信時に検証することはできません。 マッピングが正しく指定されていない場合、ジョブは完全に失敗するか、望ましくない結果を生成する可能性があります。 その場合は、通常、Azure ファイル共有を削除して再作成してから、その共有に対する新しい移行ジョブでマッピングのステートメントを修正するのが最善の方法です。 修正したマッピングのステートメントを使用して新しいジョブを実行すると、省略されたフォルダーを修正し、既存の共有に移動できます。 ただし、この方法で対処できるのは、パスのスペルミスのために省略されたフォルダーのみです。

#### <a name="semantic-elements"></a>セマンティックの要素
マッピングは左から右に表記されます: [\ソースパス] \> [\ターゲットパス]。

|セマンティックの文字          | 意味  |
|:---------------------------|:---------|
| **\\**                     | ルート レベル インジケーター        |
| **\>**                     | [ソース] と [ターゲット] のマッピング演算子        |
|**\|** または RETURN (改行) | 2 つのフォルダー マッピング命令の区切り記号。 </br>または、この文字を省略し、Enter キーを押して、次のマッピング式を独自の行に作成することもできます。        |

### <a name="examples"></a>例
フォルダー "User data" の内容を、ターゲット ファイル共有のルートに移動します。
``` console
\User data > \\
```
ボリュームの内容全体を、ターゲット ファイル共有の新しいパスに移動します。
``` console
\ \> \Apps\HR tracker
```
ソース フォルダーの内容を、ターゲット ファイル共有の新しいパスに移動します。
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
複数のソースの場所を、新しいディレクトリ構造に並べ替えます。
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>セマンティックのルール
* フォルダーのパスは、常にルート レベルを基準にして指定します。 
* 各フォルダーのパスは、ルート レベル インジケーター "\" で開始します。 
* ドライブ文字は含めません。 
* 複数のパスを指定するとき、ソースまたはターゲットのパスを重複することはできません。</br>
   無効なソース パスの重複の例:</br>
    *\\folder\1 > \\folder*</br>
    *\\folder\\1\\2 > \\folder2*</br>
   無効なターゲット パスの重複の例:</br>
   *\\folder > \\*</br>
   *\\folder2 > \\*</br>
* 存在しないソース フォルダーは無視されます。 
* ターゲットに存在しないフォルダー構造は作成されます。 
* Windows と同様に、フォルダー名は大文字と小文字が区別されませんが、大文字と小文字は維持されます。

> [!NOTE]
> StorSimple ボリュームの " *\System Volume Information* " フォルダーと " *$Recycle.Bin* " の内容は、変換ジョブによってコピーされません。

### <a name="phase-3-summary"></a>フェーズ 3 のまとめ
フェーズ 3 が終了すると、StorSimple ボリュームから Azure ファイル共有へのデータ変換ジョブが実行されます。 共有のための Azure File Sync の設定 (共有への移行ジョブが完了した後) またはインフォメーション ワーカーとアプリのための Azure ファイル共有への直接共有アクセスの設定に集中できるようになります。

## <a name="phase-4-accessing-your-azure-file-shares"></a>フェーズ 4: Azure ファイル共有へのアクセス

Azure ファイル共有へのアクセスには、主に次の 2 つの方法があります。

* **Azure File Sync:** オンプレミスの Windows Server に [Azure File Sync をデプロイする方法](#deploy-azure-file-sync)。 AFS には、StorSimple と同様に、ローカル キャッシュのすべての利点があります。
* **共有への直接アクセス:** [共有への直接アクセスをデプロイする方法](#deploy-direct-share-access)。 特定の Azure ファイル共有のアクセス シナリオでローカル キャッシュにメリットがない場合、またはオンプレミスの Windows Server をホストする機能がなくなった場合は、この方法を使用します。 ここでは、ユーザーとアプリは引き続き SMB プロトコルを使用して SMB 共有にアクセスしますが、これらの共有はオンプレミスのサーバー上ではなく、クラウドのものを直接使用します。

このガイドの「[フェーズ 1](#phase-1-prepare-for-migration)」で、どのオプションが最適であるかを既に決定しておく必要があります。

このセクションの残りの部分では、デプロイの手順について説明します。

### <a name="deploy-azure-file-sync"></a>Azure File Sync のデプロイ

ここでは、Azure File Sync の一部をデプロイします。
1. Azure File Sync クラウド リソースを作成します。
1. オンプレミス サーバーに Azure File Sync エージェントをデプロイします。
1. クラウド リソースにサーバーを登録します

同期グループはまだ作成しないでください。 Azure ファイル共有での同期の設定は、Azure ファイル共有への移行ジョブが完了した後でのみ行う必要があります。 移行が完了する前に File Sync の使用を開始した場合は、カットオーバーを開始するタイミングを簡単に判断できないため、移行が不必要に難しくなります。

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure File Sync クラウド リソースをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 移行の完了時にデータが存在する Azure リージョンを変更する場合は、この移行のターゲット ストレージ アカウントと同じリージョンに、ストレージ同期サービスをデプロイします。

#### <a name="deploy-an-on-premises-windows-server"></a>オンプレミスの Windows サーバーをデプロイする

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも 2012R2) のサーバーを作成します。 Windows Server フェールオーバー クラスターもサポートされています。 StorSimple 8100 または 8600 の前面にあるサーバーを再利用しないでください。
* 直接接続記憶域 (NAS ではなく DAS、NAS はサポートされていません) をプロビジョニングまたは追加します。

StorSimple 8100 または 8600 アプライアンスがローカルでキャッシュに使用できる容量以上のストレージを新しい Windows サーバーに用意することをお勧めします。 StorSimple アプライアンスを使用したものと同じ方法で Windows サーバーを使用します。アプライアンスと同じ容量のストレージがある場合、キャッシュ エクスペリエンスは同じでなくても似ているはずです。
Windows Server のストレージは、いつでも追加または削除できます。 これにより、ローカル ボリューム サイズとキャッシュに使用できるローカル ストレージの容量をスケールできます。

#### <a name="prepare-the-windows-server-for-file-sync"></a>ファイル同期のために Windows サーバーを準備する

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Windows サーバー上で Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows サーバーを準備し、インターネットに接続しておく必要があります。

> [!IMPORTANT]
> 作業を続ける前に、ファイルとフォルダーの Azure ファイル共有への StorSimple 移行が完了している必要があります。 ファイル共有への変更がそれ以上行われないことを確認します。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> クラウドを使った階層化を必ず有効にしておきます。 クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようにする Azure File Sync 機能です。 ローカルの関心のあるデータもローカルにキャッシュされ、高速でローカルのアクセス パフォーマンスが実現します。 この手順でクラウドを使った階層化を有効にするもう 1 つの理由は、この段階ではファイル コンテンツを同期せず、この時点で名前空間のみを移行するようにするためです。

### <a name="deploy-direct-share-access"></a>直接共有アクセスをデプロイする

:::row:::
    :::column:::
        [![インフォメーション ワーカーやアプリに直接 Azure ファイル共有を安全に公開する方法についてのステップ バイ ステップ ガイドとデモ - クリックして再生。](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        このビデオは、簡単な 5 つのステップでインフォメーション ワーカーやアプリに直接 Azure ファイル共有を安全に公開する方法についてのガイドとデモです。</br>
        ビデオでは、いくつかのトピックに関する専用のドキュメントが参照されています。
* [ID の概要](storage-files-active-directory-overview.md)
* [ストレージ アカウントにドメインを参加させる方法](storage-files-identity-auth-active-directory-enable.md)
* [Azure ファイル共有のネットワークの概要](storage-files-networking-overview.md)
* [パブリックおよびプライベート エンドポイントを構成する方法](storage-files-networking-endpoints.md)
* [S2S VPN を構成する方法](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN を構成する方法](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN を構成する方法](storage-files-configure-p2s-vpn-linux.md)
* [DNS 転送を構成する方法](storage-files-networking-dns.md)
* [DFS-N を構成する](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>フェーズ 4 のまとめ
このフェーズでは、 *StorSimple Data Manager* で複数の " *データ変換サービス* " (DTS) ジョブを作成して実行しました。 それらのジョブによって、ファイルとフォルダーが Azure ファイル共有に移行されました。 さらに、Azure File Sync のデプロイ、または直接共有アクセスのためのネットワークとストレージ アカウントの準備を行いました。

## <a name="phase-5-user-cut-over"></a>フェーズ 5:ユーザーのカットオーバー
このフェーズでは、移行を完了します。
* ダウンタイムを計画します。
* フェーズ 3 のデータ変換ジョブが実行されている間に、StorSimple 側で生成されたユーザーとアプリのすべての変更を取得します。 
* Azure File Sync または直接共有アクセスによる Azure ファイル共有を使用して、ユーザーを新しい Windows Server にフェールオーバーします。

### <a name="plan-your-downtime"></a>ダウンタイムを計画する
この移行アプローチを使用すると、ユーザーとアプリに若干のダウンタイムが発生します。 目標はダウンタイムを最小限に抑えることであり、次の考慮事項が役に立ちます。

* データ変換ジョブの実行中に、StorSimple ボリュームを使用可能な状態に保ちます。
* 共有へのデータ移行ジョブの実行が完了したら、StorSimple ボリュームと共有からユーザー アクセス (少なくとも書き込みアクセス) を削除します。 最後の RoboCopy により Azure ファイル共有が取得され、ユーザーをカットオーバーできます。 どこで RoboCopy を実行するかは、Azure File Sync または直接共有アクセスのどちらの使用を選択したかによって異なります。 後の RoboCopy のセクションで、その方法について説明します。
* RoboCopy での取得が完了すると、新しい場所をユーザーに公開する準備が整います。 Azure ファイル共有で直接、または AFS を使用して Windows Server 上の SMB 共有で。 多くの場合、DFS-N のデプロイは、迅速かつ効率的にカットオーバーを実行するのに役立ちます。 これにより、既存の共有アドレスの一貫性が維持され、移行されたファイルとフォルダーが含まれる新しい場所がポイントされるようになります。

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>名前空間がサーバーに完全に同期されたことを確認する

Azure ファイル共有への Azure File Sync を使用する場合は、ローカルの RoboCopy を開始する前に、名前空間全体のサーバーへのダウンロードが完了したことを確認することが重要です。 名前空間のダウンロードにかかる時間は、Azure ファイル共有内の項目の数によって異なります。 サーバーに名前空間が完全に移動されたかどうかを確認するには、次の 2 つの方法があります。

#### <a name="azure-portal"></a>Azure portal
Azure portal を使用して、名前空間が完全に移動されたことを確認できます。
* Azure portal にサインインし、同期グループに移動して、同期グループとサーバー エンドポイントの同期状態を確認します。 
* 注目する方向はダウンロードです。サーバー エンドポイントが新しくプロビジョニングされている場合、名前空間がまだダウンロードされていることを示す **初期同期** が表示されます。
それが " *初期同期* " 以外に変わったら、名前空間はサーバーに完全に設定されており、ローカルの RoboCopy に進むことができます。

#### <a name="windows-server-event-viewer"></a>Windows Server イベント ビューアー
Windows Server のイベント ビューアーを使用して、名前空間が完全に移動されたことを確認することもできます。

1. **イベント ビューアー** を開き、 **[アプリケーションとサービス]** に移動します。
1. **Microsoft\FileSync\Agent\Telemetry** に移動して開きます。
1. 完了した同期セッションに対応する、最新の **イベント 9102** を探します。
1. **[詳細]** を選択し、 **SyncDirection** の値が **Download** であるイベントを確認します。
1. サーバーへの名前空間のダウンロードを完了すると、 **Scenario** の値が **FullGhostedSync** で **HResult** = **0** である 1 つのイベントが発生します。
1. そのイベントがない場合は、 **SyncDirection** = **Download** および **Scenario** =  **"RegularSync"** である他の **9102 イベント** を調べることもできます。 これらのイベントのいずれかが見つかったら、現時点では、名前空間のダウンロードが完了し、同期するものがあるかどうかに関係なく、通常の同期セッションの進行中であることを示しています。

### <a name="a-final-robocopy"></a>最終的な RoboCopy
この時点で、オンプレミスの Windows サーバーと StorSimple 8100 または 8600 アプライアンスには違いがあります。

1. 移行の進行中にユーザーやアプリが StorSimple 側で生成した変更を取得する必要があります。
1. Azure File Sync を使用している場合: StorSimple アプライアンスにはキャッシュがありますが、Windows Server は名前空間のみであり、現時点ではファイル コンテンツはローカルに格納されていません。 そのため、最後の RoboCopy により、使用可能なローカルにキャッシュされたファイルの内容を取得することによってローカルの AFS キャッシュをすぐに開始でき、AFS サーバーに格納できます。
1. 無効な文字があるため、一部のファイルがデータ変換ジョブによって残されている可能性があります。 その場合は、Azure File Sync が有効になっている Windows Server にコピーします。 これらは、後で同期するように調整できます。特定の共有に Azure File Sync を使用していない場合は、StorSimple ボリュームで無効な文字を使用してファイルの名前を変更してから、Azure ファイル共有に対して RoboCopy を直接実行することをお勧めします。 

> [!WARNING]
> サーバーに Azure ファイル共有の名前空間が完全にダウンロードされる前に、RoboCopy を開始することはできません。
> 次の説明を参照してください: "[名前空間がサーバーに完全にダウンロードされたことを確認する](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 移行ジョブが最後に実行されてから変更されたファイルと、前のジョブで移動されていないファイルをコピーするだけです。 移行が完了した後で、サーバーに移動されなかった問題を解決できます。 [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)に関するページを参照してください。

RoboCopy にはいくつかのパラメーターがあります。 以下では、完全なコマンドと、これらのパラメーターを選択する理由の一覧を示します。

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy でマルチスレッドを実行できるようにします。 既定値は 8 です。最大値は 128 です。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      状態を UNICODE 形式でログ ファイルに出力します (既存のログを上書きします)。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      コンソール ウィンドウに出力します。 ログ ファイルへの出力と組み合わせて使用されます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      バックアップ アプリケーションが使用するのと同じモードで RoboCopy を実行します。 これにより、現在のユーザーがアクセス許可を持っていないファイルを、RoboCopy で移動できます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      RoboCopy がソース (StorSimple アプライアンス) とターゲット (Windows Server ディレクトリ) 間の差分のみを考慮できるようにします。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ファイル コピーの忠実性 (既定値は /COPY:DAT)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ、S = セキュリティ = NTFS ACL、O = 所有者情報、U = 監査情報
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      すべてのファイル情報をコピーします (/COPY:DATSOU と同等)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ディレクトリのコピーの忠実性 (既定値は /DCOPY:DA)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ
   :::column-end:::
:::row-end:::

RoboCopy コマンドのソースとターゲットの場所を構成する場合は、ソースとターゲットの構造が一致していることを確認してください。 移行ジョブのディレクトリ マッピング機能を使用した場合は、ルート ディレクトリの構造が StorSimple のボリュームの構造と異なる場合があります。 その場合は、サブディレクトリごとに 1 回ずつ、複数の RoboCopy ジョブが必要になることがあります。 この RoboCopy コマンドでは /MIR が使用されており、同じファイルは移動されません (たとえば階層化されたファイル)。しかし、ソースとターゲットのパスが間違っている場合は、/MIR でも、StorSimple のソース パスに存在しない Windows Server および Azure ファイル共有上のディレクトリ構造が消去されます。 そのため、移行中に行われた最新の変更で移行された内容を更新するという RoboCopy ジョブの目的を達成できるように、それらを正確に一致させる必要があります。 

RoboCopy のログ ファイルを調べて、ファイルが残っているかどうかを確認します。 問題がある場合は修正し、RoboCopy コマンドを再実行します。 対象のファイルまたはフォルダーの未解決の問題を解決する前に、StorSimple リソースのプロビジョニングを解除しないでください。

問題の特定の Azure ファイル共有をキャッシュするために Azure File Sync を使用せずに、直接共有アクセスを選択した場合は、次のようにします。
1. ローカルの Windows コンピューターにネットワーク ドライブとして [Azure ファイル共有をマウント](storage-how-to-use-files-windows.md#mount-the-azure-file-share)します。
1. StorSimple とマウントされた Azure ファイル共有の間で RoboCopy を実行します。 ファイルがコピーされない場合は、StorSimple 側で名前を修正して無効な文字を削除し、RoboCopy を再試行します。 前に示した RoboCopy コマンドは、StorSimple に対する不要な呼び出しを再度行うことなく、複数回実行できます。

### <a name="user-cut-over"></a>ユーザーのカットオーバー

Azure File Sync を使用している場合は、その AFS が有効になっている Windows Server に、StorSimple のボリュームに存在していた共有と一致する SMB 共有を作成することが、必要になる可能性があります。 ここで時間を無駄にしないために、このステップを前倒しし、早期に実行することができます。ただし、この時点より前に、Windows Server が変更されないようにする必要があります。

DFS-N のデプロイがある場合、DFN 名前空間を新しいサーバー フォルダーの場所に指すようにすることができます。 DFS-N のデプロイがなく、Windows Server を使用するローカル環境に 8100/8600 アプライアンスを配置した場合、そのサーバーをドメインから削除できます。 次に、Azure File Sync を有効にした新しい Windows Server をドメインに参加させます。 そのプロセスの間に、そのサーバーに古いサーバーと同じサーバー名と共有名を指定し、カットオーバーがユーザー、グループ ポリシー、スクリプトに対して透過的なままになるようにします。

[DFS-N の詳細を参照してください](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>プロビジョニング解除
リソースのプロビジョニングを解除すると、そのリソースおよびそのデータの構成にアクセスできなくなります。 プロビジョニング解除は元に戻すことができないため、移行が完了したこと、およびプロビジョニングを解除しようとしている StorSimple のファイル、フォルダー、またはボリュームのバックアップに依存関係がないことを確認するまで、続行しないでください。

始める前に、運用環境の新しい Azure File Sync のデプロイを少し観察することをお勧めします。 こうすることで、発生する可能性のある問題を修正できる選択肢がわかります。
Azure File Sync のデプロイを少なくとも数日間観察したら、リソースのプロビジョニング解除を次の順序で開始できます。

1. Azure portal を使用して StorSimple Data Manager リソースのプロビジョニングを解除します。
   それによりすべての DTS ジョブが削除されます。 コピー ログを簡単に取得することができなくなります。 それらがレコードにとって重要な場合は、プロビジョニングを解除する前に取得します。
1. StorSimple の物理アプライアンスが移行されていることを確認してから、登録を解除します。
   移行されたことを完全に確認できない場合は、続行しないでください。 まだ必要な間にこれらのリソースのプロビジョニングを解除した場合、データやその構成を回復することはできません。
1. StorSimple デバイス マネージャーに登録されているデバイスがこれ以上ない場合は、そのデバイス マネージャー リソース自体を削除することができます。
1. それから、Azure の StorSimple ストレージ アカウントを削除します。 やはり、続行する前に、移行が完了していることと、このデータに依存しているものやユーザーがないことを確認してください。
1. StorSimple 物理アプライアンスをデータ センターから取り外します。
1. StorSimple アプライアンスを所有している場合は、PC を自由にリサイクルできます。 
   デバイスがリースされている場合は、リース元に通知し、必要に応じてデバイスを返却します。

移行が完了しました。

> [!NOTE]
> まだ質問があるか、問題が発生していますか。</br>
> その場合は、AzureFilesMigration@microsoft.com にご連絡ください。


## <a name="next-steps"></a>次の手順

* [Azure File Sync: aka.ms/AFS](https://aka.ms/AFS) の詳細を確認します。
* [クラウドを使った階層化](storage-sync-cloud-tiering.md)ポリシーの柔軟性を理解します。
* Azure ファイル共有で [Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) を有効にして、スナップショットをスケジュールし、バックアップ保持期間のスケジュールを定義します。
* Azure portal で、一部のファイルが完全に同期していないことがわかった場合は、その問題を解決する手順について[トラブルシューティング ガイド](storage-sync-files-troubleshoot.md)を確認します。
