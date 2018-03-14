---
title: "Azure Import/Export を使用した Azure Storage との間でのデータの転送 | Microsoft Docs"
description: "Azure Portal でインポートおよびエクスポート ジョブを作成して、Azure Storage との間でデータを転送する方法について説明します。"
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/28/2018
ms.author: muralikk
ms.openlocfilehash: e9fce2530bc4e654304b946cea1715ac8e2ce6fa
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Microsoft Azure Import/Export サービスを使用した Azure Storage へのデータの転送
この記事では、Azure Import/Export サービスを使用してディスク ドライブを Azure データ センターに送付することで、大量のデータを Azure Blob Storage と Azure Files に安全に転送する詳細な手順を説明します。 また、このサービスを使用して、Azure Storage のデータをハード ディスク ドライブに転送し、それらのドライブをオンプレミスのサイトに返送することもできます。 1 台の内蔵 SATA ディスク ドライブのデータを、Azure Blob Storage または Azure Files にインポートできます。 

> [!IMPORTANT] 
> このサービスでは、内蔵 SATA HDD または SSD のみを使用できます。 他のデバイスはサポートされていません。 外付け HDD や NAS デバイスなどは送付しないでください。可能な場合は返却されますが、そうでない場合は廃棄されます。
>
>

ディスク上のデータを Azure Storage にインポートする場合は、次の手順に従います。
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>手順 1: WAImportExport ツールを使用して、1 台または複数のドライブを準備し、1 つまたは複数のジャーナル ファイルを生成する

