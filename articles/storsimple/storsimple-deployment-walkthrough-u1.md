<properties 
   pageTitle="オンプレミスの StorSimple デバイスのデプロイ"
   description="StorSimple Update 1 のデバイスとサービスをデプロイするための手順とベスト プラクティスです。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/08/2015"
   ms.author="alkohli" />

# オンプレミスの StorSimple デバイスのデプロイ

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## 概要

Microsoft Azure StorSimple デバイスのデプロイへようこそ。

これらのデプロイのチュートリアルは、StorSimple 8000 シリーズ Update 1.0 に適用されます。

このチュートリアルのシリーズでは、StorSimple デバイスを構成する方法について説明します。また、インストール前のチェック リスト、構成の前提条件、詳細な構成手順についても紹介します。

> [AZURE.NOTE]Microsoft Azure の Web サイトと MSDN ライブラリで公開されている StorSimple のデプロイに関する情報は、StorSimple 8000 シリーズ デバイスのみに適用されます。7000 シリーズ デバイスの詳細については、[http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) を参照してください。7000 シリーズのデプロイについては、『[StorSimple システム クイック スタート ガイド](http://onlinehelp.storsimple.com/111_Appliance/)』を参照してください。

これらのチュートリアルの情報は、ユーザーが安全上の注意と構成チェック リストを確認していること、および StorSimple デバイスのパッケージを展開してラックに配置し、配線していることを想定しています。これらの作業がまだ済んでいない場合は、必要に応じて「[安全上の注意](https://msdn.microsoft.com/library/azure/dn772366.aspx)」、「[構成チェックリスト](https://msdn.microsoft.com/library/azure/dn757787.aspx)」、および[デバイスのハードウェアの設置](https://msdn.microsoft.com/library/azure/dn772375.aspx)に関するページを参照してください。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。開始する前に、インストール前のチェック リストを確認することをお勧めします。デプロイと構成のプロセスは、完了するまでに時間がかかることがあります。

## インストール前のチェック リスト

次のインストール前のチェック リストでは、StorSimple デバイスでソフトウェアを構成する前に収集する必要のある情報について説明します。事前にこの情報を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。

| | 必要条件 | 詳細 | 値 |
|---| --------------------- | ---------------------- | ------------- |
| 1 | ネットワーク設定 <ol><li>ネットワーク インターフェイス、4 x 1 GbE、2 x 10 GbE</li><li>固定コントローラーの IP</li><li>サブネット マスク</li><li>ゲートウェイ</li></ol> | 必要な IP アドレスの合計: 8 個 <ol><li>有効なネットワーク インターフェイスごとに 1 個、合計 6 個</li><li>コントローラーごとに 1 個、計 2 個、インターネットへの接続とサービス更新プログラムに必要</li><li>IP アドレスごとに 1 個</li><li>デバイスごとに 1 個</li></ol> | |
| 2 | シリアル アクセス | デバイスの初期構成 | はい/いいえ |
| 3 | DNS サーバーの IP アドレス | Microsoft Azure への接続に必要: 高可用性のために合計 2 つが必要 | |
| 4 | NTP サーバーの IP アドレス | Azure での時刻の同期に必要: 必須 1、省略可能 1 | |
| 5 | プロキシ サーバー (省略可能) | プロキシ サーバーの IP アドレス/完全修飾ドメイン名と、使用するポート | |
| 6 | Azure ストレージ アカウント | アカウント名やアクセス キーなどの資格情報、ストレージ アカウントごと | |
| 7 | クラウド ストレージ暗号化キー (推奨) | ボリューム コンテナーごと | |
| 8 | ホストの IQN | ホストごと | |

## デプロイの前提条件

ここでは、StorSimple Manager サービスと StorSimple デバイスの構成の前提条件について説明します。

### StorSimple Manager サービスの場合

開始する前に次の点を確認します。

- アクセスの資格情報を持つ Microsoft アカウントがある。

- アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがある。

- StorSimple Manager サービスの Microsoft Azure サブスクリプションが有効である。サブスクリプションは [Enterprise Agreement](http://azure.microsoft.com/pricing/enterprise-agreement/) から購入する必要があります。

- PuTTY などのターミナル エミュレーション ソフトウェアにアクセスできる。

### データセンターのデバイスの場合

デバイスを構成する前に次の点を確認します。

- 「[8100 デバイスの開梱](https://msdn.microsoft.com/library/azure/dn772327.aspx)」または「[8600 デバイスの開梱](https://msdn.microsoft.com/library/azure/dn772418.aspx)」の説明に従って、デバイスが完全に開梱されている。

- 「[8100 デバイスをラックに取り付ける](https://msdn.microsoft.com/library/azure/dn757749.aspx)」または「[8600 デバイスをラックに取り付ける](https://msdn.microsoft.com/library/azure/dn757745.aspx)」の説明に従って、デバイスがラックに取り付けられている。

- 「[8100 デバイスにケーブルを接続する](https://msdn.microsoft.com/library/azure/dn757738.aspx)」または「[8600 デバイスにケーブルを接続する](https://msdn.microsoft.com/library/azure/dn757762.aspx)」の説明に従って、デバイスの電源、ネットワーク、およびシリアル アクセスのケーブルが完全に接続されている。

- 「[StorSimple デバイスのネットワーク要件](https://msdn.microsoft.com/library/dn772371.aspx)」で説明するとおり、データセンターのファイアウォールでポートを開くと、iSCSI とクラウドのトラフィックが許可されます。

## デプロイの手順

StorSimple デバイスを構成し、StorSimple Manager サービスに接続するには、次の必要な手順を実行します。

- 手順 1. 新しいサービスを作成する 
- 手順 2. サービス登録キーを取得する
- 手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する 
- 手順 4. デバイスの最小セットアップを完了する
- 手順 5. ボリューム コンテナーを作成する 
- 手順 6. ボリュームを作成する
- 手順 7. ボリュームをマウント、初期化、フォーマットする 
- 手順 8. バックアップを取得する

これらの必要な手順以外に、ソリューションをデプロイする際に完了する必要のある手順がいくつかありますが、省略することもできます。これらの省略可能な手順では、次の方法について説明します。

- サービスの新しいストレージ アカウントを構成する
- PuTTY を使用してデバイスのシリアル コンソールに接続する
- Windows Server ホストの IQN を取得する
- 手動バックアップの作成
- MPIO を構成する

デプロイの詳細な手順では、どの時点でこれらの省略可能な手順を実行するかを示しています。

## 手順 1. 新しいサービスを作成する

StorSimple Manager サービスでは、複数の StorSimple デバイスを管理できます。StorSimple Manager サービスの新しいインスタンスを作成するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT]サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。このストレージ アカウントは、ボリューム コンテナーを作成するときに使用します。
>
> * ストレージ アカウントを自動的に作成していない場合は、「[サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service)」に移動して詳細な手順をご確認ください。 
> * ストレージ アカウントの自動作成を有効にしている場合は、「[手順 2. サービス登録キーを取得する](#step-2:-get-the-service-registration-key)」に進みます。

## 手順 2. サービス登録キーを取得する

StorSimple Manager サービスが稼働したら、サービス登録キーを取得する必要があります。このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

管理ポータルで、次の手順を実行します。

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## 手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する

次の手順の説明に従い、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの初期セットアップを完了します。この手順を完了するには、ターミナル エミュレーション ソフトウェアを使用する必要があります。詳細については、「[PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console)」を参照してください。

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## 手順 4. デバイスの最小セットアップを完了する

StorSimple デバイスの最小構成を完了するには、次の手順を実行する必要があります。

- セカンダリ DNS サーバーをセットアップします。
- 1 つ以上のネットワーク インターフェイスで iSCSI を有効にします。
- 両方のコントローラーに固定の IP アドレスを割り当てます。

デバイスの最小セットアップを完了するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 手順 5. ボリューム コンテナーを作成する

ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。StorSimple デバイス上のボリュームのプロビジョニングを開始する前に、ボリューム コンテナーを作成する必要があります。

ボリューム コンテナーを作成するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 手順 6. ボリュームを作成する

ボリューム コンテナーを作成したら、サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングできます。ボリュームを作成するには、管理ポータルで次の手順を実行します。

> [AZURE.IMPORTANT]Azure StorSimple は、仮想プロビジョニングされたボリュームのみを作成できます。完全にプロビジョニングされたボリュームまたは部分的にプロビジョニングされたボリュームを Azure StorSimple システム上に作成することはできません。

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 手順 7. ボリュームをマウント、初期化、フォーマットする

Windows Server ホスト上で次の手順を実行します。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 手順 8. バックアップを取得する

バックアップにより、特定の時点のボリュームを保護し、復元時間を最小限に抑えながら回復性を向上させることができます。StorSimple デバイスでは、ローカル スナップショットとクラウド スナップショットという 2 種類のバックアップを実行できます。どちらの種類のバックアップも、**[スケジュール設定]** または **[手動]** で実行できます。

スケジュールされたバックアップを作成するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

手動バックアップはいつでも実行できます。手順については、「[手動バックアップの作成](#create-a-manual-backup)」を参照してください。

## サービスの新しいストレージ アカウントを構成する

これは省略可能な手順で、サービスでストレージ アカウントの自動作成を有効にしていない場合のみ実行する必要があります。StorSimple ボリューム コンテナーを作成するには、Microsoft Azure ストレージ アカウントが必要です。

別のリージョンで Azure のストレージ アカウントを作成する必要がある場合の詳細な手順については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

管理ポータルの **[StorSimple Manager サービス]** ページで次の手順を実行します。

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## PuTTY を使用してデバイスのシリアル コンソールに接続する

StorSimple 用 Windows PowerShell に接続するには、PuTTY などのターミナル エミュレーション ソフトウェアを使用する必要があります。シリアル コンソールから直接デバイスにアクセスするか、またはリモート コンピューターから telnet セッションを開いて PuTTY を使用できます。

[AZURE.INCLUDE [PuTTY を使用してデバイスのシリアル コンソールに接続する](../../includes/storsimple-use-putty.md)]

## Windows Server ホストの IQN を取得する

Windows Server® 2012 を実行する Windows ホストの ISCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

[AZURE.INCLUDE [手動バックアップの作成](../../includes/storsimple-get-iqn.md)]

## 手動バックアップの作成

StorSimple デバイスの 1 つのボリュームに対し、オンデマンドの手動バックアップを作成するには、管理ポータルで次の手順を実行します。

[AZURE.INCLUDE [手動バックアップの作成](../../includes/storsimple-create-manual-backup.md)]

## MPIO を構成する

マルチパス I/O (MPIO) はオプションの機能であり、Windows サーバーに既定ではインストールされていません。サーバー マネージャーを使用して、機能としてインストールする必要があります。

> [AZURE.NOTE]StorSimple 仮想デバイスでは、MPIO がサポートされていません。

MPIO のインストール手順については、[StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)に関するページを参照してください。

## 次のステップ

[仮想デバイス](storsimple-virtual-device.md)を構成します。

[StorSimple Manager サービス](https://msdn.microsoft.com/library/azure/dn772396.aspx)を使用して StorSimple デバイスを管理します。
 

<!---HONumber=August15_HO6-->