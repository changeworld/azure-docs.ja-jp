---
title: Azure Files (SMB) を使用した Windows での Azure VM の SAP NW 用 HA | Microsoft Docs
description: SAP アプリケーションのための Azure Files (SMB) を使用した Windows 上の Azure VM での SAP NetWeaver の高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: 458c63f27e9e17d71b9af97eda0de56f8f935a25
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251359"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>SAP アプリケーションのための Azure Files Premium SMB を使用した Windows 上の Azure VM での SAP NetWeaver の高可用性

## <a name="introduction"></a>はじめに
Azure Files Premium SMB が、Microsoft と SAP によって完全にサポートされるようになりました。 Azure Files Premium SMB ストレージは **SWPM 1.0 SP32** および **SWPM 2.0 SP09** 以降でサポートされています。  Azure Files Premium SMB 共有のサイズ設定には特別な要件があります。 このドキュメントには、Azure Files Premium SMB でワークロードを分散させる方法、Azure Files Premium SMB のサイズを適切に設定する方法、および Azure Files Premium SMB の最小インストール要件に関する、具体的な推奨事項が記載されています。

高可用性の SAP ソリューションには、**sapmnt**、**trans**、**interface ディレクトリ** をホストするために高可用性のファイル共有が必要です。 Azure Files Premium SMB は、Windows 環境上の SAP 用共有ファイル システムのためのシンプルな Azure PaaS ソリューションです。 Azure Files Premium SMB は、可用性セットおよび Availability Zones と組み合わせて使用できます。 Azure Files Premium SMB は、別のリージョンへのディザスター リカバリー シナリオにも使用できます。  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>SAP システム用 Azure Files Premium SMB のサイズ設定と分散

Azure Files Premium SMB のデプロイを計画するときは、次の点を評価する必要があります。
* ファイル共有名 **sapmnt** は、ストレージ アカウントごとに 1 回作成できます。  **/sapmnt/\<SID1\>** や **/sapmnt/\<SID2\>** など、同じ **/sapmnt** 共有上のディレクトリとして追加の SID を作成できます 
* 適切なサイズ、IOPS、スループットを選択します。  共有に推奨されるサイズは、SID あたり 256 GB です。  共有の最大サイズは 5,120 GB です
* Azure Files Premium SMB は、ストレージ アカウントあたり 100 万から 200 万ファイルを超える非常に大きな  **sapmnt** 共有では、最適に実行されない場合があります。何百万ものジョブ ログ ファイルが作成される何百万ものバッチ ジョブを使用するお客様は、[SAP Note 16083][16083] に従ってそれらを定期的に再編成する必要があります。必要な場合は、古いジョブ ログを別の Azure Files Premium SMB に移動またはアーカイブできます。 **sapmnt** が非常に大きくなることが予想される場合は、代わりのオプション (Azure ANF など) を検討する必要があります。
* プライベート ネットワーク エンドポイントを使用することをお勧めします
* 1 つのストレージ アカウントとそのファイル共有に統合する SID が多くなり過ぎないようにします。
* 一般的なガイダンスとして、2 から 4 個以下の非運用 SID を統合できます。
* 開発と QAS と運用環境全体を 1 つのストレージ アカウントやファイル共有に統合しないでください。共有で問題が発生すると、SAP ランドスケープ全体のダウンタイムが発生します。
* 非常に小さなシステムを除き、**sapmnt** と **transport ディレクトリ** を同じストレージ アカウントに統合することはお勧めできません。 SAP PAS インスタンスのインストールの間に、SAPInst によってトランスポート ホスト名が要求されます。  異なるストレージ アカウントの FQDN は、<ストレージ アカウント>.file.core.windows.net に入れる必要があります。
* インターフェイスに使用されるファイル システムを、 **/sapmnt/\<SID>** と同じストレージ アカウントに統合しないでください 
* SAP のユーザーとグループは "sapmnt" 共有に追加し、Azure portal で "**記憶域ファイル データの SMB 共有の管理者特権の共同作成者**" アクセス許可が設定されている必要があります。

**トランスポート**、**インターフェイス**、**sapmnt** を異なるストレージ アカウントに分けるのには、重要な理由があります。  これらのコンポーネントを個別のストレージ アカウントに分散させると、スループットと回復性が向上し、パフォーマンスの分析が簡単になります。  多くの SID や他のファイル システムが 1 つの Azure Files Storage アカウントに統合されていて、スループットの制限に達したためにストレージ アカウントのパフォーマンスが低下した場合、問題の原因になっている SID またはアプリケーションを特定するのが非常に困難になります。 

