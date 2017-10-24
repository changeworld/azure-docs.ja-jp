# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行
この記事では、クラシックから Resoure Manager デプロイメント モデル への、サービスとしてのインフラストラクチャ (IaaS) リソースの移行を有効にする方法について説明します。 [Azure Resource Manager の機能と利点](../articles/azure-resource-manager/resource-group-overview.md)の詳細を参照してください。 仮想ネットワーク サイト間ゲートウェイを使用して、サブスクリプション内で共存する 2 つのデプロイメント モデルのリソースを接続する方法について詳しく説明します。

## <a name="goal-for-migration"></a>移行の目的
Resource Manager では、テンプレートを使用して複雑なアプリケーションをデプロイできます。また、VM の拡張機能を使用して仮想マシンを構成し、アクセス管理とタグ付けを統合します。 Azure Resource Manager には、仮想マシンの可用性セットへのスケーラブルな並列デプロイも含まれます。 さらに、新しいモデルでは、計算、ネットワーク、ストレージの個別のライフサイクル管理が提供されます。 最後に、仮想ネットワークでの仮想マシンの実行によって、セキュリティが既定で有効になることが重要視されています。

機能面について言うと、クラシック デプロイメント モデルの計算、ネットワーク、およびストレージ機能のほとんどが、Azure Resource Manager でもサポートされています。 Azure Resource Manager の新機能を活用するには、クラシック デプロイメント モデルから既存のデプロイを移行します。

## <a name="supported-resources-for-migration"></a>移行がサポートされているリソース
次のクラシック IaaS リソースは、移行中にサポートされます。

* Virtual Machines
* 可用性セット
* Cloud Services
* ストレージ アカウント
* Virtual Networks
* VPN ゲートウェイ
* ExpressRoute ゲートウェイ _(仮想ネットワークと同じサブスクリプション内にある場合のみ)_
* ネットワーク セキュリティ グループ 
* ルート テーブル 
* 予約済み IP 

## <a name="supported-scopes-of-migration"></a>移行のサポート対象範囲
コンピューティング リソース、ネットワーク リソース、ストレージ リソースの移行を完了するには、4 つの方法があります。 これは次のとおりです。 

* 仮想マシンの移行 (仮想ネットワーク外)
* 仮想マシンの移行 (仮想ネットワーク内)
* ストレージ アカウントの移行
* 接続されていないリソース (ネットワーク セキュリティ グループ、ルート テーブル、予約済み IP)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>仮想マシンの移行 (仮想ネットワーク外)
Resource Manager デプロイメント モデルでは、既定でアプリケーションのセキュリティが適用されます。 すべての VM が Resource Manager モデルの仮想ネットワーク内にある必要があります。 Azure Platform は移行の一環として、仮想マシンを再起動 (`Stop`、`Deallocate`、および `Start`) します。 仮想マシンの移行先となる仮想ネットワークには、2 つのオプションがあります。

* まず、新しい仮想ネットワークを作成し、仮想マシンをその新しい仮想ネットワークに移行するようにプラットフォームに要求できます。
* また、Resource Manager の既存の仮想ネットワークに仮想マシンを移行することもできます。

> [!NOTE]
> この移行範囲では、移行中のある期間、管理プレーンおよびデータ プレーンのいずれの操作も許可されない場合があります。
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>仮想マシンの移行 (仮想ネットワーク内)
ほとんどの VM 構成では、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの間でメタデータのみが移行されます。 基になる VM は、同じネットワーク内の同じストレージを使用する同じハードウェアで実行されます。 移行中の一定期間は、管理プレーン操作が許可されない場合があります。 ただし、データ プレーンは引き続き機能します。 つまり、移行中は VM (クラシック) 上で実行されているアプリケーションでダウンタイムが発生することはありません。

次の構成は現在サポートされていません。 今後、これらのサポートが追加されたときに、この構成の一部の VM でダウンタイムが発生する場合があります (VM の動作の停止、割り当て解除、再起動が行われます)。

* 単一のクラウド サービスに複数の可用性セットがある。
* 単一のクラウド サービスに 1 つ以上の可用性セットと、可用性セットに存在しない VM がある。

> [!NOTE]
> 単一のクラウド サービスに 1 つ以上の可用性セットと、可用性セットに属さない VM がある 上記の特定の構成の場合は、データ プレーン ダウンタイムが発生します。
>
>

### <a name="storage-accounts-migration"></a>ストレージ アカウントの移行
シームレスに移行できるように、クラシック ストレージ アカウントで Resource Manager VM をデプロイできます。 この機能により、ストレージ アカウントとは関係なく、コンピューティングおよびネットワーク リソースを移行できます。また、移行する必要があります。 仮想マシンおよび仮想ネットワークを移行したら、ストレージ アカウントを移行して、移行プロセスを完了する必要があります。

> [!NOTE]
> Resource Manager デプロイメント モデルには、従来のイメージおよびディスクという概念がありません。 クラシック イメージやディスクは、ストレージ アカウントを移行すると Resource Manager スタックには表示されなくなりますが、バッキング VHD はストレージ アカウントに残ります。
>
>

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>接続されていないリソース (ネットワーク セキュリティ グループ、ルート テーブル、予約済み IP)
どの仮想マシンと仮想ネットワークにも接続されていないネットワーク セキュリティ グループ、ルート テーブル、予約済み IP は、個別に移行することができます。

<br>

## <a name="unsupported-features-and-configurations"></a>サポートされていない機能と構成
現時点では、一部の機能および構成セットはサポートされていません。 以下のセクションでは、こうした機能や構成セットに関する推奨事項について説明します。

