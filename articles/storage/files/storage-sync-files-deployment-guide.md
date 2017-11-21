---
title: "Azure File Sync (プレビュー) をデプロイする | Microsoft Docs"
description: "Azure File Sync をデプロイする方法を、開始から終了まで説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 42a0e7a3816e0f1d96951feac210e5770add4fe1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="deploy-azure-file-sync-preview"></a>Azure File Sync (プレビュー) をデプロイする
Azure File Sync (プレビュー) を使用して、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま、Azure Files で組織のファイル共有を一元化します。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事に記載されている手順を完了する前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」と「[Azure File Sync のデプロイの計画](storage-sync-files-planning.md)」を読むことを強くお勧めします。

## <a name="prerequisites"></a>前提条件
* Azure File Sync をデプロイするリージョンに Azure Storage アカウントと Azure ファイル共有があること。詳細については、次を参照してください。
    - Azure File Sync の「[リージョンの可用性](storage-sync-files-planning.md#region-availability)」
    - ストレージ アカウントを作成する方法の詳細な手順を示す「[ストレージ アカウントの作成](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」
    - ファイル共有を作成する方法の詳細な手順を示す「[ファイル共有の作成](storage-how-to-create-file-share.md)」
* Azure File Sync と同期する少なくとも 1 つのサポートされる Windows Server または Windows Server クラスターのインスタンス。サポートされている Windows Server バージョンの詳細については、[Windows Server との相互運用性](storage-sync-files-planning.md#azure-file-sync-interoperability)に関するページをご覧ください。

## <a name="deploy-the-storage-sync-service"></a>ストレージ同期サービスのデプロイ 
ストレージ同期サービスは、Azure File Sync の最上位レベルの Azure リソースです。ストレージ同期サービスをデプロイするには、[Azure ポータル](https://portal.azure.com/)に移動し、Azure File Sync を検索します。検索結果から **[Azure File Sync (preview)]\(Azure File Sync (プレビュー)\)** を選択した後、**[作成]** を選択して **[ストレージ同期のデプロイ]** タブを開きます。

開いたウィンドウに、次の情報を入力します。

- **名前**: ストレージ同期サービスの (サブスクリプションごとに) 一意の名前。
- **サブスクリプション**: ストレージ同期サービスを作成するサブスクリプション。 組織の構成方針によっては、1 つ以上のサブスクリプションにアクセスできることがあります。 Azure サブスクリプションは、各クラウド サービス (Azure Files など) に対する課金の最も基本的なコンテナーです。
- **リソース グループ**: リソース グループは、ストレージ アカウントやストレージの同期サービスなどの Azure リソースの論理グループです。 Azure File Sync 用の新しいリソース グループを作成するか、既存のリソース グループを選択できます (リソース グループをコンテナーとして使用して、組織のリソースを論理的に分離することをお勧めします。たとえば、HR リソースや特定のプロジェクトのリソースをグループ化します)。
- **場所**: Azure File Sync をデプロイするリージョン。この一覧ではサポートされているリージョンのみを使用できます。

完了したら、**[作成]** を選択して、ストレージ同期サービスをデプロイします。

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Azure File Sync で使用する Windows Server を準備する
フェールオーバー クラスターのサーバー ノードを含め、Azure File Sync で使用する予定のすべてのサーバーで次の手順を実行します。

1. **[Internet Explorer セキュリティ強化の構成]** を無効にします。 これは、初回のサーバー登録でのみ必要です。 サーバーの登録後に再び有効にできます。
    1. サーバー マネージャーを開きます。
    2. **[ローカル サーバー]** をクリックします。  
        ![サーバー マネージャー UI の左側にある [ローカル サーバー]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. **[プロパティ]** サブウィンドウで、**[IE セキュリティ強化の構成]** リンクを選択します。  
        ![サーバー マネージャー UI の [IE セキュリティ強化の構成] ウィンドウ](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、**[管理者]** と **[ユーザー]** の両方で **[オフ]** を選択します。  
        ![[オフ] が選択された [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウ](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. PowerShell 5.1.\* 以上を実行していることを確認します(Windows Server 2016 では PowerShell 5.1 が既定です)。 **$PSVersionTable** オブジェクトの **PSVersion** プロパティの値を調べて、PowerShell 5.1.\* を実行していることを確認できます。

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Windows Server 2012 R2 のほとんどのインストールのように、PSVersion の値が 5.1.\* 未満の場合は、[Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) をダウンロードしてインストールすることで簡単にアップグレードできます。 Windows Server 2012 R2 でダウンロードしてインストールする適切なパッケージは、**Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu** です。

3. [Azure PowerShell をインストールして構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)します。 最新バージョンの Azure PowerShell モジュールを使用することをお勧めします。

## <a name="install-the-azure-file-sync-agent"></a>Azure File Sync エージェントをインストールする
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 このエージェントは、[Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?linkid=858257) からダウンロードできます。 ダウンロードが完了したら、MSI パッケージをダブルクリックして Azure File Sync エージェントのインストールを開始します。

> [!Important]  
> フェールオーバー クラスターで Azure File Sync を使用する場合は、クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。

Azure File Sync エージェントのインストール パッケージは、追加のプロンプトがあまり多くないため、比較的簡単にインストールできます。 次を実行することをお勧めします。
- トラブルシューティングとサーバーのメンテナンスを簡素化するために、既定のインストール パス (C:\Program Files\Azure\StorageSyncAgent) をそのまま使用します。
- Microsoft Update を有効にして、Azure File Sync を最新の状態に保ちます。 機能の更新プログラムと修正プログラムを含め、Azure File Sync エージェントに対するすべての更新が Microsoft Update から実行されます。 最新の更新プログラムを Azure File Sync に適用することをお勧めします。詳細については、「[Azure File Sync の更新ポリシー](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)」をご覧ください。

Azure File Sync エージェントのインストールが完了すると、サーバー登録 UI が自動的に開きます。 このサーバーを Azure File Sync に登録する方法については、次のセクションをご覧ください。

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server をストレージ同期サービスに登録する
Windows Server をストレージ同期サービスに登録すると、サーバー (またはクラスター) とストレージ同期サービスの間に信頼関係が確立されます。 Azure File Sync エージェントがインストールされると、サーバー登録 UI が自動的に開きます。 開かない場合は、ファイルの場所 (C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe) から手動で開くことができます。 サーバー登録 UI が開いたら、**[サインイン]** を選択して開始します。

サインインすると、次の情報の入力を求められます。

![サーバー登録 UI のスクリーンショット](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure サブスクリプション**: ストレージ同期サービスを含むサブスクリプション (「[ストレージ同期サービスのデプロイ](#deploy-the-storage-sync-service)」をご覧ください)。 
- **リソース グループ**: ストレージ同期サービスを含むリソース グループ。
- **ストレージ同期サービス**: 登録するストレージ同期サービスの名前。

適切な情報を選択したら、**[登録]** を選択してサーバー登録を完了します。 登録プロセスの一環として、追加のサインインを求められます。

## <a name="create-a-sync-group"></a>同期グループの作成
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期状態が維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つと、Windows サーバー上のパスを表すサーバー エンドポイントが 1 つ含まれている必要があります。 同期グループを作成するには、[Azure ポータル](https://portal.azure.com/)でストレージ同期サービスに移動し、**[+ 同期グループ]** を選択します。

![Azure ポータルで新しい同期グループを作成する](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

開いたウィンドウに、同期グループとクラウド エンドポイントを作成するための次の情報を入力します。

- **同期グループ名**: 作成する同期グループの名前。 この名前は、ストレージ同期サービス内で一意である必要がありますが、理にかなった任意の名前を指定できます。
- **サブスクリプション**: 「[ストレージ同期サービスのデプロイ](#deploy-the-storage-sync-service)」でストレージ同期サービスをデプロイしたサブスクリプション。
- **ストレージ アカウント**: **［ストレージ アカウントの選択］** を選択した場合は、同期する Azure ファイル共有を持っているストレージ アカウントを選択できる別のウィンドウが奉持されます。
- **Azure ファイル共有**: 同期する Azure ファイル共有の名前。

サーバー エンドポイントを追加するには、新規に作成した同期グループに移動し、**[サーバー エンドポイントの追加]** を選択します。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

**[サーバー エンドポイントの追加]** ウィンドウで、次の情報を入力してサーバー エンドポイントを作成します。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**: 同期グループの一部として同期される Windows Server のパス。
- **クラウドの階層化**: クラウドの階層化を有効または無効にするスイッチ。 クラウドの階層化によって、使用頻度やアクセス頻度が低いファイルを Azure Files に階層化できます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、単一のサーバー エンドポイントで [ボリュームの空き領域] をボリュームの 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。

サーバー エンドポイントを追加するには、**[作成]** を選択します。 Azure ファイル共有と Windows Server でファイルの同期が維持されます。 

> [!Important]  
> 同期グループ内の任意のクラウド エンドポイントまたはサーバー エンドポイントに変更を加えることにより、ファイルを同期グループ内の他のエンドポイントと同期できます。 クラウド エンドポイント (Azure ファイル共有) を直接変更した場合、その変更は、Azure File Sync の変更検出ジョブによって最初に認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md#afs-change-detection)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [Azure File Sync のサーバー エンドポイントの追加/削除](storage-sync-files-server-endpoint.md)
- [Azure File Sync (プレビュー) へのサーバーの登録/登録解除](storage-sync-files-server-registration.md)
