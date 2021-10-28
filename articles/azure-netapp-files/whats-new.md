---
title: Azure NetApp Files の新機能 | Microsoft Docs
description: Azure NetApp Files の最新の新機能と機能強化の概要について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: 0d733117cd9e1d4ac45dede368153bb77098fad5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252461"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp Files の新機能

Azure NetApp Files は定期的に更新されています。 この記事では、最新の新機能と機能強化の概要について説明します。 
 
## <a name="october-2021"></a>2021 年 10 月

* [Azure NetApp Files のリージョン間レプリケーション](cross-region-replication-introduction.md)の一般提供 (GA)

    リージョン間レプリケーション機能が一般提供 (GA) になりました。 この機能を使用する前に登録する必要がなくなりました。

* [Standard ネットワーク機能](configure-network-features.md) (プレビュー)

    Azure NetApp Files は、当初よりお客様から求められていたボリュームの **Standard** ネットワーク機能をサポートしています。 この機能は、革新的なハードウェアとソフトウェアの統合によって可能になりました。 標準ネットワーク機能により、Azure NetApp Files を含むすべてのワークロードのセキュリティ態勢と共に、シームレスで一貫したエクスペリエンスを実現するためのさまざまな機能を通じて、仮想ネットワーク エクスペリエンスが強化されます。
    
    Azure NetApp Files ボリュームの新規作成時に、*Standard* または *Basic* のネットワーク機能を選択できるようになりました。 Standard ネットワーク機能を選択すると、Azure NetApp Files ボリュームと委任されたサブネットに対して、次のサポートされている機能を使用できます。   
    * Azure NetApp Files ボリュームのある VNet の IP 制限を VM と同等に引き上げました
    * Azure NetApp Files の委任されたサブネット上で[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)をサポートすることでネットワーク セキュリティを強化しました
    * Azure NetApp Files の委任されたサブネットとの間で[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes)をサポートし、ネットワーク制御を強化しました
    * アクティブ/アクティブ VPN ゲートウェイ設定を介した接続
    * [ExpressRoute FastPath](../expressroute/about-fastpath.md) による Azure NetApp Files への接続

    現在、このパブリック プレビューは **米国中北部** から使用可能になりました。また、他のリージョンにも展開される予定です。  使用可能なリージョンや機能が増えた際には、[Azure Update](https://azure.microsoft.com/updates/) で詳細をご確認ください。  
 
    詳細については、「[Azure NetApp Files ボリュームのネットワーク機能を構成する](configure-network-features.md)」を参照してください。

## <a name="september-2021"></a>2021 年 9 月

* [Azure NetApp Files バックアップ](backup-introduction.md) (プレビュー)

    Azure NetApp Files オンライン スナップショットが、スナップショットのバックアップによって拡張されました。 この新しいバックアップ機能を使用すると、Azure NetApp Files スナップショットを、コスト効率が高く ZRS 対応の Azure Storage に迅速かつコスト効率の高い方法で保管できます。これにより、データを誤って削除しないように保護できます。 Azure NetApp Files バックアップにより、ONTAP の組み込みスナップショット テクノロジが拡張されます。 スナップショットが Azure Storage に保管されると、以前に保管されたスナップショットと比較して変更されたブロックのみが、効率的な形式でコピーおよび格納されます。 ただし、保管されたスナップショットは依然として完全な形で表現され、新しいボリュームに直接かつ個別に復元できます。これにより、反復的な完全増分回復プロセスが不要になります。 この高度なテクノロジにより、Azure Storage との間でデータを格納、取得するために必要なデータ量が最小限に抑えられるため、データ転送とストレージのコストを抑えることができます。 また、バックアップの保管にかかる時間が短縮されるため、より小さい回復ポイントの目標 (RPO) を実現できます。 Azure NetApp Files サービスで、ほぼ瞬時にデータを回復する必要がある場合に備えて最小数のスナップショットをオンラインで維持でき、また Azure NetApp Files バックアップ コンテナーで長期保存するために、これまでより長いスナップショットの履歴をより低いコストで作成することができるようになりました。 詳細については、「[Azure NetApp Files スナップショットのしくみ](snapshots-introduction.md)」を参照してください。

* Active Directory 接続の [ **[管理者]**](create-active-directory-connections.md#create-an-active-directory-connection) オプション (プレビュー)

    [Active Directory 接続] ページに **[管理者]** フィールドが追加されました。 ボリュームに対する管理者権限が付与されるユーザーまたはグループを指定できます。

## <a name="august-2021"></a>2021 年 8 月

* [既存の SMB ボリュームでの継続的可用性の有効化](enable-continuous-availability-existing-SMB.md)のサポート

    [新しい SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md#continuous-availability)ときに、既に SMB 継続的可用性 (CA) 機能を有効にすることができます。 既存の SMB ボリュームでも SMB CA を有効にできるようになりました。 「[既存の SMB ボリュームで継続的可用性を有効にする](enable-continuous-availability-existing-SMB.md)」をご覧ください。

* [スナップショット ポリシー](snapshots-manage-policy.md)が一般提供 (GA) になりました  

    スナップショット ポリシー機能が一般提供になりました。 この機能を使用する前に登録する必要がなくなりました。

* [NFS `Chown Mode` エクスポート ポリシーと UNIX エクスポート アクセス許可](configure-unix-permissions-change-ownership-mode.md) (プレビュー)   

    Azure NetApp Files の NFS ボリュームまたはデュアル プロトコル ボリュームで、Unix のセキュリティ スタイルを使用して Unix のアクセス許可と所有権モードの変更 (`Chown Mode`) のオプションを設定できるようになりました。 これらの設定は、ボリュームの作成時またはボリュームの作成後に指定できます。   

    所有権モードの変更 (`Chown Mode`) 機能を使用すると、ファイルとディレクトリの所有権管理機能を設定できます。 ボリュームのエクスポート ポリシーで設定を指定または変更できます。 `Chown Mode` には 2 つのオプションがあります。*Restricted* (既定) の場合、ルート ユーザーだけがファイルとディレクトリの所有権を変更でき、*Unrestricted* の場合、ルート以外のユーザーは所有するファイルとディレクトリの所有権を変更できます。   

    Azure NetApp Files の Unix のアクセス許可機能を使用すると、マウント パスの変更アクセス許可を指定できます。 

    これらの新機能により、特定のファイルやディレクトリのアクセス制御を、サービス オペレーターではなくデータ ユーザーに移管することができます。   

* [デュアルプロトコル (NFSv4.1 と SMB) ボリューム](create-volumes-dual-protocol.md) (プレビュー)   

    Azure NetApp Files では、[2020 年 7 月](#july-2020)の時点で NFSv3 および SMB ボリュームへのデュアルプロトコル アクセスが既にサポートされています。 デュアルプロトコル (NFSv4.1 と SMB) での同時アクセスを可能にし、LDAP ユーザー マッピングをサポートする、Azure NetApp Files ボリュームを作成できるようになりました。 この機能を使用すると、アクセスに NFSv4.1 を使用する Linux ベースのワークロードがあり、ワークロードによって生成されるデータが Azure NetApp Files ボリュームに格納されるようなユース ケースが可能になります。 それと同時に、担当者が Windows ベースのクライアントとソフトウェアを使用して、同じ Azure NetApp Files ボリュームの新しく生成されたデータを分析する必要がある場合があります。 デュアルプロトコルでの同時アクセス機能を利用すると、ワークロードによって生成されたデータを事後分析のために別のプロトコルを使用して別のボリュームにコピーする必要がなくなるため、ストレージのコストと運用時間を節約できます。 この機能は無料で (通常の Azure NetApp Files のストレージ コストは適用されます)、一般提供になっています。 詳細については、[デュアルプロトコル (NFSv4.1/SMB) での同時アクセス](create-volumes-dual-protocol.md)に関するドキュメントを参照してください。

## <a name="june-2021"></a>2021 年 6 月  

* [Azure NetApp Files ストレージ サービス アドオン](storage-service-add-ons.md)

    新しい Azure NetApp Files の **[Storage service add-ons]\(ストレージ サービス アドオン\)** メニュー オプションは、Azure NetApp Files ストレージ サービスで利用できるサードパーティ製エコシステム アドオンのための Azure portal の "起動パッド" となるものです。 この新しいポータル メニュー オプションを使用すると、アドオン タイルをクリックしてランディング ページを開き、アドオンにすばやくアクセスすることができます。  

    **NetApp アドオン** は、 **[Storage service add-ons]\(ストレージ サービス アドオン\)** に導入されたアドオンの最初のカテゴリです。 ここから **[NetApp Cloud Compliance]** にアクセスできます。 **[NetApp Cloud Compliance]** タイルをクリックすると、新しいブラウザーが開き、アドオンのインストール ページに移動します。 

* [手動 QoS 容量プール](azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type)が一般提供 (GA) になりました   

    手動 QoS 容量プール機能が一般提供になりました。 この機能を使用する前に登録する必要がなくなりました。 

* [1 つのサブスクリプションあたり、リージョンごとに 1 つの Active Directory に対する複数アカウントの共有 AD サポート](create-active-directory-connections.md#shared_ad) (プレビュー)   

    これまで、Azure NetApp Files はリージョンごとに 1 つの Active Directory (AD) のみをサポートしており、AD にアクセスするように構成できる NetApp アカウントは 1 つのみでした。 新しい **共有 AD** 機能を使用すると、同じサブスクリプションで同じリージョンに属する NetApp アカウントのいずれかによって作成された AD 接続を、すべての NetApp アカウントで共有できます。 たとえば、この機能を使用すると、サブスクリプションとリージョンが同じであるすべての NetApp アカウントに共通の AD 構成を使用して、SMB ボリューム、NFSv4.1 Kerberos ボリューム、またはデュアル プロトコル ボリュームを作成できます。 この機能を使用すると、同じサブスクリプションで同じリージョンであるすべての NetApp アカウントの AD 接続を確認できるようになります。

## <a name="may-2021"></a>2021 年 5 月 

* Azure NetApp Files Application Consistent Snapshot ツール [(AzAcSnap)](azacsnap-introduction.md) が一般提供になりました。 

    AzAcSnap は、Linux 環境 (SUSE、RHEL など) におけるサードパーティ データベース (SAP HANA) のデータ保護を簡略化できるコマンドライン ツールです。 このツールに関する最新の変更点については、[AzAcSnap のリリース ノート](azacsnap-release-notes.md)を参照してください。   

* [容量プールの課金タグのサポート](manage-billing-tags.md)   

    Azure NetApp Files で課金タグがサポートされるようになり、部署やその他の内部コンシューマーとコストを相互参照できるようになりました。 課金タグは、ボリューム レベルではなく容量プール レベルで割り当てられ、顧客の請求書に表示されます。

* [ADDS LDAP over TLS](configure-ldap-over-tls.md) (プレビュー) 

    既定では、クライアント アプリケーションとサーバー アプリケーション間の LDAP 通信は暗号化されません。 これは、ネットワーク監視デバイスまたはソフトウェアを使用して LDAP クライアントとサーバー コンピューター間の通信を表示できることを意味します。 LDAP クライアントを LDAP サーバーにバインドするために使用される資格情報 (ユーザー名とパスワード) が暗号化されずにネットワークを介して渡されるため、このシナリオは、分離されていない VNet または共有 VNet で LDAP 単純バインドを使用する場合に問題になる可能性があります。 LDAP over TLS (LDAPS とも呼ばれます) は、TLS を使用して LDAP クライアントと LDAP サーバー間の通信をセキュリティで保護するプロトコルです。 Azure NetApp Files では、LDAP over TLS を使用して、Active Directory ドメイン サーバー (ADDS) 間のセキュリティで保護された通信がサポートされるようになりました。 Azure NetApp Files では、LDAP over TLS を使用して、Active Directory に統合された LDAP サーバー間で認証されたセッションを設定できるようになりました。 LDAP over TLS 機能は、NFS、SMB、およびデュアルプロトコル ボリュームに対して有効にできます。 LDAP over TLS は、Azure NetApp Files で既定で無効になっています。  

* スループット [メトリック](azure-netapp-files-metrics.md)のサポート    

    Azure NetApp Files では、次のメトリックのサポートが追加されました。   
    * 容量プールのスループット メトリック
        * "*プールのボリュームに割り当てられたスループット*"
        * "*プールで消費されたスループット*"
        * "*プールのボリュームに割り当てられたスループットの割合*"
        * "*プールで消費されたスループットの割合*"
    * ボリュームのスループット メトリック
        * "*ボリュームに割り当てられたスループット*"
        * "*ボリュームで消費されたスループット*"
        * "*ボリュームで消費されたスループットの割合*"

* レプリケーション ボリュームの[サービス レベルの動的の変更](dynamic-change-volume-service-level.md)のサポート   

    Azure NetApp Files で、レプリケーションのレプリケーション元ボリュームとレプリケーション先ボリュームのサービス レベルの動的な変更がサポートされるようになりました。

## <a name="april-2021"></a>2021 年 4 月

* [ボリュームと容量プールの手動管理](volume-quota-introduction.md) (ハード クォータ) 

    Azure NetApp Files のボリュームと容量プールのプロビジョニングの動作が、手動で制御可能なメカニズムに変更されました。 ボリュームのストレージ容量は、ボリュームのセット サイズ (クォータ) に制限されます。 ボリューム消費量が最大になると、ボリュームも基になる容量プールも自動的には増加しません。 代わりに、ボリュームは "領域不足" という状態を受け取ります。 ただし、必要に応じて[容量プールまたはボリュームのサイズを変更](azure-netapp-files-resize-capacity-pools-or-volumes.md)することができます。 基になる容量プールと[ボリュームの容量を積極的に監視](monitor-volume-capacity.md)する必要があります。

    この動作変更は、多くのユーザーから寄せられた次の重要な要望によるものです。

    * 以前は、OS 領域または容量監視ツールの使用時に、VM クライアントには特定のボリュームの仮想プロビジョニングされた (100 TiB) 容量が示されていました。  この状況では、クライアントまたはアプリケーション側で容量が正確に可視化されない可能性があります。 この動作が修正されました。  
    * 容量プールの以前の自動拡張動作では、アプリケーション所有者は、プロビジョニングされる容量プール領域 (および関連コスト) を制御できませんでした。 "暴走プロセス" によって容量が急速にいっぱいになり、プロビジョニングされる容量が増大する可能性がある環境では、この動作は特に厄介でした。 この動作が修正されました。  
    * ユーザーは、ボリューム サイズ (クォータ) とパフォーマンスの直接的な相関関係を確認し、管理することを望んでいます。 以前の動作では、ボリューム (容量) と容量プールの自動拡張の (暗黙的な) オーバーサブスクリプションが可能でした。 そのため、ボリューム クォータがアクティブに設定またはリセットされるまで、ユーザーは直接的な相関関係を把握することができませんでした。 この動作が修正されました。

    ユーザーは、プロビジョニングされる容量を直接制御することを求めていました。 ユーザーは、ストレージの容量と使用率を制御し、バランスを取ることを望んでいます。 また、アプリケーション ボリュームの使用可能な容量、使用済みの容量、プロビジョニングされた容量とパフォーマンスをアプリケーション側およびクライアント側で可視化するだけでなく、コストを管理することも望んでいます。 この新しい動作により、この機能がすべて有効になりました。

* [SMB 継続的可用性 (CA) 共有による FSLogix ユーザー プロファイル コンテナーのサポート](azure-netapp-files-create-volumes-smb.md#continuous-availability) (プレビュー)  

    [FSLogix](/fslogix/overview) は、非永続的な Windows コンピューティング環境を強化、有効化、簡素化するソリューションのセットです。 FSLogix ソリューションは、パブリック クラウドとプライベート クラウド両方の仮想環境に適しています。 物理デバイスを使用するときに、FSLogix ソリューションを使用して、より移植性の高いコンピューティング セッションを作成することもできます。 FSLogix を使用すると、Azure NetApp Files を含む SMB 共有ネットワーク ストレージに格納されている永続的なユーザー プロファイル コンテナーに動的にアクセスできます。 Azure NetApp Files では、ストレージ サービスのメンテナンス イベントに対する FSLogix の回復性をさらに強化するために、ユーザー プロファイル コンテナーに対する [Azure NetApp Files の SMB 継続的可用性 (CA) 共有](azure-netapp-files-create-volumes-smb.md#continuous-availability)を介して SMB 透過フェールオーバーのサポートが拡張されています。 詳細については、Azure NetApp Files の [Azure Virtual Desktop ソリューション](azure-netapp-files-solution-architectures.md#windows-virtual-desktop)に関するセクションを参照してください。  

* [SMB3 プロトコル暗号化](azure-netapp-files-create-volumes-smb.md#smb3-encryption) (プレビュー) 

    Azure NetApp Files SMB およびデュアルプロトコル ボリュームで SMB3 プロトコル暗号化を有効にできるようになりました。 この機能では、[SMB 3.0 接続では AES CCM アルゴリズムを使用し、SMB 3.1.1 接続では AES GCM アルゴリズム](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607)を使用して、移動中の SMB3 データの暗号化を有効にします。 SMB3 暗号化を使用していない SMB クライアントは、このボリュームにアクセスできません。 保存データは、この設定に関係なく暗号化されます。 SMB 暗号化により、さらにセキュリティが強化されます。 ただし、クライアントに影響を与える可能性があります (メッセージの暗号化と暗号化解除のための CPU オーバーヘッド)。 また、ストレージ リソースの使用率にも影響する可能性があります (スループットの低下)。 ワークロードを運用環境にデプロイする前に、アプリケーションのパフォーマンスに対する暗号化の影響をテストする必要があります。

* [NFS 拡張グループを使用した Active Directory Domain Services (ADDS) LDAP のユーザーマッピング](configure-ldap-extended-groups.md) (プレビュー)   

    既定では、Azure NetApp Files は、[RFC 5531](https://tools.ietf.org/html/rfc5531) で定義されているように、NFS ユーザー資格情報を処理するときに最大 16 個のグループ ID をサポートします。 この新機能を使用すると、ユーザーの数が既定のグループ数のメンバーよりも多い場合に、グループ数を最大 1,024 まで増やすことができるようになりました。 また、この機能をサポートするために、NFS ボリュームを ADDS LDAP に追加できるようになりました。これにより、拡張グループ エントリ (最大 1,024 グループ) を含む Active Directory LDAP ユーザーがボリュームにアクセスできます。 

## <a name="march-2021"></a>2021 年 3 月
 
* [SMB 継続的可用性 (CA) 共有](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (プレビュー)  

    SMB 透過フェールオーバーを使用すると、SMB ボリュームにデータを格納してアクセスするサーバー アプリケーションへの接続を中断することなく、Azure NetApp Files サービスでメンテナンス操作を行うことができます。 SMB 透過フェールオーバーをサポートするために、Azure NetApp Files では、Azure VM で実行されている SMB を介して SQL Server アプリケーションで使用する SMB 継続的可用性共有オプションがサポートされるようになりました。 この機能は現在、Windows SQL Server でサポートされています。 Linux SQL Server では現在サポートされていません。 この機能を有効にすると、[単一インスタンス、Always-On フェールオーバー クラスター インスタンス、Always-On 可用性グループの展開](azure-netapp-files-solution-architectures.md#sql-server)において、SQL Server のパフォーマンスが大幅に向上し、スケーリングとコスト面の利点が得られます。 「[SQL Server の展開に Azure NetApp Files を使用する利点](solutions-benefits-azure-netapp-files-sql-server.md)」を参照してください。

* [リージョン間レプリケーションの宛先ボリュームの自動サイズ変更](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    リージョン間のレプリケーション関係では、宛先ボリュームは、ソース ボリュームのサイズに基づいて、自動的にサイズ変更されます。 そのため、宛先ボリュームを個別にサイズ変更する必要はありません。 この自動サイズ変更の動作は、ボリュームがアクティブなレプリケーション関係にある場合、または再同期操作でレプリケーション ピアリングが切断された場合に適用されます。 この機能を使用するには、ソースと宛先の両方のボリュームの容量プールに十分なヘッドルームを確保する必要があります。

## <a name="december-2020"></a>2020 年 12 月

* [Azure アプリケーション整合性スナップショット ツール](azacsnap-introduction.md) (プレビュー)    

    Azure アプリケーション整合性スナップショット ツール (AzAcSnap) は、Linux 環境 (SUSE、RHEL など) におけるサードパーティ データベース (SAP HANA) のデータ保護を省力化するコマンドライン ツールです。   

    AzAcSnap では、Azure NetApp Files と Azure Large Instance のボリューム スナップショット機能およびレプリケーション機能が活用されます。 これには、次のようなメリットがあります。

    * アプリケーション整合性のデータ保護 
    * データベース カタログ管理 
    * "*アドホック*" ボリューム保護 
    * 記憶域ボリュームの複製 
    * ディザスター リカバリーのサポート 

## <a name="november-2020"></a>2020 年 11 月

* [スナップショットの復元](snapshots-revert-volume.md)

    スナップショットを元に戻す機能を使用すると、特定のスナップショットが作成されたときの状態にボリュームをすばやく戻すことができます。 ほとんどの場合、ボリュームを元に戻す方が、個々のファイルをスナップショットからアクティブなファイル システムに復元するよりもはるかに高速です。 また、スナップショットを新しいボリュームに復元する場合と比べて、スペース効率が高くなります。

## <a name="september-2020"></a>2020 年 9 月

* [Azure NetApp Files のリージョン間レプリケーション](cross-region-replication-introduction.md) (プレビュー)

  Azure NetApp Files で、リージョン間レプリケーションがサポートされるようになりました。 この新しいディザスター リカバリー機能を使用することで、Azure NetApp Files のボリュームをある Azure リージョンから別のリージョンに、素早くかつコスト効率の良い方法でレプリケートすることができます。これにより、予測不可能なリージョンの障害からデータを保護することができます。 Azure NetApp Files のリージョン間レプリケーションでは、NetApp の SnapMirror® 技術を活用しています。これにより、変更されたブロックのみが、圧縮された効率的な形式でネットワーク経由で送信されます。 この独自のテクノロジにより、リージョン間でのレプリケーションに必要なデータ量が最小化されるため、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい回復ポイントの目標 (RPO) を実現できます。

* [手動 QoS 容量プール](azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type) (プレビュー)  

    手動 QoS 容量プールでは、ボリュームの容量とスループットを個別に割り当てることができます。 手動 QoS 容量プールで作成されたすべてのボリュームの合計スループットは、プールの合計スループットによって制限されます。 これは、プール サイズとサービスレベルのスループットの組み合わせによって決まります。 または、容量プールの [[QoS type]\(QoS の種類\)](azure-netapp-files-understand-storage-hierarchy.md#qos_types) を既定の設定である [auto]\(自動\) にすることもできます。 自動 QoS 容量プールでは、ボリュームに割り当てられたサイズ クォータに比例して、スループットがプール内のボリュームに自動的に割り当てられます。

* [LDAP 署名](create-active-directory-connections.md#create-an-active-directory-connection) (プレビュー)   

    Azure NetApp Files で、Azure NetApp Files サービスとユーザー指定の Active Directory Domain Services ドメイン コントローラーとの間の LDAP 参照をセキュリティで保護する LDAP 署名がサポートされるようになりました。 現在、この機能はプレビュー段階にあります。

* [AD 認証の AES 暗号化](create-active-directory-connections.md#create-an-active-directory-connection) (プレビュー)

    Azure NetApp Files で、SMB ボリュームの AES 暗号化を有効にする、DC への LDAP 接続で AES 暗号化がサポートされるようになりました。 現在、この機能はプレビュー段階にあります。 

* 新しい[メトリック](azure-netapp-files-metrics.md):   

    * 新しいボリューム メトリック: 
        * *ボリュームの割り当てサイズ*:ボリュームのプロビジョニングされたサイズ
    * 新しいプールのメトリック: 
        * *プールの割り当てサイズ*:プールのプロビジョニングされたサイズ 
        * *プールのスナップショット サイズの合計*:プール内のすべてのボリュームからのスナップショット サイズの合計

## <a name="july-2020"></a>2020 年 7 月

* [デュアルプロトコル (NFSv3 と SMB) ボリューム](create-volumes-dual-protocol.md)

    デュアルプロトコル (NFS v3 と SMB) での同時アクセスを可能にし、LDAP ユーザー マッピングをサポートする、Azure NetApp Files ボリュームを作成できるようになりました。 この機能により、生成したデータを Azure NetApp Files ボリュームに格納する Linux ベースのワークロードが存在する場合があるユース ケースに対応できます。 それと同時に、スタッフは Windows ベースのクライアントとソフトウェアを使用して、同じ Azure NetApp Files ボリュームの新しく生成されたデータを分析する必要があります。 デュアルプロトコルでの同時アクセス機能を利用すると、ワークロードによって生成されたデータを事後分析のために別のプロトコルを使用して別のボリュームにコピーする必要がなくなるため、ストレージのコストと運用時間を節約できます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、一般提供になっています。 詳細については、[デュアルプロトコルでの同時アクセスに関するドキュメント](create-volumes-dual-protocol.MD)を参照してください。

* [転送中の NFS v4.1 Kerberos 暗号化](configure-kerberos-encryption.MD)

    Azure NetApp Files で、AES-256 暗号化を使用した Kerberos モード (krb5、krb5i、krb5p) での NFS クライアント暗号化がサポートされるようになり、データ セキュリティが強化されました。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、一般提供になっています。 詳細については、[NFS v4.1 Kerberos 暗号化に関するドキュメント](configure-kerberos-encryption.MD)を参照してください。

* [ボリュームのサービス レベルの動的な変更](dynamic-change-volume-service-level.MD) (プレビュー) 

    クラウドでは IT の支出における柔軟性が期待されています。 ボリュームに必要なサービス レベルを使用している別の容量プールにボリュームを移動することで、既存の Azure NetApp Files ボリュームのサービス レベルを変更できるようになりました。 ボリュームのサービス レベルのインプレース変更では、データを移行する必要はありません。 ボリュームに対するデータ プレーン アクセスにも影響はありません。 既存のボリュームを変更し、パフォーマンス向上のために上位のサービス レベルを使用することも、コスト最適化のために下位のサービス レベルを使用することもできます。 この機能は無料です (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)。 これは現在プレビューの段階です。 機能のプレビューに登録するには、[ボリュームのサービス レベルの動的な変更に関するドキュメント](dynamic-change-volume-service-level.md)を参照してください。

* [ボリュームのスナップショット ポリシー](snapshots-manage-policy.md) (プレビュー) 

    Azure NetApp Files を利用すると、ボリュームの特定の時点のスナップショットを作成できます。 今後は、選択した頻度で Azure NetApp Files によってボリュームのスナップショットが自動的に作成されるよう指定するスナップショット ポリシーを作成できます。 スナップショットが毎時、毎日、毎週、または毎月のサイクルで作成されるようにスケジュールを設定できます。 スナップショット ポリシーの一環として、保持するスナップショットの最大数を指定することもできます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、プレビューになっています。 [ボリュームのスナップショット ポリシーのドキュメント](snapshots-manage-policy.md)に従って、機能のプレビューに登録できます。

* [NFS ルート アクセスのエクスポート ポリシー](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files で、ルート アカウントからボリュームにアクセスできるかどうかを指定できるようになりました。 

* [スナップショット パスの非表示](snapshots-edit-hide-path.md)

    マウントされたボリューム上の `.snapshot` ディレクトリ (NFS クライアント) または `~snapshot` フォルダー (SMB クライアント) をユーザーが参照してアクセスできるかどうかを Azure NetApp Files で指定できるようになりました。

## <a name="may-2020"></a>2020 年 5 月

* [バックアップ ポリシー ユーザー](create-active-directory-connections.md) (プレビュー)

    Azure NetApp Files では、Azure NetApp Files で使用するために作成されたコンピューター アカウントに対して昇格された特権を必要とする追加のアカウントを含めることができます。 指定したアカウントは、ファイルまたはフォルダー レベルで NTFS アクセス許可を変更できます。 たとえば、Azure NetApp Files の SMB ファイル共有にデータを移行するために使用される非特権サービス アカウントを指定できます。 バックアップ ポリシー ユーザー機能は、現在プレビューの段階です。

## <a name="next-steps"></a>次の手順
* [Azure NetApp Files とは](azure-netapp-files-introduction.md)
* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md) 