## <a name="planning"></a>計画 
> [!IMPORTANT]
> Active Directory 統合を使用した Azure Files Premium SMB への SAP 高可用性システムのインストールには、チーム間のコラボレーションが必要です。  Basis チーム、Active Directory チーム、Azure チームが協力して作業し、次のタスクを完了することを強くお勧めします。 
>
* Azure チーム – ストレージ アカウント、スクリプト実行、AD ディレクトリ同期のセットアップと構成。
* Active Directory チーム – ユーザー アカウントとグループの作成。
* Basis チーム – SWPM の実行と ACL の設定 (必要な場合)。

Active Directory 統合を使用した Azure Files Premium SMB への SAP NetWeaver 高可用性システムのインストールの前提条件。

* SAP サーバーは Active Directory ドメインに参加している必要があります。
* SAP サーバーが含まれる Active Directory ドメインは、Azure AD Connect を使用してAzure Active Directory にレプリケートされる必要があります。
* ExpressRoute を使用してオンプレミスのドメイン コントローラーに接続する必要がないように、Azure 環境内に少なくとも 1 つの Active Directory ドメイン コントローラーを配置することを強くお勧めします。
* Azure サポート チームは、[Active Directory 統合](../../../storage/files/storage-files-identity-auth-active-directory-enable.md#videos)のドキュメントで Azure Files SMB を確認する必要があります。 *このビデオでは、簡略化の理由から変更 (DNS) およびスキップ (DFS-N) された追加の構成オプションが示されています。* それでも、これらは有効な構成オプションです。 
* Azure Files PowerShell スクリプトを実行するユーザーは、Active Directory にオブジェクトを作成するアクセス許可を持っている必要があります。
* **SWPM バージョン 1.0 SP32 および SWPM 2.0 SP09 以降が必要です。SAPInst のパッチは 749.0.91 以降である必要があります。**
* スクリプトが実行される Windows Server に、PowerShell の最新リリースをインストールする必要があります。 

## <a name="installation-sequence"></a>インストールの順序
 1. Active Directory 管理者は、**ローカル管理者** 権限を持つ 3 人のドメイン ユーザーと 1 つのグローバル グループを、**ローカル Windows AD** に前もって作成しておく必要があります。 **SAPCONT_ADMIN@SAPCONTOSO.local** はドメイン管理者権限を持ち、**SAPInst**、 **\<sid>adm**、**SAPService\<SID>** を SAP システム ユーザーおよび **SAP_\<SAPSID>_GlobalAdmin** グループとして実行するために使用されます。 SAP インストール ガイドには、これらのアカウントに必要な具体的な詳細が含まれています。  **SAP ユーザー アカウントをドメイン管理者にすることはできません**。 一般に、**SAPInst を実行するには \<sid>adm を使用しない** ことをお勧めします。
 2. Active Directory 管理者または Azure 管理者は、**Azure AD Connect** Synchronization Service Manager を確認する必要があります。 既定では、**Azure Active Directory** へのレプリケートには約 30 分かかります。 
 3. Azure 管理者は、次のタスクを完了する必要があります。
     1. **Premium ZRS** または **LRS** を使用してストレージ アカウントを作成します。 ゾーン デプロイを使用しているお客様は、ZRS を選択する必要があります。 ここでは、**Standard** または **Premium アカウント** のどちらかを選択して設定する必要があります。![ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 1](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png)ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 1
         > [!IMPORTANT]
         > 運用環境で使用する場合は、**Premium アカウント** の使用をお勧めします。 非運用環境では、**Standard アカウント** で十分です。 
         >
         ![ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 2](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 2
         
         この画面は、既定の設定で問題ないはずです。
        
         ![ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 3](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)ストレージ アカウントの作成に関する Azure portal のスクリーンショット - ステップ 3 
         
         このステップでは、プライベート エンドポイントを使用することを決定します。
     1. ストレージ アカウントの **プライベート ネットワーク エンドポイントを選択します**。
     必要に応じて、 **<ストレージ アカウント名>.file.core.windows.net** に対する DNS A レコードを Windows DNS に追加します (これは、新しい DNS ゾーンで必要になる場合があります)。  これについては、DNS 管理者と相談してください。  組織の外部で新しいゾーンを更新しないでください。  
         ![pivate-endpoint-creation](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png)プライベート エンドポイントの定義に関する Azure portal のスクリーンショット。
         ![private-endpoint-dns-1](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png)プライベート エンドポイントの DNS の定義に関する DNS サーバーのスクリーンショット。    
     1. 適切なサイズで **sapmnt** ファイル共有を作成します。  推奨されるサイズは 256 GB で、650 IOPS、75 MB/秒のエグレス、50 MB/秒のイングレスが提供されます。
         ![create-storage-account-5](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)SMB 共有の定義に関する Azure portal のスクリーンショット。 
      
     1. [Azure Files の GitHub](../../../storage/files/storage-files-identity-ad-ds-enable.md#download-azfileshybrid-module) コンテンツをダウンロードして、[スクリプト](../../../storage/files/storage-files-identity-ad-ds-enable.md#run-join-azstorageaccountforauth)を実行します。   
     このスクリプトにより、コンピューター アカウントまたはサービス アカウントが Active Directory に作成されます。  スクリプトを実行するユーザーには、次のプロパティが必要です。 
         * スクリプトを実行するユーザーには、SAP サーバーが含まれる Active Directory ドメインにオブジェクトを作成するためのアクセス許可が必要です。 通常は、 **SAPCONT_ADMIN@SAPCONTOSO.local** のようなドメイン管理者アカウントが使用されます 
         * スクリプトを実行する前に、この Active Directory ドメイン ユーザー アカウントが Azure Active Directory (AAD) と同期していることを確認します。  たとえば、Azure portal を開き、AAD ユーザーに移動して、ユーザー **SAPCONT_ADMIN@SAPCONTOSO.local** が存在することを確認し、AAD ユーザー アカウント **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** を確認します。
         * ファイル共有が保持されているストレージ アカウントが含まれるリソース グループのこの Azure Active Directory ユーザー アカウントに、**共同作成者 RBAC** ロールを付与します。  この例では、ユーザー **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** に、対応するリソース グループに対する **共同作成者ロール** が付与されます 
         * 上で指定したアクセス許可を持つ Active Directory ドメイン ユーザー アカウントを使用して Windows Server にログオンしている間に、スクリプトを実行する必要があります。この例では、アカウント **SAPCONT_ADMIN@SAPCONTOSO.local** を使用します。
         >[!IMPORTANT]
         > PowerShell スクリプト コマンド **Connect-AzAccount** を実行するときは、Windows Server へのログオンに使用した Active Directory ドメイン ユーザー アカウントに対応し、マップされている Azure Active Directory ユーザー アカウントを使用することを、強くお勧めします。この例では、これはユーザー アカウント **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** です
         >
         この例のシナリオでは、Active Directory 管理者は **SAPCONT_ADMIN@SAPCONTOSO.local** として Windows Server にログオンし、**PS コマンド Connect-AzAccount** を使用するときは、ユーザー **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** として接続します。  Active Directory 管理者と Azure 管理者が協力してこのタスクを行うのが理想的です。
         ![powershell-script-1](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png)ローカル AD アカウントを作成する PowerShell スクリプトのスクリーンショット。

         ![smb-configured-screenshot](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)PowerShell スクリプトが正常に実行された後の Azure portal のスクリーンショット。 

         次の場所に [構成済み] と表示される必要があります  
         [ストレージ] -> [ファイル共有] -> [Active Directory: 構成済み]
     1. SAP ユーザー **\<sid>adm** と **SAPService\<SID>** および **SAP_\<SAPSID>_GlobalAdmin** グループを、Azure portal で **記憶域ファイル データの SMB 共有の管理者特権共同作成者** を使用して Azure Files Premium SMB ファイル共有に割り当てます 
     1. インストール後に **sapmnt ファイル共有** で ACL を確認し、**DOMAIN\CLUSTER_NAME$** アカウント、**DOMAIN\\\<sid>adm**、**DOMAIN\SAPService\<SID>** 、および **Group SAP_\<SID>_GlobalAdmin** を追加します。 これらのアカウントとグループは、**sapmnt ディレクトリを完全に制御できる必要があります**。

         > [!IMPORTANT]
         > このステップは、SAPInst をインストールする前に完了する必要があります。そうしないと、SAPInst によってファイル共有にディレクトリとファイルが作成された後では、ACL の変更が困難または不可能になります
         >
         ![ACL のプロパティ](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)割り当てられたユーザー権限のエクスプローラーのスクリーンショット。

         次のスクリーンショットでは、[オブジェクトの種類] -> [コンピューター] を選択することでコンピューター マシン アカウントを追加する方法が示されています。![ローカル AD にクラスター名が追加されている Windows Server のスクリーンショット](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)ローカル AD にクラスター名が追加されている Windows Server のスクリーンショット。
         
         DOMAIN\CLUSTER_NAME$ は、[オブジェクトの種類] から [コンピューター] を選択することで確認できます  
         ![AD コンピューター アカウントの追加のスクリーンショット - ステップ 2](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png)AD コンピューター アカウントの追加のスクリーンショット - ステップ 2 ![AD コンピューター アカウントの追加のスクリーンショット - ステップ 3](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png)AD コンピューター アカウントの追加のスクリーンショット - ステップ 3 ![コンピューター アカウントのアクセス プロパティのスクリーンショット](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png)コンピューター アカウントのアクセス プロパティのスクリーンショット。

     8. 必要に応じて、Azure Files に作成されたコンピューター アカウントを、アカウントに有効期限のない Active Directory コンテナーに移動します。  コンピューター アカウントの名前は、ストレージ アカウントの短い名前になります 

     
     > [!IMPORTANT]
     > SMB 共有用に Windows ACL を初期化するには、共有をドライブ文字に 1 回マウントする必要があります。
     >
     次に示すように、ストレージ キーはパスワードで、ユーザーは **Azure\\\<SMB share name>** です。![1 回限りの net use マウント](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png)SMB 共有の 1 回限りの net use マウントの Windows のスクリーンショット。

 4. Basis 管理者は、次のタスクを完了する必要があります。
     1. [ASCS/ERS ノードに Windows クラスターをインストールし、クラウド監視を追加します](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. 最初のクラスター ノードにインストールするときに、Azure Files SMB のストレージ アカウント名の指定を求められます。  <ストレージ アカウント名>.file.core.windows.net という FQDN を入力します。  SAPInst で 14 文字以上が受け付けられない場合は、SWPM のバージョンが古すぎます。
     3. [ASCS/SCS インスタンスの SAP プロファイルを変更します](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     4. [WSFC で SAP \<SID> ロール用のプローブ ポートを更新します](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     5. 2 番目の ASCS/ERS ノードに対する SWPM のインストールに進みます。 SWPM では、プロファイル ディレクトリのパスだけが必要です。  プロファイル ディレクトリへの完全な UNC パスを入力します。
     6. DB と PAS/AAS インストールの UNC プロファイル パスを入力します。
     7. PAS のインストールのときに、トランスポート ホスト名の指定を求められます。 トランスポート ディレクトリ用の別のストレージ アカウント名の FQDN を指定します。
     8. SID と trans ディレクトリで ACL を確認します。

## <a name="disaster-recovery-setup"></a>ディザスター リカバリーの設定
Azure Files Premium SMB では、ディザスター リカバリーのシナリオまたはリージョン間レプリケーションのシナリオがサポートされています。 このリンクを使用して、Azure Files Premium SMB ディレクトリ内のすべてのデータを、DR リージョンのストレージ アカウントに継続的に同期できます。 ディザスター リカバリー イベントと、ASCS インスタンスの DR リージョンへのフェールオーバーの後、SAPGLOBALHOST プロファイル パラメーターを、DR リージョン内の Azure Files SMB を指すように変更します。 同じ準備手順を DR ストレージ アカウントで実行して、ストレージ アカウントを Active Directory に参加させ、SAP ユーザーとグループに RBAC ロールを割り当てる必要があります。

## <a name="troubleshooting"></a>トラブルシューティング
ステップ 3.c でダウンロードした PowerShell スクリプトには、構成を検証するためのいくつかの基本的なチェックを実行するデバッグ スクリプトが含まれます。
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Powershell-script-output](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)デバッグ スクリプトの出力が示されている PowerShell のスクリーンショット。

![Powershell-script-technical-info](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)次の画面は、ドメイン参加が成功したことを検証するための技術情報を示したものです。
## <a name="useful-links--resources"></a>役に立つリンクとリソース

* SAP Note [2273806][2273806] ストレージまたはファイル システム関連のソリューションに関する SAP サポート 
* [Windows フェールオーバー クラスターへの SAP NetWeaver 高可用性のインストールおよび Azure 上での SAP ASCS/SCS インスタンスのファイル共有](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ](./sap-high-availability-architecture-scenarios.md)
* [ASCS クラスター構成にプローブ ポートを追加する](sap-high-availability-installation-wsfc-file-share.md)
* [フェールオーバー クラスターへの (A)SCS インスタンスのインストール](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806