1.  Azure Storage にインポートするデータを特定します。 インポートできるのは、ローカル サーバーまたはネットワーク共有上のディレクトリやスタンドアロン ファイルです。
2.  データの合計サイズに応じて、2.5 インチ SSD か、2.5 (または 3.5) インチ SATA II または III ハード ディスク ドライブを必要な数だけ調達します。
3.  SATA または外部 USB アダプターを使用して、ハード ドライブを Windows コンピューターに直接接続します。
1.  各ハード ドライブ上に 1 つの NTFS ボリュームを作成し、このボリュームにドライブ文字を割り当てます。 マウントポイントは不要です。
2.  Windows コンピューターで暗号化を有効にするには、NTFS ボリュームで BitLocker の暗号化を有効にします。 https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx の手順に従ってください。
3.  コピーと貼り付けまたはドラッグ アンド ドロップまたは Robocopy または任意のそのようなツールを使用して、ディスク上のこれらの暗号化された 1 つの NTFS ボリュームにデータを完全にコピーします。
7.  https://www.microsoft.com/en-us/download/details.aspx?id=42659 から WAImportExport V1 をダウンロードします。
8.  既定のフォルダー waimportexportv1 に解凍します。 たとえば、C:\WaImportExportV1 などです。  
9.  管理者として実行し、PowerShell またはコマンド ラインを開き、解凍したフォルダーにディレクトリを変更します。 たとえば、cd C:\WaImportExportV1 などです。
10. 次のコマンド ラインをテキスト エディターにコピーし、それを編集して、コマンド ラインを作成します。

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ 
    ```
    
    これらのコマンド ライン オプションについては次の表で説明します。

    |オプション  |[説明]  |
    |---------|---------|
    |/j:     |ジャーナル ファイルの名前 (拡張子は .jrn)。 ジャーナル ファイルはドライブごとに 1 つ生成されます。 ディスクのシリアル番号をジャーナル ファイル名と使用することをお勧めします。         |
    |/sk:     |Azure Storage アカウント キー。         |
    |/t:     |送付するディスクのドライブ文字。 例: ドライブ `D`。         |
    |/bk:     |ドライブの BitLocker キー。         |
    |/srcdir:     |送付するディスクのドライブ文字の末尾に `:\` を付けます。 たとえば、「`D:\`」のように入力します。         |
    |/dstdir:     |Azure Storage 内の保存先コンテナーの名前         |

1. 送付する必要があるディスクごとに手順 10 を繰り返します。
2. /j: パラメーターで指定された名前のジャーナル ファイルは、コマンド ラインの実行のたびに作成されます。

### <a name="step-2-create-an-import-job-on-azure-portal"></a>手順 2: Azure Portal でインポート ジョブを作成する

1. https://portal.azure.com/ にログオンし、[その他のサービス] 、[ストレージ]、[インポート/エクスポート ジョブ] の順に移動して、**[インポート/エクスポート ジョブの作成]** をクリックします。

2. [基本] セクションで、[Azure へインポート] を選択してジョブ名の文字列を入力し、サブスクリプションを選択してリソース グループを入力または選択します。 インポート ジョブのわかりやすい名前を入力します。 名前に含めることができるのは、アルファベットの小文字、数字、ハイフン、アンダースコアだけです。また、先頭の文字はアルファベットにします。スペースを含めることはできません。 ここで入力した名前を使って、ジョブの進行中および完了後にジョブを追跡します。

3. [ジョブの詳細] セクションで、ドライブの準備手順で取得したドライブのジャーナル ファイルをアップロードします。 waimportexport.exe version1 を使った場合は、準備したドライブごとに 1 つのファイルをアップロードする必要があります。 データがインポートされるストレージ アカウントを [インポート先] の [ストレージ アカウント] セクションで選択します。 配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。
   
   ![インポート ジョブの作成 - ステップ 3](./media/storage-import-export-service/import-job-03.png)
4. [差出人住所の詳細] セクションで、運送業者をドロップ ダウン リストから選び、その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/リージョンを指定します。
   
5. [概要] セクションで、Azure DC にディスクを送付するために使用する Azure データセンターの送付先住所を指定します。 ジョブの名前と完全な住所が出荷ラベルに示されていることを確認します。 

6. [概要] ページで [OK] をクリックして、インポート ジョブの作成を完了します。

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>手順 3: 手順 2 で指定した Azure データセンターの送付先住所にドライブを発送する
Azure DC にパッケージを発送するには、FedEx、UPS、または DHL を利用できます。

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>手順 4: 手順 2 で作成したジョブを出荷の追跡番号で更新する
ディスクを発送した後、Azure Portal の **[インポート/エクスポート]** ページに戻り、次の手順を使用して、追跡番号を更新します。a) インポートジョブに移動してクリックし、b) **[ドライブを発送したら、ジョブの状態と追跡情報を更新してください]** をクリックします。 c) [発送済みとしてマークする] チェック ボックスをオンにし、d) [運送業者] と [追跡番号] を指定します。
追跡番号がジョブ作成後 2 週間以内に更新されない場合、ジョブは期限切れになります。 ポータルのダッシュボードでジョブの進行状況を追跡できます。 前のセクションのジョブの各状態の意味については、「 [ジョブの状態の表示](#viewing-your-job-status)」をご覧ください。

## <a name="when-should-i-use-the-azure-importexport-service"></a>Azure Import/Export サービスを使用するシナリオ
ネットワーク経由でデータをアップロードまたはダウンロードすると時間がかかりすぎる場合や、追加のネットワーク帯域幅を取得すると莫大なコストがかかる場合は、Azure Import/Export サービスを使用することを検討します。

このサービスは、次のようなシナリオで使用できます。

* クラウドへのデータの移行: Azure に大量のデータを迅速にコスト効率よく移動します。
* コンテンツ配信: 顧客サイトにデータを迅速に送信します。
* バックアップ: Azure Storage に格納するオンプレミスのデータのバックアップを作成します。
* データの回復: ストレージに格納された大量のデータを回復し、オンプレミスの場所に配信します。

## <a name="prerequisites"></a>前提条件
このセクションでは、このサービスを使うために必要な前提条件を示します。 ドライブを発送する前に、これらの条件を慎重に確認してください。

### <a name="storage-account"></a>ストレージ アカウント
既存の Azure サブスクリプション、または、Import/Export サービスを使用するための 1 つ以上のストレージ アカウントを持っている必要があります。 Azure Import/Export では、クラシック、Blob Storage アカウントおよび汎用目的 v1 ストレージ アカウントのみがサポートされます。 各ジョブを使用できるのは、1 つのストレージ アカウントとの間でのデータ転送だけです。 言い換えると、1 つのインポート/エクスポート ジョブを、複数のストレージ アカウントに対して使用することはできません。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-create-storage-account.md#create-a-storage-account)」を参照してください。

> [!IMPORTANT] 
> Azure Import Export サービスでは、[仮想ネットワークのサービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)機能が有効になっているストレージ アカウントはサポートされません。 
> 
> 

### <a name="data-types"></a>データの種類
Azure Import/Export サービスを使用して、データを**ブロック** BLOB、**ページ** BLOB、あるいは **Files** にコピーできます。 逆に言うと、このサービスを使用して Azure Storage からエクスポートできるのは、**ブロック** BLOB、**ページ** BLOB、**追加** BLOB に限られます。 このサービスは、Azure Files の Azure Storage へのインポートのみをサポートします。 Azure Files のエクスポートは、現在サポートされていません。

### <a name="job"></a>ジョブ
ストレージとの間でインポートまたはエクスポートの処理を開始するには、まず、ジョブを作成します。 ジョブは、"インポート ジョブ" または "エクスポート ジョブ" です。

* オンプレミスのデータを Azure Storage アカウントに転送する場合は、インポート ジョブを作成します。
* ストレージ アカウントに現在格納されているデータを、Microsoft に送付するハード ドライブに移す場合は、エクスポート ジョブを作成します。 ジョブを作成するときは、1 台以上のハード ドライブを Azure データ センターに発送することを Import/Export サービスに通知します。

* インポート ジョブの場合、データが保存されたハード ドライブを送付します。
* エクスポート ジョブの場合、空のハード ドライブを送付します。
* ジョブごとに最大 10 台のハード ディスク ドライブを送付できます。

インポート/エクスポート ジョブは、Azure Portal または [Azure Storage Import/Export REST API](/rest/api/storageimportexport) を使用して作成できます。

> [!Note]
> RDFE API は、2018 年 2 月 28日以降はサポートされなくなります。 サービスを使い続けるには、[ARM Import/Export REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json) に移行してください。 

### <a name="waimportexport-tool"></a>WAImportExport ツール
**インポート** ジョブを作成するときは、まず、インポート用に送付するドライブを準備します。 ドライブを準備するには、ドライブをローカル サーバーに接続し、ローカル サーバーで WAImportExport ツールを実行する必要があります。 この WAImportExport ツールにより、ドライブへのデータのコピー、BitLocker を使用したドライブ上のデータの暗号化、ドライブのジャーナル ファイルの生成が容易になります。

ジャーナル ファイルには、ドライブのシリアル番号やストレージ アカウント名など、ジョブとドライブに関する基本情報が保存されます。 このジャーナル ファイルはドライブには保存されません。 このファイルはインポート ジョブの作成時に使用されます。 ジョブの作成手順の詳細については、この記事で後述します。

WAImportExport ツールは、64 ビット Windows オペレーティング システムとのみ互換性があります。 サポートされる OS バージョンについては、「 [オペレーティング システム](#operating-system) 」をご覧ください。

最新バージョンの [WAImportExport ツール](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip)をダウンロードしてください。 WAImportExport ツールの使用について詳しくは、「[Using the WAImportExport Tool](storage-import-export-tool-how-to.md)」(WAImportExport ツールの使用) をご覧ください。

>[!NOTE]
>**以前のバージョン:** [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) バージョンのツールをダウンロードし、[WAImportExpot V1 の使用ガイド](storage-import-export-tool-how-to-v1.md)を参照できます。 WAImportExpot V1 バージョンのツールでは、**データが既にディスクに事前書き込みされている場合に、ディスクを準備する**機能はサポートされていません。 使用可能なキーが SAS キーだけである場合は、WAImportExpot V1 ツールを使う必要があります。

>

### <a name="hard-disk-drives"></a>ハード ディスク ドライブ
Import/Export サービスで使用できるのは、2.5 インチ SSD か、2.5 (または 3.5) SATA II または III の内部 HDD だけです。 単一の Import/Export ジョブは最大 10 個の HDD や SSD を持つことができ、個々の HDD や SSD はどのようなサイズでも構いません。 多数のドライブを複数のジョブに分散でき、また、作成可能なジョブの数に制限はありません。 

インポート ジョブの場合は、ドライブの最初のデータ ボリュームだけが処理されます。 データ ボリュームは NTFS でフォーマットされている必要があります。

> [!IMPORTANT]
> USB アダプターが組み込まれた外部ハード ディスク ドライブは、このサービスではサポートされていません。 また、外付け HDD のケース内のディスクも使用できません。外付け HDD は送付しないでください。
> 
> 

以下は、内部 HDD にデータをコピーするために使う外部 USB アダプターの一覧です。 Anker 68UPSATAA-02BU Anker 68UPSHHDS-BU Startech SATADOCK22UE Orico 6628SUS3-C-BK (6628 シリーズ) Thermaltake BlacX Hot-Swap SATA 外部ハード ドライブ ドッキング ステーション (USB 2.0 & eSATA)

### <a name="encryption"></a>暗号化
ドライブ上のデータは、BitLocker ドライブ暗号化で暗号化する必要があります。 この暗号化により、移送中にデータが保護されます。

インポート ジョブの場合は、2 つの方法で暗号化できます。 1 つ目の方法は、ドライブの準備時に WAImportExport ツールを実行する際、データセット CSV ファイルを使用するときにオプションを指定する方法です。 もう 1 つは、ドライブで BitLocker 暗号化を手動で有効にし、ドライブの準備時にWAImportExport ツールのコマンド ラインを実行する際、ドライブセット CSV で暗号化キーを指定する方法です。

エクスポート ジョブの場合は、データがドライブにコピーされたドライブが、サービスによって BitLocker で暗号化され、その後、返送されます。 暗号化キーは、Azure Portal を介して提供されます。  

### <a name="operating-system"></a>オペレーティング システム
Azure にドライブを送付する前、WAImportExport ツールを使用してハード ドライブを準備するときに、次の 64 ビット オペレーティング システムのいずれかを使用できます。

Windows 7 Enterprise、Windows 7 Ultimate、Windows 8 Pro、Windows 8 Enterprise、Windows 8.1 Pro、Windows 8.1 Enterprise、Windows 10<sup>1</sup>、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。 このすべてのオペレーティング システムが BitLocker ドライブ暗号化をサポートしています。

### <a name="locations"></a>場所
Azure Import/Export サービスでは、すべてのパブリック Azure Storage アカウントとの間でのデータのコピーをサポートしています。 ハード ディスク ドライブは、一覧のいずれかの場所に送付できます。 ここに示されていないパブリック Azure の場所にストレージ アカウントが存在する場合、Azure Portal または Import/Export REST API を使用してジョブを作成するときに、別の送付先が示されます。

サポートされる送付先は次のとおりです。

* 米国東部
* 米国西部
* 米国東部 2
* 米国西部 2
* 米国中央部
* 米国中北部
* 米国中南部
* 米国中西部
* 北ヨーロッパ
* 西ヨーロッパ
* 東アジア
* 東南アジア
* オーストラリア東部
* オーストラリア南東部
* 西日本
* 東日本
* インド中部
* インド南部
* インド西部
* カナダ中部
* カナダ東部
* ブラジル南部
* 韓国中部
* 米国政府バージニア州
* 米国政府アイオワ州
* US DoD East
* US DoD Central
* 中国 (東部)
* 中国 (北部)
* 英国南部
* ドイツ中部
* ドイツ北東部

### <a name="shipping"></a>発送
**データ センターへのドライブの発送:**

インポート/エクスポート ジョブの作成時に、ドライブの送付先として、サポートされるいずれかの場所の送付先住所が提供されます。 提供される送付先住所はストレージ アカウントの場所によって異なりますが、ストレージ アカウントの場所とは異なる場合があります。

出荷先住所へドライブを発送するには、FedEx、UPS または DHL を利用できます。

**データ センターからのドライブの発送:**

インポート/エクスポート ジョブの作成時に、ジョブの完了後に、Microsoft がドライブを返送するときに使用する返送先住所を指定する必要があります。 処理が遅れないように、必ず有効な返送先住所を指定してください。

輸送業者は、配送過程を適切に追跡して管理する必要があります。 Microsoft がドライブを返送するときに利用する FedEx、UPS、または DHL の有効な運送業者アカウント番号を指定する必要があります。 米国およびヨーロッパの場所からドライブを返送する場合は、FedEx、UPS、または DHL のアカウント番号が必要です。 アジアおよびオーストラリアの場所からドライブを返送する場合は、DHL アカウント番号が必要です。 [FedEx](http://www.fedex.com/us/oadr/) (米国とヨーロッパ) または [DHL](http://www.dhl.com/) (アジアとオーストラリア) の運送業者アカウントがない場合は、アカウントを作成できます。 運送業者アカウント番号を既に持っている場合は、その番号が有効であることを確認してください。

パッケージを発送する際には、 [Microsoft Azure サービス条件](https://azure.microsoft.com/support/legal/services-terms/)の規定に従う必要があります。

> [!IMPORTANT]
> 発送する物理メディアが国境を越える場合があることに注意してください。 お客様は、物理メディアおよびデータが輸入および輸出に関して該当する法律に準拠していることを確認する必要があります。 物理メディアを発送する前に、アドバイザーに依頼して、メディアおよびデータが指定のデータ センターに合法的に発送できることを確認してもらってください。 それにより、Microsoft へのメディアの到着が遅れるのを防ぐことができます。 たとえば、国境を越える (欧州連合域内で国境を越える場合を除く) パッケージには、コマーシャル インボイスを同梱する必要があります。 運送業者の Web サイトで、入力済みコマーシャル インボイスを印刷することもできます。 例として、[DHL のコマーシャル インボイス](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf)や [FedEx のコマーシャル インボイス](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)などがあります。 Microsoft が輸出者として指定されていないことを確認します。
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Azure Import/Export サービスのしくみ
Azure Import/Export サービスを使用して、オンプレミス サイトと Azure Storage 間でデータを転送するには、ジョブを作成し、ハード ディスク ドライブを Azure データ センターに送付します。 送付する各ハード ディスク ドライブは、1 つのジョブに関連付けられます。 各ジョブは、1 つのストレージ アカウントに関連付けられます。 [前提条件のセクション](#pre-requisites)を慎重に見直して、サポートされているデータの種類、ディスクの種類、場所、発送など、このサービスの詳細を確認してください。

このセクションでは、ジョブのインポートとエクスポートに含まれる手順の概要を説明します。 後述の「[クイック スタート](#quick-start)」で、インポート/エクスポート ジョブの作成手順を説明します。

### <a name="inside-an-import-job"></a>インポート ジョブの内部
大まかに言うと、インポート ジョブには次の手順が含まれます。

* インポートするデータと必要なドライブの数を決定します。
* Azure Storage でのデータのインポート先となる BLOB またはファイルの場所を特定します。
* WAImportExport ツールを使用して、1 台以上のハード ディスク ドライブにデータをコピーし、そのドライブを BitLocker で暗号化します。
* Azure Portal または Import/Export REST API を使用して、ターゲット ストレージ アカウントでインポート ジョブを作成します。 Azure Portal を使用している場合は、ドライブのジャーナル ファイルをアップロードします。
* ドライブ返送に使用する返送先住所と運送業者アカウント番号を指定します。
* ジョブの作成時に提供された送付先住所にハード ディスク ドライブを発送します。
* インポート ジョブの詳細で配送問い合わせ番号を更新し、インポート ジョブを送信します。
* ドライブが Azure データ センターに届き、処理されます。
* 運送業者アカウントを使用して、インポート ジョブで提供された返送先住所にドライブが送付されます。
  
    ![図 1: インポート ジョブのフロー](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>エクスポート ジョブの内部
> [!IMPORTANT]
> サービスでは Azure BLOB のエクスポートのみがサポートされており、Azure Files のエクスポートはサポートされていません。
> 
>

大まかに言うと、エクスポート ジョブには次の手順が含まれます。

* エクスポートするデータと必要なドライブの数を決定します。
* BLOB ストレージでデータのソース BLOB またはソース コンテナーのパスを特定します。
* Azure Portal または Import/Export REST API を使用して、ソース ストレージ アカウントでエクスポート ジョブを作成します。
* エクスポート ジョブでデータのソース BLOB またはソース コンテナーのパスを指定します。
* ドライブ返送に使用する返送先住所と運送業者アカウント番号を指定します。
* ジョブの作成時に提供された送付先住所にハード ディスク ドライブを発送します。
* エクスポート ジョブの詳細で配送問い合わせ番号を更新し、エクスポート ジョブを送信します。
* ドライブが Azure データ センターに届き、処理されます。
* ドライブが BitLocker で暗号化され、Azure Portal を介してキーが提供されます。  
* 運送業者アカウントを使用して、インポート ジョブで提供された返送先住所にドライブが送付されます。
  
    ![図 2: エクスポート ジョブのフロー](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>ジョブとドライブの状態の表示
Azure Portal から、インポート ジョブまたはエクスポート ジョブの状態を追跡できます。 **[インポート/エクスポート]** タブをクリックします。ジョブの一覧がページに表示されます。

![ジョブの状態の追跡](./media/storage-import-export-service/jobstate.png)

ドライブの処理状況に応じて、次のジョブの状態のいずれかが表示されます。

| ジョブの状態 | [説明] |
|:--- |:--- |
| 作成 | ジョブが作成されると、その状態は"作成中" に設定されます。 ジョブの状態が "作成中" である間、インポート/エクスポート サービスでは、ドライブがデータ センターにまだ配送されていないと見なされます。 ジョブが "作成中" の状態で維持されるのは最大 2 週間で 、その後はサービスによって自動的に削除されます。 |
| 発送 | パッケージを発送したら、Azure Portal で追跡情報を更新する必要があります。  これにより、ジョブの状態が "発送" に変わります。 ジョブが "発送" の状態で維持されるのは最大 2 週間です。 
| 受取済み | すべてのドライブがデータ センターで受け取られると、ジョブの状態が "受取済み" に設定されます。 |
| 転送 | 少なくとも 1 つのドライブの処理が開始されると、ジョブの状態が "転送" に設定されます。 詳しくは、後述する「ドライブの状態」セクションをご覧ください。 |
| 梱包 | すべてのドライブの処理が完了してから、ドライブがお客様に返送されるまでの間、ジョブの状態は "梱包" に設定されます。 |
| 完了 | すべてのドライブがお客様へ返送され、ジョブがエラーなく完了すると、ジョブの状態が "完了" に設定されます。 "完了" 状態になってから 90 日が経過すると、ジョブは自動的に削除されます。 |
| クローズ | すべてのドライブがお客様へ返送された後、ジョブの処理中に何らかのエラーが発生した場合は、ジョブの状態が "クローズ" に設定されます。 "クローズ" 状態になってから 90 日が経過すると、ジョブは自動的に削除されます。 |

次の表は、各ドライブがインポート ジョブまたはエクスポート ジョブを通じて処理されるまでのライフ サイクルを示しています。 ジョブに含まれる各ドライブの現在の状態は、Azure Portal から表示できるようになりました。
次の表では、ジョブ内の各ドライブの状態がたどる経過について説明しています。

| ドライブの状態 | [説明] |
|:--- |:--- |
| 指定済み | インポート ジョブの場合、Azure Portal からジョブが作成されたときのドライブの初期状態は "指定済み" です。 エクスポート ジョブの場合、ジョブの作成時にドライブは指定されないので、ドライブの初期状態は "受取済み" です。 |
| 受取済み | インポート/エクスポート サービスのオペレーターがインポート ジョブの配送業者から受け取ったドライブを処理すると、ドライブの状態が "受取済み" に変わります。 エクスポート ジョブの場合は、"受取済み" がドライブの初期状態になります。 |
| NeverReceived | ジョブのパッケージが到着したものの、パッケージにドライブが含まれていなかった場合は、ドライブの状態が NeverReceived になります。 また、出荷情報を受信してから 2 週間経過してもパッケージがデータ センターに到着しない場合にも、このドライブ状態が設定されます。 |
| 転送 | ドライブから Windows Azure へのデータ転送が開始されると、ドライブの状態が "転送" に変わります。 |
| 完了 | すべてのデータがエラーなく正常に転送されると、ドライブの状態が "完了" に変わります。
| CompletedMoreInfo | ドライブとストレージ間でのデータ コピー中に何らかの問題が発生した場合は、ドライブの状態が CompletedMoreInfo に変わります。 この情報には、エラー、警告、または BLOB の上書きに関する情報メッセージが含まれる場合があります。
| ShippedBack | データ センターから返送先に向けてドライブが発送されると、ドライブの状態が ShippedBack に変わります。 |

Azure Portal の次の画像では、サンプル ジョブのドライブの状態が表示されています。

![ドライブの状態の表示](./media/storage-import-export-service/drivestate.png)

次の表は、ドライブのエラー状態と、各状態に対して実行されるアクションを示したものです。

| ドライブの状態 | Event | 解決方法/次の手順 |
|:--- |:--- |:--- |
| NeverReceived | NeverReceived としてマークされたドライブ (ジョブの出荷プロセスを通じて受け取られなかったドライブ) は、別便で配送されます。 | ドライブの状態は、オペレーション チームによって "受取済み" に変更されます。 |
| 該当なし | ジョブの対象でないドライブは、別のジョブを通じてデータ センターに配送されます。 | これらのドライブは追加ドライブとしてマークされ、元のパッケージに関連付けられたジョブが完了したときに、お客様に返送されます。 |

### <a name="time-to-process-job"></a>ジョブの処理の所要時間
インポート/エクスポート ジョブの処理にかかる時間は、運送時間、ジョブの種類、コピーするデータの種類とサイズ、用意されたディスクのサイズなど、さまざまな要素によって変わります。 Import/Export サービスに SLA はありませんが、ディスクの到着後 7 日から 10 日以内にコピーが完了するよう努力しております。 REST API を使用すると、ジョブの進行状況を詳細に追跡できます。 ジョブの一覧表示操作には、コピーの進行状況を示す完了率パラメーターがあります。 タイム クリティカルなインポート/エクスポート ジョブの推定所要時間が必要な場合は、Microsoft にお問い合わせください。

### <a name="pricing"></a>価格
**ドライブ処理料金**

インポート/エクスポート ジョブの一環として処理されるドライブごとにドライブ処理料金が発生します。 詳細については、「 [Import/Export の料金](https://azure.microsoft.com/pricing/details/storage-import-export/)」をご覧ください。

**運送費**

ドライブを Azure に発送するときに、運送業者に運送費を支払います。 Microsoft からドライブが返送されるときには、ジョブの作成時に指定した運送業者アカウントに運送費が課金されます。

**トランザクション料金**

データを Azure Storage にインポートするときに、標準のストレージ トランザクション料金の他に、追加のトランザクション料金は必要ありません。 Blob Storage からデータをエクスポートするときは、標準の送信料金が適用されます。 トランザクション料金の詳細については、「 [Data Transfers (データ転送) の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>内蔵 SATA HDD および SSD を使用して Azure File Storage にデータをインポートする方法
ディスク上のデータを Azure File Storage にインポートする場合は、次の手順に従います。
Azure Import/Export サービスを使用してデータをインポートするときは、まず、WAImportExport ツールを使用してドライブを準備します。 次の手順に従ってドライブを準備します。

1. Azure File Storage にインポートするデータを識別します。 これは、ローカル サーバーまたはネットワーク共有上のディレクトリやスタンドアロン ファイルです。  
2. データの合計サイズに応じて、必要なドライブの数を決定します。 2.5 インチ SSD か、2.5 (または 3.5) SATA II または III のハード ディスク ドライブを必要な数だけ調達します。
4. 各ハード ディスク ドライブにコピーするディレクトリやスタンドアロン ファイルを決定します。
5. データセットとドライブセットの CSV ファイルを作成します。
    
  Azure Files としてデータをインポートする場合の、サンプルのデータセット CSV ファイル例を以下に示します。
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   上記の例では、"fileshare" のルートに、100M_1.csv.txt がコピーされます。 "fileshare" が存在しない場合は作成されます。 50M_original の下にあるすべてのファイルとフォルダーが、fileshare に再帰的にコピーされます。 フォルダー構造は維持されます。

    詳しくは、[データセット CSV ファイルの準備](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)に関する記事をご覧ください。
    


    **ドライブセット CSV ファイル**

    ドライブセット フラグの値は、ドライブ文字のマップ先のディスクの一覧を含む CSV ファイルです。これにより、準備するディスクの一覧をツールで正しく選択できます。 

    次に示すのは、ドライブセット CSV ファイルの例です。
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    上記の例では、2 つのディスクが接続され、ボリューム文字 G:\ と H:\ を使用したベーシック NTFS ボリュームが作成されています。 ツールは、H:\ をホストするディスクをフォーマットして暗号化し、ボリューム G:\ をホストしているディスクについては、フォーマットと暗号化を行いません。

    詳しくは、[ドライブセット CSV ファイルの準備](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)に関する記事をご覧ください。

6.  [WAImportExport ツール](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)を使用して、1 台以上のハード ドライブにデータをコピーします。
7.  ドライブセット CSV の Encryption フィールドで "Encrypt" を指定すると、ハード ディスク ドライブでの BitLocker 暗号化を有効にすることができます。 また、ハード ディスク ドライブで BitLocker 暗号化を手動で有効にし、ツールの実行時にドライブセット CSV で "AlreadyEncrypted" を指定し、キーを指定することもできます。

8. ディスクの準備が完了した後に、ハード ディスク ドライブのデータやジャーナル ファイルを変更しないでください。

> [!IMPORTANT]
> 準備したハード ディスク ドライブごとに 1 つのジャーナル ファイルが生成されます。 Azure Portal を使用してインポート ジョブを作成するときに、そのインポート ジョブに含まれるドライブのすべてのジャーナル ファイルをアップロードする必要があります。 ジャーナル ファイルのないドライブは処理されません。
> 
>

WAImportExport ツールを使用してハード ディスク ドライブを準備する際のコマンドと例を以下に示します。

WAImportExport ツールの PrepImport コマンド (最初のコピー セッションで、新規のコピー セッションを使用してディレクトリやファイルをコピーします):

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**インポート例 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

**ドライブを追加する**には、新しいドライブセット ファイルを作成し、次に示すようにコマンドを実行します。 InitialDriveset .csv ファイルで指定した以外のディスク ドライブに対する後続のコピー セッションについては、新しいドライブセット CSV ファイルを指定し、それを "AdditionalDriveSet" パラメーターの値として入力します。 **同じジャーナル ファイル**名を使用し、**新しいセッション ID** を指定します。 AdditionalDriveset CSV ファイルの形式は、InitialDriveSet の形式と同じです。

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**インポート例 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

同じドライブセットにデータを追加するには、後続のコピー セッションに対して WAImportExport ツールの PrepImport コマンドを呼び出し、追加のファイルやディレクトリをコピーします。InitialDriveset .csv ファイルで指定されたのと同じハード ディスク ドライブに対する後続のコピー セッションについては、**同じジャーナル ファイル**名を指定し、**新しいセッション ID** を指定します。ストレージ アカウント キーを入力する必要はありません。

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**インポート例 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

WAImportExport ツールの使用方法の詳細については、[インポート用のハード ドライブの準備](storage-import-export-tool-preparing-hard-drives-import.md)に関する記事をご覧ください。

また、手順の詳細については、「[インポート ジョブ用のハード ドライブを準備するためのサンプル ワークフロー](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)」をご覧ください。  



## <a name="create-an-export-job"></a>エクスポート ジョブの作成
エクスポート ジョブを作成することで、1 台以上の空のドライブをデータ センターに発送すること、そこでストレージ アカウントからそのドライブにデータをエクスポートすること、その後そのドライブを返送してもらうことを、Import/Export サービスに通知します。

### <a name="prepare-your-drives"></a>ドライブの準備
エクスポート ジョブのドライブを準備する際には、次の事前チェックを行うことをお勧めします。

1. WAImportExport ツールの PreviewExport コマンドを使用して、必要なディスクの数を確認します。 詳細については、「 [Previewing Drive Usage for an Export Job (エクスポート ジョブのドライブ使用率のプレビュー)](https://msdn.microsoft.com/library/azure/dn722414.aspx)」をご覧ください。 使用するドライブのサイズに基づいて、選択した BLOB に対するドライブの使用率をプレビューするのに役立ちます。
2. エクスポート ジョブ用に送付するハード ドライブの読み取り/書き込みが可能であることを確認します。

### <a name="create-the-export-job"></a>エクスポート ジョブの作成
1. エクスポート ジョブを作成するには、Azure Portal で [その他のサービス]-> [ストレージ] -> [インポート/エクスポート ジョブ] に移動します。 **[インポート/エクスポート ジョブの作成]** をクリックします。
2. ステップ 1. の [基本] で、[Azure からエクスポート] を選択してジョブ名の文字列を入力し、サブスクリプションを選択してリソース グループを入力または選択します。 インポート ジョブのわかりやすい名前を入力します。 名前に含めることができるのは、アルファベットの小文字、数字、ハイフン、アンダースコアだけです。また、先頭の文字はアルファベットにします。スペースを含めることはできません。 ここで入力した名前を使用して、ジョブの進行中および完了後にジョブを追跡します。 このエクスポート ジョブの担当者の連絡先情報を入力します。 

3. ステップ 2. の [ジョブの詳細] では、データのエクスポート元のストレージ アカウントを [ストレージ アカウント] セクションで選択します。 配送場所は、選択したストレージ アカウントのリージョンに基づいて自動的に入力されます。 ストレージ アカウントから空のドライブにエクスポートする BLOB データを指定します。 ストレージ アカウントの BLOB データをすべてエクスポートすることも、エクスポートする BLOB データまたはそのセットを指定することもできます。
   
   エクスポートする BLOB を指定するには、 **[等しい]** セレクターを使用し、コンテナー名で始まる、BLOB への相対パスを指定します。 ルート コンテナーを指定するには、" *$root* " を使用します。
   
   プレフィックスで始まるすべての BLOB を指定するには、 **[指定値で始まる]** セレクターを使用し、スラッシュ "/" で始まるプレフィックスを指定します。 プレフィックスには、コンテナー名のプレフィックス、コンテナー名全体、またはコンテナー名全体の後に BLOB 名のプレフィックスを付けた名前を指定できます。
   
   次の表は、有効な BLOB のパスの例を示しています。
   
   | セレクター | BLOB パス | [説明] |
   | --- | --- | --- |
   | [指定値で始まる] |/ |ストレージ アカウントのすべての BLOB をエクスポートする |
   | [指定値で始まる] |/$root/ |ルート コンテナー内のすべての BLOB をエクスポートする |
   | [指定値で始まる] |/book |プレフィックス " **book** |
   | [指定値で始まる] |/music/ |コンテナー " **music** |
   | [指定値で始まる] |/music/love |コンテナー "**music**" 内の、プレフィックス "**love**" で始まるすべての BLOB をエクスポートする |
   | [等しい] |$root/logo.bmp |ルート コンテナー内の BLOB " **logo.bmp** " をエクスポートする |
   | [等しい] |videos/story.mp4 |コンテナー "**videos**" 内の BLOB "**story.mp4**" をエクスポートする |
   
   次のスクリーンショットで示すように、処理中にエラーが発生しないように、有効な形式で BLOB のパスを指定する必要があります。
   
   ![エクスポート ジョブの作成 - ステップ 3](./media/storage-import-export-service/export-job-03.png)

4. ステップ 3. の [差出人住所の詳細] では、運送業者をドロップ ダウン リストから選び、その運送業者で作成した有効な運送業者アカウント番号を入力します。 マイクロソフトでは、インポート ジョブの完了後、このアカウントを使用してドライブを返送します。 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/リージョンを指定します。
   
 5. [概要] ページで、Azure DC にディスクを送付するために使用する Azure データセンターの送付先住所を指定します。 ジョブの名前と完全な住所が出荷ラベルに示されていることを確認します。 

6. [概要] ページで [OK] をクリックして、インポート ジョブの作成を完了します

7. ディスクを発送した後、Azure Portal の **[インポート/エクスポート]** ページに戻り、a) インポートジョブに移動してクリックし、b) **[ドライブを発送したら、ジョブの状態と追跡情報を更新してください。]** をクリックします。 
     c) [発送済みとしてマークする] チェック ボックスをオンにし、d) [運送業者] と [追跡番号] を指定します。
    
   追跡番号がジョブ作成後 2 週間以内に更新されない場合、ジョブは期限切れになります。
   
8. ポータルのダッシュボードでジョブの進行状況を追跡できます。 前のセクションのジョブの各状態の意味については、「 [ジョブの状態の表示](#viewing-your-job-status)」をご覧ください。

   > [!NOTE]
   > エクスポート対象の BLOB がハード ドライブへのコピー時に使用中の場合は、Azure Import/Export サービスは BLOB のスナップショットを作成してスナップショットをコピーします。
   > 
   > 
 
9. エクスポートされたデータが含まれたドライブを受け取ったら、サービスによって生成されたドライブの BitLocker キーを表示し、コピーできます。 Azure Portal でエクスポート ジョブに移動し、[インポート/エクスポート] タブをクリックします。一覧からエクスポート ジョブを選択し、[BitLocker キー] オプションをクリックします。 BitLocker キーが次のように表示されます。
   
   ![エクスポート ジョブの BitLocker キーの表示](./media/storage-import-export-service/export-job-bitlocker-keys.png)

このサービスを使用しているときに生じる最も一般的な質問については、次の FAQ セクションをご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Azure Import/Export サービスを使用して Azure File Storage をコピーすることはできますか。**

はい。Azure Import/Export サービスでは Azure File Storage へのインポートがサポートされます。 現時点では、Azure Files のエクスポートはサポートされていません。

**CSP サブスクリプションで Azure Import/Export サービスを使用できますか。**

Azure Import/Export サービスは、CSP サブスクリプションをサポートしています。

**インポート ジョブのドライブの準備手順を省略することはできますか。あるいは、コピーせずにドライブを準備することはできますか。**

データをインポートするために送付するドライブは、Azure WAImportExport ツールを使用して準備する必要があります。 WAImportExport ツールを使用して、データをドライブにコピーする必要があります。

**エクスポート ジョブを作成するときに、ディスクの準備作業を実行する必要はありますか。**

いいえ。ただし、いくつかの事前チェックをお勧めします。 WAImportExport ツールの PreviewExport コマンドを使用して、必要なディスクの数を確認します。 詳細については、「 [Previewing Drive Usage for an Export Job (エクスポート ジョブのドライブ使用率のプレビュー)](https://msdn.microsoft.com/library/azure/dn722414.aspx)」をご覧ください。 使用するドライブのサイズに基づいて、選択した BLOB に対するドライブの使用率をプレビューするのに役立ちます。 また、エクスポート ジョブに対して発送されるハード ドライブを読み書きできることを確認します。

**サポートされる要件に適合しない HDD を誤って発送した場合は、どうなりますか。**

Azure データ センターでは、サポートされる要件に適合しないドライブは返送されます。 サポートされる要件を満たすのが、パッケージ内の一部のドライブだけである場合は、それらのドライブが処理され、要件を満たさないドライブは返送されます。

**ジョブを取り消すことはできますか。**

ジョブの状態が "作成" または "発送" のときは、ジョブを取り消すことができます。

**Azure Portal では、どのくらいの期間、完了したジョブの状態を確認できますか。**

完了したジョブの状態は、最大 90 日間、表示されます。 90 日が経過すると、完了したジョブは削除されます。

**10 台を超えるドライブでインポートまたはエクスポートするには、どうすればよいでしょうか。**

Import/Export サービスで、1 つのインポート ジョブまたはエクスポート ジョブが参照できるのは、10 台のドライブまでです。 10 台を超えるドライブを発送する必要がある場合は、複数のジョブを作成できます。 同じジョブに関連付けられているドライブは、同じパッケージにまとめて発送する必要があります。
マイクロソフトは、データ キャパシティが複数のディスクのインポート ジョブにわたる際のガイダンスとサポートを提供しています。 詳しくは、bulkimport@microsoft.com にお問い合わせください。

**ドライブは返送前にフォーマットされるのですか。**


いいえ。 ドライブはすべて BitLocker で暗号化されます。

**インポート/エクスポート ジョブのドライブを Microsoft から購入できますか。**


いいえ。 インポート ジョブとエクスポート ジョブの両方で、自分のドライブを発送する必要があります。

** このサービスでインポートしたデータにアクセスする方法**

Azure ストレージ アカウント内のデータには、Azure Portal から、またはストレージ エクスプローラーと呼ばれるスタンドアロン ツールを使用してアクセスできます。 https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**インポート ジョブの完了後、ストレージ アカウント内でデータはどのような状態になりますか。ディレクトリ階層は保持されますか。**

インポート ジョブのハード ドライブを準備するときに、データセット CSV の DstBlobPathOrPrefix フィールドでコピー先が指定されます。 これは、ハード ドライブのデータのコピー先となるストレージ アカウント内のコピー先コンテナーです。 このコピー先コンテナー内に、ハード ドライブのフォルダーの仮想ディレクトリが作成され、ファイルの BLOB が作成されます。 

**ストレージ アカウント内に既に存在するファイルがドライブに含まれている場合、ストレージ アカウント内の既存の BLOB またはファイルは上書きされますか。**

ドライブを準備するときに、データセット CSV の /Disposition:<rename|no-overwrite|overwrite> というフィールドを使用して、コピー先のファイルを上書きするか無視するかを指定できます。 既定では、既存の BLOB またはファイルは上書きされず、新しいファイルの名前が変更されます。

**WAImportExport ツールは、32 ビット オペレーティング システムと互換性がありますか。**

いいえ。 WAImportExport ツールは、64 ビット Windows オペレーティング システムとのみ互換性があります。 サポートされる OS バージョンの一覧については、「 [前提条件](#pre-requisites) 」の「オペレーティング システム」セクションをご覧ください。

**ハード ディスク ドライブ以外のものをパッケージに含める必要はありますか。**

ハード ドライブのみを発送してください。 電源ケーブルや USB ケーブルなどは同梱しないでください。

**FedEx や DHL を利用してドライブを発送する必要がありますか。**

FedEx、DHL、UPS などの既知の運送業者や、アメリカ郵政公社を利用してデータ センターにドライブを発送できます。 ただし、データ センターからドライブを返送するために、米国および EU リージョンでは FedEx アカウント番号を、アジアおよびオーストラリア リージョンでは DHL アカウント番号を指定する必要があります。

**ドライブを海外発送する場合、何か制限はありますか。**

発送する物理メディアが国境を越える場合があることに注意してください。 お客様は、物理メディアおよびデータが輸入および輸出に関して該当する法律に準拠していることを確認する必要があります。 物理メディアを発送する前に、アドバイザーに依頼して、メディアおよびデータが指定のデータ センターに合法的に発送できることを確認してもらってください。 それにより、マイクロソフトへのメディアの到着が遅れるのを防ぐことができます。

**ジョブの作成時に、ストレージ アカウントの場所とは異なる場所が送付先住所として提供されました。どうすればよいですか。**

一部のストレージ アカウントの場所は、別の送付先にマップされています。 以前に利用できた送付先が、一時的に別の場所にマップされている場合もあります。 ドライブを発送する前に、ジョブの作成時に提供された送付先住所を必ず確認してください。

**ドライブを発送するときに、運送業者からデータ センターの連絡先の住所と電話番号を求められました。何を提供すればよいですか。**

電話番号と DC の住所は、ジョブの作成の一環として提供されます。

**Azure Import/Export サービスを使用して、PST メールボックスと SharePoint データを O365 にコピーすることはできますか。**

[Office 365 への PST ファイルまたは SharePoint データのインポート](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)に関する記事をご覧ください。

**Azure Import/Export サービスを使用して、Azure Backup サービスにバックアップをオフラインでコピーすることはできますか。**

「 [Azure Backup でのオフライン バックアップのワークフロー](../../backup/backup-azure-backup-import-export.md)」をご覧ください。

**一度に発送できる HDD は最大で何個ですか。**

HDD は一度の発送で何個でも送ることができます。ディスクが複数のジョブに属している場合は、a) 対応するジョブ名でディスクをラベル付けする、 b) -1、-2 などのサフィックスが付いた追跡番号でジョブを更新することをお勧めします。
  
**Disk Import/Export でサポートされるブロック BLOB とページ BLOB の最大サイズはいくつですか?**

ブロック BLOB の最大サイズは、約 4.768 TB または 5,000,000 MB です。
ページ BLOB の最大サイズは 1 TB です。

**Disk Import/Export は AES 256 暗号化をサポートしていますか。**

Azure Import/Export サービスは、既定では AES 128 bitlocker で暗号化されますが、データをコピーする前に手動で bitlocker による暗号化を実行すれば、AES 256 に高めることができます。 

[WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) を使用している場合のサンプル コマンドは次のとおりです
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
[WAImportExport ツール](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip)を使用している場合は、"AlreadyEncrypted" を指定し、driveset CSV 内にキーを指定します。
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>次の手順

* [Setting up the WAImportExport tool](storage-import-export-tool-how-to.md) (WAImportExport ツールの設定)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
* [Azure Import Export REST API サンプル](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

