---
title: TDE - Bring Your Own Key (BYOK) - Azure SQL Database | Microsoft Docs
description: SQL Database と Data Warehouse の Transparent Data Encryption (TDE) で Azure Key Vault を使用した Bring Your Own Key (BYOK) がサポートされます。 BYOK 対応 TDE の概要、利点、しくみ、考慮事項、および推奨事項。
keywords: ''
services: sql-database
documentationcenter: ''
author: aliceku
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 4a2f751c55a3bd386c6a984a3b5a16a24166f90c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043641"
---
# <a name="transparent-data-encryption-with-bring-your-own-key-support-for-azure-sql-database-and-data-warehouse"></a>Azure SQL Database と Data Warehouse の Transparent Data Encryption での Bring Your Own Key のサポート

[Transparent Data Encryption (TDE)]((https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption) の Bring Your Own Key (BYOK) サポートにより、TDE 保護機能と呼ばれる非対称キーを使用してデータベース暗号化キー (DEK) を暗号化することが可能になります。  TDE 保護機能は、ユーザーの管理下で、Azure のクラウドベースの外部キー管理システムである [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) に格納されます。 Azure Key Vault は、TDE に BYOK のサポートが統合された最初のキー管理サービスです。 TDE の DEK は、データベースのブート ページに格納され、TDE 保護機能によって暗号化および暗号化解除されます。 TDE 保護機能は Azure Key Vault に格納され、キー コンテナーの外部に移動されることはありません。 キー コンテナーへのサーバーのアクセスが取り消された場合、データベースを暗号化解除して、メモリに読み込むことはできません。  TDE 保護機能は論理サーバー レベルで設定され、そのサーバーに関連付けられているすべてのデータベースによって継承されます。 

BYOK のサポートにより、Azure Key Vault 機能を使用して、キーの交換、キー コンテナーのアクセス許可、キーの削除、すべての TDE 保護機能の監査/レポートの有効化などのキー管理タスクをユーザーが制御できるようになりました。 Key Vault はキーの一元管理を提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることで規制順守への対応を支援します。  


BYOK 対応 TDE には、次のような利点があります。
- TDE 保護機能を自己管理できるので、透明性が向上し、きめ細かく制御できる   
- Key Vault でホストすることにより、TDE 保護機能 (および他の Azure サービスで使用されている他のキーやシークレット) を一元管理できる
- 組織内でのキー管理とデータ管理の責任を分離することで、職務の分離を支援する
- Key Vault は Microsoft が暗号化キーを参照または抽出しないように設計されているので、ユーザー独自のクライアントからの信頼が向上する 
- キーの交換がサポートされている

> [!IMPORTANT]
> サービス管理 TDE を使用しているユーザーが Key Vault を使い始める場合、Key Vault の TDE 保護機能に切り替えるプロセス中も TDE は引き続き有効です。 ダウンタイムも、データベース ファイルの再暗号化もありません。 サービス管理キーから Key Vault キーへの切り替えに必要なのは、データベース暗号化キー (DEK) の再暗号化だけであり、これは高速のオンライン操作です。 
>

## <a name="how-does-tde-with-byok-support-work"></a>BYOK をサポートする TDE のしくみ
 
![Key Vault に対するサーバーの認証](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Key Vault の TDE 保護機能を使用するように TDE が最初に構成されたときに、サーバーは各 TDE 対応データベースの DEK を Key Vault に送信してキーのラップを要求します。 Key Vault から、暗号化されたデータベース暗号化キーが返され、ユーザー データベースに格納されます。  

>[!IMPORTANT]
>**TDE 保護機能は、Azure Key Vault に一度格納されると、Azure Key Vault の外部に移動されることはない**ことに注意してください。 論理サーバーが実行できるのは、Key Vault 内の TDE 保護機能のキー マテリアルにキー操作要求を送信することだけであり、**TDE 保護機能にアクセスしたり、TDE 保護機能をキャッシュしたりすることはありません**。 Key Vault 管理者には、サーバーの Key Vault アクセス許可をいつでも取り消すことができる権限があります。アクセス許可を取り消した場合、サーバーへのすべての接続が切断されます。 
>


## <a name="guidelines-for-configuring-tde-with-byok"></a>BYOK 対応 TDE の構成に関するガイドライン

### <a name="general-guidelines"></a>一般的なガイドライン
- Azure Key Vault と Azure SQL Database が必ず同じテナントに属するようにします。  キー コンテナーとサーバーのテナント間の対話は**サポートされていません**。
- 必要なリソースに使用するサブスクリプションを決定します。後でサーバーをサブスクリプション間で移動するには、BYOK 対応 TDE の新しいセットアップが必要になります。 リソースの移動の詳細については、[こちら](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-move-resources)をご覧ください。
- BYOK 対応 TDE を構成するときは、ラップ/ラップ解除操作の繰り返しによってキー コンテナーにかかる負荷を考慮することが重要です。 たとえば、論理サーバーに関連付けられているすべてのデータベースで同じ TDE 保護機能が使用されるため、そのサーバーのフェールオーバーが発生すると、サーバー内のデータベースと同じ数のキー操作がコンテナーに対してトリガーされます。 これまでの経験と文書化されている [Key Vault サービスの制限](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-service-limits)に基づき、コンテナー内の TDE 保護機能にアクセスするときに高可用性を常に確保するために、1 つのサブスクリプションの 1 つの Azure Key Vault に関連付けるデータベースの数を、Standard/General Purpose データベースの場合は最大 500 個、Premium/Business Critical データベースの場合は最大 200 個にすることをお勧めします。 
- 推奨: TDE 保護機能のコピーをオンプレミスに保持します。  これには、TDE 保護機能をローカルに作成するための HSM デバイスと、TDE 保護機能のローカル コピーを格納するためのキー エスクロー システムが必要です。  ローカル HSM から Azure Key Vault にキーを転送する方法については、[こちら](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys)をご覧ください。


### <a name="guidelines-for-configuring-azure-key-vault"></a>Azure Key Vault の構成に関するガイドライン

- キー (またはキー コンテナー) を誤って削除した場合に、データの損失から保護するために、[論理的な削除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)を有効にしてキー コンテナーを作成します。  [PowerShell を使用して、キー コンテナーの "soft-delete" プロパティを有効にする](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell)必要があります (このオプションは AKV ポータルからはまだ使用できませんが、SQL で必要です)。  
  - 論理的に削除されたリソースは、復旧または消去されない限り、一定期間 (90 日間) 保持されます。
  - **recover** アクションと **purge** アクションには、キー コンテナーのアクセス ポリシーに独自のアクセス許可が関連付けられています。 
- キー コンテナーにリソース ロックを設定して、この重要なリソースを削除できるユーザーを制御し、誤削除や許可されていない削除を防ぎます。  リソース ロックの詳細については、[こちら](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources)をご覧ください。

- Azure Active Directory (Azure AD) ID を使用して、論理サーバーにキー コンテナーへのアクセス権を付与します。  ポータル UI を使用すると、Azure AD ID が自動的に作成され、キー コンテナーのアクセス許可がサーバーに付与されます。  PowerShell を使用して BYOK 対応 TDE を構成する場合は、Azure AD ID を作成し、完了を確認する必要があります。 PowerShell を使用するときの詳細な手順については、[BYOK 対応 TDE の構成](transparent-data-encryption-byok-azure-sql-configure.md)に関する記事をご覧ください。

  >[!NOTE]
  >Azure AD ID が**誤って削除された場合や、サーバーのアクセス許可が取り消された場合** (キー コンテナーのアクセス ポリシーを使用)、サーバーはキー コンテナーにアクセスできなくなり、TDE で暗号化されたデータベースは 24 時間以内に削除されます。
  >

- VNET またはファイアウォールを使用せずに Azure Key Vault を構成します。  SQL がキー コンテナーにアクセスできなくなった場合、TDE で暗号化されたデータベースは、24 時間以内に削除されます。
- すべての暗号化キーの監査とレポートを有効にする: Key Vault が提供するログは、他のセキュリティ情報およびイベント管理 (SIEM) ツールに簡単に挿入できます。 Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) は、既に統合されているサービスの一例です。
- 暗号化されたデータベースの高可用性を確保するには、異なるリージョンに存在する 2 つの Azure Key Vault で 各論理サーバーを構成します。


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key-stored-in-azure-key-vault"></a>Azure Key Vault に格納された TDE 保護機能 (非対称キー) の構成に関するガイドライン

- ローカル HSM デバイス上で暗号化キーをローカルに作成します。 Azure Key Vault に格納できるように、非対称の RSA 2048 キーであることを確認します。
- キー エスクロー システムにキーをエスクローします。  
- 暗号化キー ファイル (.pfx、.byok、または .backup) を Azure Key Vault にインポートします。 
    

>[!NOTE] 
    >テストのために、Azure Key Vault を使用してキーを作成することは可能ですが、秘密キーはキー コンテナーの外部に移動できないため、このキーをエスクローすることはできません。  キーを失うと (キー コンテナーでの誤削除、期限切れなど)、データが完全に失われるので、実稼働データの暗号化に使用するキーを常にバックアップし、エスクローしてください。
    >
    
- 有効期限のないキーを使用する (既に使用中のキーに有効期限を設定しない): **キーの有効期限が切れると、暗号化されたデータベースは TDE 保護機能にアクセスできなくなり、24 時間以内に削除されます**。
- キーが有効であり、"*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行するためのアクセス許可があることを確認します。
- Azure Key Vault のキーを初めて使用する前に、Azure Key Vault キーのバックアップを作成します。 Backup-AzureKeyVaultKey コマンドの詳細については、[こちら](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1)をご覧ください。
- キーに変更を加えるたびに (ACL の追加、タグの追加、キー属性の追加など) 新しいバックアップを作成します。
- データベースの古いバックアップを復元できるように、キーを交換するときは、キー コンテナーにキーの**以前のバージョンを保持**します。 データベースの TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように**更新されることはありません**。  各バックアップの復元時には、バックアップの作成時に使用された TDE 保護機能が必要になります。 キーの交換を実行するには、[PowerShell を使用した Transparent Data Encryption 保護機能の交換](transparent-data-encryption-byok-azure-sql-key-rotation.md)に関する記事の手順に従います。
- サービス管理キーに戻したら、以前に使用したすべてのキーを Azure Key Vault に保持します。  これにより、Azure Key Vault に格納された TDE 保護機能を使用してデータベースのバックアップを復元できます。  保存されているすべてのバックアップが、サービス管理キーを使用して作成されるまで、Azure Key Vault を使用して作成された TDE 保護機能を保持する必要があります。  
- [Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) を使用して、これらのキーの回復可能なバックアップ コピーを作成します。
- セキュリティ インシデントの発生時に、データ損失のリスクを伴わずに、侵害された可能性のあるキーを削除するには、[侵害された可能性のあるキーの削除](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)に関する記事の手順に従ってください。


## <a name="high-availability-geo-replication-and-backup--restore"></a>高可用性、geo レプリケーション、バックアップ/復元

### <a name="high-availability-and-disaster-recovery"></a>高可用性と障害復旧

Azure Key Vault で高可用性を構成する方法は、使用しているデータベースと論理サーバーの構成によって異なります。ここでは、2 つの異なるケースの推奨される構成について説明します。  1 つ目のケースは、geo 冗長が構成されていないスタンドアロンのデータベースまたは論理サーバーです。  2 つ目のケースは、フェールオーバー グループまたは geo 冗長で構成されたデータベースまたは論理サーバーです。この場合、geo フェールオーバーが機能するように、各 geo 冗長コピーには、フェールオーバー グループ内のローカル Azure Key Vault が含まれている必要があります。 最初のケースで、geo 冗長が構成されていないデータベースおよび論理サーバーの高可用性が必要な場合は、同じキー マテリアルで、2 つの異なるリージョンの 2 つの異なるキー コンテナーを使用するようにサーバーを構成することを強くお勧めします。  これを実現するには、論理サーバーと同じリージョンに併置されたプライマリ キー コンテナーを使用して TDE 保護機能を作成し、別の Azure リージョンのキー コンテナーにキーを複製します。これにより、データベースの稼働中にプライマリ キー コンテナーが停止した場合に、サーバーはもう 1 つのキー コンテナーにアクセスできます。 Backup-AzureKeyVaultKey コマンドレットを使用して、プライマリ キー コンテナーから暗号化された形式のキーを取得し、Restore-AzureKeyVaultKey コマンドレットを使用して、2 つ目のリージョンのキー コンテナーを指定します。


![1 台のサーバーの HA (Geo-DR なし)](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Azure Key Vault で Geo-DR を構成する方法

暗号化されたデータベースの TDE 保護機能の高可用性を維持するには、既存のあるいは必要な SQL Database フェールオーバー グループまたはアクティブ geo レプリケーション インスタンスに基づいて、冗長 Azure Key Vault を構成する必要があります。  geo レプリケートされたサーバーごとに個別のキー コンテナーが必要であり、同じ Azure リージョンにサーバーと併置する必要があります。 1 つのリージョンの停止によってプライマリ データベースにアクセスできなくなり、フェールオーバーがトリガーされた場合は、セカンダリ データベースがセカンダリ キー コンテナーを使用して引き継ぐことができます。 
 
geo レプリケートされた Azure SQL Database の場合、Azure Key Vault の次の構成が必要です。
- リージョン内にキー コンテナーがあるプライマリ データベースが 1 つと、リージョン内にキー コンテナーがあるセカンダリ データベースが 1 つ。 
- 少なくとも 1 つのセカンダリが必要です。最大 4 つのセカンダリがサポートされています。 
- セカンダリのセカンダリ (チェーン) はサポートされていません。

次のセクションでは、セットアップと構成の手順について詳しく説明します。 

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault の構成手順

- [PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0) をインストールします。 
- 2 つの Azure Key Vault を 2 つの異なるリージョンに作成します。作成時に、[PowerShell を使用して各キー コンテナーの "soft-delete" プロパティを有効にします](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell) (このオプションは AKV ポータルからはまだ使用できませんが、SQL で必要です)。
- キーのバックアップと復元が機能するように、2 つの Azure Key Vault は、同じ Azure Geo で利用できる 2 つのリージョンに配置する必要があります。  SQL Geo-DR の要件を満たすために、2 つのキー コンテナーを異なる Geo に配置する必要がある場合は、オンプレミスの HSM からキーをインポートできる [BYOK プロセス](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys)に従います。
- 1 つ目のキー コンテナーに次のような新しいキーを作成します。  
  - RSA/RSA-HSA 2048 キー 
  - 有効期限なし 
  - キーが有効化され、取得、キーのラップ、キーのラップ解除の各操作を実行するためのアクセス許可がある 
- 主キーをバックアップし、2 つ目のキー コンテナーにキーを復元します。  「[Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1)」および「[Restore-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0)」をご覧ください。 

### <a name="azure-sql-database-configuration-steps"></a>Azure SQL Database の構成手順

次の構成手順は、新しい SQL デプロイから開始するか、既存の SQL Geo-DR デプロイを使用するかによって異なります。  まず、新しいデプロイの構成手順を概説します。次に、Azure Key Vault に格納されている TDE 保護機能を、Geo-DR リンクが既に確立されている既存のデプロイに割り当てる方法を説明します。 

新しいデプロイの手順:
- 以前に作成したキー コンテナーと同じ 2 つのリージョンに、2 つの論理 SQL サーバーを作成します。 
- 論理サーバーの TDE ウィンドウを選択し、論理 SQL サーバーごとに次の操作を行います。  
   - 同じリージョン内の AKV を選択します。 
   - TDE 保護機能として使用するキーを選択します。各サーバーでは、TDE 保護機能のローカル コピーが使用されます。 
   - ポータルでこれを行うと、論理 SQL サーバーの [AppID](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/overview) が作成されます。この ID は、キー コンテナーにアクセスするために、論理 SQL サーバーのアクセス許可を割り当てるときに使用されるので、削除しないでください。 アクセスを取り消すには、論理 SQL サーバー のアクセス許可ではなく、Azure Key Vault のアクセス許可を削除します。これは、キー コンテナーにアクセスするために論理 SQL サーバー のアクセス許可を割り当てるときに使用されます。
- プライマリ データベースを作成します。 
- [アクティブ geo レプリケーションのガイダンス](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview)に従ってシナリオを完了します。この手順により、セカンダリ データベースが作成されます。

![フェールオーバー グループと Geo-DR](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

>[!NOTE]
>データベース間の geo リンクの確立に進む前に、両方のキー コンテナーに同じ TDE 保護機能が存在することを確認することが重要です。
>

Geo-DR デプロイを使用した既存の SQL DB の手順:

論理 SQL サーバーが既に存在し、プライマリ データベースとセカンダリ データベースが既に割り当てられているため、Azure Key Vault の構成手順は、次の順序で実行する必要があります。 
- セカンダリ データベースをホストする論理 SQL サーバーから始めます。 
   - 同じリージョンにあるキー コンテナーを割り当てる 
   - TDE 保護機能を割り当てる 
- 次に、プライマリ データベースをホストする論理 SQL サーバーに移ります。 
   - セカンダリ DB に使用したものと同じ TDE 保護機能を選択する
   
![フェールオーバー グループと Geo-DR](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>キー コンテナーをサーバーに割り当てるときには、セカンダリ サーバーから始めることが重要です。  2 番目の手順では、キー コンテナーをプライマリ サーバーに割り当て、TDE 保護機能を更新します。この時点で、レプリケートされたデータベースで使用される TDE 保護機能を両方のサーバーで使用できるため、Geo-DR リンクは引き続き機能します。
>

SQL Database Geo-DR シナリオで、Azure Key Vault のユーザー管理のキーを使用して TDE を有効にする前に、SQL Database の geo レプリケーションに使用されるリージョンに、同じ内容の 2 つの Azure Key Vault を作成して管理することが重要です。  "同じ内容" とは、具体的には、すべてのデータベースで使用される TDE 保護機能に両方のサーバーがアクセスできるように、どちらのキー コンテナーにも同じ TDE 保護機能のコピーが含まれている必要があることを意味します。  今後、2 つのキー コンテナーを同期させておく必要があります。つまり、キーの交換後に 2 つのキー コンテナーに TDE 保護機能の同じコピーが含まれており、ログ ファイルまたはバックアップに使用されるキーの古いバージョンが保持されている必要があります。また、TDE 保護機能は同じキー プロパティを保持する必要があり、キー コンテナーは SQL の同じアクセス許可を保持する必要があります。  
 
[アクティブ geo レプリケーションの概要](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)に関する記事の手順に従ってテストを行い、フェールオーバーをトリガーします。2 つのキー コンテナーに対する SQL のアクセス許可が保持されていることを確認するために、この作業を定期的に行う必要があります。 


### <a name="backup-and-restore"></a>バックアップと復元

Key Vault のキーを使用して TDE でデータベースを暗号化すると、生成されるバックアップも同じ TDE 保護機能で暗号化されます。

Key Vault の TDE 保護機能で暗号化されたバックアップを復元するには、キー マテリアルが元のキー名で元のコンテナーに引き続き存在することを確認します。 データベースの TDE 保護機能が変更されても、データベースの古いバックアップが、最新の TDE 保護機能を使用するように更新されることは**ありません**。 そのため、データベースのバックアップを復元できるように、TDE 保護機能の古いバージョンをすべて Key Vault に保持しておくことをお勧めします。 

バックアップの復元に必要なキーが元のキー コンテナーに残っていない場合、"Target server <Servername> does not have access to all AKV Uris created between <Timestamp #1> and <Timestamp #2>. Please retry operation after restoring all AKV Uris."\(ターゲット サーバー <Servername> は <Timestamp #1> から <Timestamp #2> までの間に作成されたどの AKV URI にもアクセスできません。 すべての AKV URI を復元してから操作をやり直してください\) というエラー メッセージが返されます。

これを軽減するには、[Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) コマンドレットを実行して、サーバーに追加された Key Vault のキーのリストを取得します (ユーザーによって削除された場合を除きます)。 すべてのバックアップを復元できるようにするには、バックアップのターゲット サーバーがこれらのキーのすべてにアクセスできることを確認します。

   ```powershell
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
SQL Database のバックアップの回復の詳細については、[Azure SQL Database の復旧](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups)に関する記事をご覧ください。 SQL Data Warehouse のバックアップの回復の詳細については、[Azure SQL Data Warehouse の復元](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview)に関する記事をご覧ください。


バックアップ ログ ファイルに関するその他の考慮事項: TDE 保護機能が交換され、データベースが新しい TDE 保護機能を使用している場合でも、バックアップ ログ ファイルは元の TDE 暗号化機能で暗号化されたままになっています。  復元時に、データベースを復元するには両方のキーが必要になります。  サービス管理 TDE を使用するようにデータベースが変更された場合でも、Azure Key Vault に格納されている TDE 保護機能がログ ファイルで使用されている場合は、復元時にこのキーが必要になります。