### <a name="unsupported-features"></a>サポートされていない機能
次の機能は現在サポートされていません。 必要に応じて、これらの設定を削除し、VM を移行してから、Resource Manager デプロイメント モデルでその設定を再度有効にすることができます。

| リソース プロバイダー | 機能 | 推奨 |
| --- | --- | --- |
| 計算 | 関連付けのない仮想マシン ディスク。 | これらのディスクの背後にある VHD BLOB は、ストレージ アカウントの移行時に移行されます。 |
| 計算 | 仮想マシン イメージ。 | これらのディスクの背後にある VHD BLOB は、ストレージ アカウントの移行時に移行されます。 |
| ネットワーク | エンドポイント ACL。 | エンドポイント ACL を削除して移行をやり直します。 |
| ネットワーク | ExpressRoute ゲートウェイと VPN Gateway の両方を含んだ仮想ネットワーク  | 移行を開始する前に VPN Gateway を削除し、移行の完了後に VPN Gateway を作成し直します。 詳細については、[ExpressRoute の移行](../articles/expressroute/expressroute-migration-classic-resource-manager.md)に関するページを参照してください。|
| ネットワーク | Application Gateway | 移行を開始する前に Application Gateway を削除し、移行の完了後に Application Gateway を作成し直します。 |
| ネットワーク | VNet ピアリングを使用した仮想ネットワーク (VNet から ARM に移行した後、ピアリング)。 | 仮想ネットワークを Resource Manager に移行してからピアリングします。 詳細については、[VNet ピアリング](../articles/virtual-network/virtual-network-peering-overview.md)に関するページを参照してください。 | 

### <a name="unsupported-configurations"></a>サポートされていない構成
次の構成は現在サポートされていません。

| サービス | 構成 | 推奨 |
| --- | --- | --- |
| Resource Manager |クラシック リソース用のロールベースの Access Control (RBAC) |リソースの URI は移行後に変更されるため、移行後に必要になる RBAC ポリシーの更新を計画しておくことをお勧めします。 |
| 計算 |VM に関連付けられている複数のサブネット |サブネットのみを参照するように、サブネット構成を更新します。 |
| 計算 |仮想ネットワークに属しているが、明示的なサブネットが割り当てられていない仮想マシン |必要に応じて VM を削除することができます。 |
| 計算 |アラートの自動スケール ポリシーが適用されている仮想マシン |移行を実行すると、これらの設定は削除されます。 したがって、移行を行う前に環境を評価することを強くお勧めします。 移行の完了後に、アラート設定を再構成することもできます。 |
| 計算 |XML VM 拡張機能 (BGInfo 1.*、Visual Studio デバッガー、Web デプロイ、リモート デバッグ) |これはサポートされていません。 移行を続行するために、仮想マシンからこれらの拡張機能を削除することをお勧めします。削除していない場合、移行プロセスで自動的に削除されます。 |
| 計算 |Premium storage を使用したブート診断 |VM のブート診断機能を無効にしてから移行を続行してください。 移行が完了した後に、Resource Manager スタックでブート診断を再び有効にできます。 さらに、スクリーン ショットとシリアル ログに使用されている BLOB を削除する必要があるため、これらの BLOB に対して課金されることはなくなります。 |
| 計算 | Web/worker ロールを含む Cloud Services | 現在これはサポートされていません。 |
| コンピューティング | 2 つ以上の可用性セット (つまり、複数の可用性セット) を含むクラウド サービス。 |現在これはサポートされていません。 移行前に同じ可用性セットに Virtual Machines を移動してください。 |
| コンピューティング | Azure Security Center の拡張機能を備えた VM | Azure Security Center では、セキュリティを監視し、アラートを生成するために、仮想マシンに拡張機能を自動的にインストールします。 サブスクリプションで Azure Security Center のポリシーが有効になっている場合、通常はこれらの拡張機能が自動的にインストールされます。 Virtual Machines を移行するには、サブスクリプションでセキュリティ センター ポリシーを無効にして、Virtual Machines から Security Center の監視の拡張機能を削除してください。 |
| コンピューティング | バックアップまたはスナップショットの拡張機能を備えた VM | これらの拡張機能は、Azure Backup サービスで構成された Virtual Machines にインストールされます。 これらの Virtual Machines を移行するには、[こちらの](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault)ガイダンスに従います。  |
| ネットワーク |仮想マシンと Web/worker ロールを含む仮想ネットワーク |現在これはサポートされていません。 移行する前に、Web/ワーカー ロールを独自の仮想ネットワークに移動してください。 従来の仮想ネットワークが移行されると、それ以降、移行された Azure Resource Manager 仮想ネットワークは従来の仮想ネットワークを使ってピアリングされ、以前と同様の構成を実現できます。|
| ネットワーク | クラシック Express Route 回線 |現在これはサポートされていません。 これらの回線は、IaaS 移行を開始する前に、Azure Resource Manager に移行する必要があります。 これに関する詳細については、「[クラシック デプロイメント モデルから Resource Manager デプロイメント モデルへの ExpressRoute 回線の移行](../articles/expressroute/expressroute-move.md)」をご覧ください。|
| Azure App Service |App Service 環境を含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure HDInsight |HDInsight サービスを含む仮想ネットワーク |現在これはサポートされていません。 |
| Microsoft Dynamics Lifecycle Services |Dynamics Lifecycle Services によって管理される仮想マシンを含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure AD Domain Services |Azure AD ドメイン サービスを含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure RemoteApp |Azure RemoteApp デプロイを含む仮想ネットワーク |現在これはサポートされていません。 |
| Azure API Management |Azure API Management デプロイを含む仮想ネットワーク |現在これはサポートされていません。 IaaS VNET を移行するには、API Management デプロイの VNET を変更します。この操作では停止時間は発生しません。 |