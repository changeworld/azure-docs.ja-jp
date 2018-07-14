---
title: Azure Import/Export を使用した Azure Storage との間でのデータの転送 | Microsoft Docs
description: Azure Portal でインポートおよびエクスポート ジョブを作成して、Azure Storage との間でデータを転送する方法について説明します。
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 83ba437e699eb150e86e6c89e478377394966419
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232679"
---
# <a name="what-is-azure-importexport-service"></a>Azure Import/Export サービスとは

Azure Import/Export サービスでは、Azure データセンターにディスク ドライブを送付することで、Azure Blob Storage と Azure Files に大量のデータを安全にインポートできます。 また、このサービスでは、Azure Blob Storage からディスク ドライブにデータを転送し、オンプレミスのサイトに送付できます。 1 つまたは複数のディスクからのデータを Azure Blob Storage か Azure Files にインポートできます。 

## <a name="azure-importexport-usecases"></a>Azure Import/Export ユースケース

ネットワーク経由でデータをアップロードまたはダウンロードすると時間がかかりすぎる場合や、追加のネットワーク帯域幅を取得すると莫大なコストがかかる場合は、Azure Import/Export サービスを使用することを検討します。 次のシナリオでこのサービスを使用します。

* **クラウドへのデータの移行**: Azure に大量のデータを迅速にコスト効率よく移動します。
* **コンテンツ配信**: 顧客サイトにデータを迅速に送信します。
* **バックアップ**: Azure Storage に格納するオンプレミスのデータのバックアップを作成します。
* **データの回復**: ストレージに格納された大量のデータを回復し、オンプレミスの場所に配信します。

## <a name="importexport-components"></a>Import/Export コンポーネント

Import/Export サービスでは、次のコンポーネントが使用されます。

- **Import/Export** サービス: Azure Portal で利用できるこのサービスでは、ユーザーはインポート/エクスポート ジョブを作成したり、追跡したりできます。  

- **WAImportExport ツール**: これは次を行うコマンドライン ツールです。 
    - インポートのためにドライブの配送準備をします。
    - データをドライブにコピーする作業を容易にします。
    - BitLocker でドライブ上のデータを暗号化します。
    - インポート作成中に使用されるドライブのジャーナル ファイルを生成します。
    - エクスポート ジョブに必要なドライブの数を特定します。

    このツールには 2 つのバージョン、バージョン 1 とバージョン 2 があります。 次の使い分けをお勧めします。

    - Azure Blob Storage との間でインポート/エクスポートする場合、バージョン 1。 
    - Azure Files にデータをインポートする場合、バージョン 2。

    WAImportExport ツールは、64 ビット Windows オペレーティング システムとのみ互換性があります。 サポートされている特定の OS バージョンについては、[Azure Import/Export の要件](storage-import-export-requirements.md#supported-operating-systems)に関するページを参照してください。

- **ディスク**: ソリッド ステート ドライブ (SSD) またはハード ディスク ドライブ (HDD) を Azure データセンターに送付できます。 インポート ジョブを作成するときは、データが含まれるディスク ドライブを送付します。 エクスポート ジョブを作成するときは、空のドライブを Azure データセンターに送付します。 具体的なディスクの種類については、[サポートされるディスクの種類](storage-import-export-requirements.md#supported-hardware)を参照してください。

## <a name="how-does-importexport-work"></a>Import/Export のしくみは?

Azure Import/Export サービスでは、ジョブを作成することで、Azure Blobs と Azure Files にデータを転送できます。 Azure Portal または Azure Resource Manager REST API を使用し、ジョブを作成します。 各ジョブは、1 つのストレージ アカウントに関連付けられます。 

ジョブにはインポート ジョブとエクスポート ジョブがあります。 インポート ジョブでは、Azure Blobs または Azure Files にデータをインポートできます。エクスポート ジョブでは、Azure Blobs からデータをエクスポートできます。 インポート ジョブの場合、データが含まれるディスク ドライブを送付します。 エクスポート ジョブを作成するときは、空のドライブを Azure データセンターに送付します。 いずれの場合でも、ジョブごとに最大 10 台のディスク ドライブを送付できます。

> [!IMPORTANT]
> Azure Files にはデータをエクスポートできません。

このセクションでは、ジョブのインポートとエクスポートに含まれる手順の概要を説明します。 


### <a name="inside-an-import-job"></a>インポート ジョブの内部

大まかに言うと、インポート ジョブには次の手順が含まれます。

1. インポートするデータ、必要なドライブの数、Azure Storage でインポート先となる Blob の場所を決定します。
2. WAImportExport ツールを使用し、データをディスク ドライブにコピーします。 BitLocker でディスクを暗号化します。
3. Azure Portal のターゲット ストレージ アカウントでインポート ジョブを作成します。 ドライブのジャーナル ファイルをアップロードします。
2. ドライブの返送先となる住所と運送業者アカウント番号を指定します。
3. ジョブの作成時に提供された送付先住所にディスク ドライブを発送します。
4. インポート ジョブの詳細で配送問い合わせ番号を更新し、インポート ジョブを送信します。
5. ドライブが Azure データ センターに届き、処理されます。
6. 運送業者アカウントを使用して、インポート ジョブで提供された返送先住所にドライブが送付されます。
  
    ![図 1: インポート ジョブのフロー](./media/storage-import-export-service/importjob.png)

データの段階的インポート方法については、次にお進みください。

- [Azure Blobs へのデータのインポート](storage-import-export-data-to-blobs.md)
- [Azure Files へのデータのインポート](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>エクスポート ジョブの内部

> [!IMPORTANT]
> このサービスでは、Azure Blobs のエクスポートのみがサポートされます。 Azure Files のエクスポートはサポートされていません。

大まかに言うと、エクスポート ジョブには次の手順が含まれます。

1. エクスポートするデータ、必要なドライブの数、Blob ストレージのデータのソース Blob またはコンテナー パスを決定します。
3. Azure Portal で、ソース ストレージ アカウントでエクスポート ジョブを作成します。
4. エクスポートするデータのソース Blob またはコンテナー パスを指定します。
5. ドライブの返送先となる住所と運送業者アカウント番号を指定します。
6. ジョブの作成時に提供された送付先住所にディスク ドライブを発送します。
7. エクスポート ジョブの詳細で配送問い合わせ番号を更新し、エクスポート ジョブを送信します。
8. ドライブが Azure データ センターに届き、処理されます。
9. ドライブが BitLocker で暗号化され、Azure Portal を介してキーが提供されます。  
10. 運送業者アカウントを使用して、インポート ジョブで提供された返送先住所にドライブが送付されます。
  
    ![図 2: エクスポート ジョブのフロー](./media/storage-import-export-service/exportjob.png)

データ エクスポートの段階的な手順については、[Azure Blobs からデータをエクスポートする](storage-import-export-data-from-blobs.md)方法に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン 

Azure Import/Export サービスでは、すべての Azure Storage アカウントとの間でのデータのコピーをサポートしています。 ディスク ドライブは、一覧のいずれかの場所に送付できます。 ここで指定されていない Azure の場所にストレージ アカウントがある場合、ジョブの作成時、代替送付場所が与えられます。

### <a name="supported-shipping-locations"></a>サポートされる送付先


|Country  |Country  |Country  |Country  |
|---------|---------|---------|---------|
|米国東部    | 北ヨーロッパ        | インド中部        |米国政府アイオワ州         |
|米国西部     |西ヨーロッパ         | インド南部        | US DoD East        |
|米国東部 2    | 東アジア        |  インド西部        | US DoD Central        |
|米国西部 2     | 東南アジア        | カナダ中部        | 中国 (東部)         |
|米国中央部     | オーストラリア東部        | カナダ東部        | 中国 (北部)        |
|米国中北部     |  オーストラリア南東部       | ブラジル南部        | 英国南部        |
|米国中南部     | 西日本        |韓国中部         | ドイツ中部        |
|米国中西部     |  東日本       | 米国政府バージニア州        | ドイツ北東部        |


## <a name="security-considerations"></a>セキュリティに関する考慮事項

ドライブ上のデータは、BitLocker ドライブ暗号化で暗号化されます。 この暗号化により、移送中にデータが保護されます。

インポート ジョブの場合、ドライブは 2 つの方法で暗号化されます。  


- ドライブの準備時に WAImportExport ツールを実行する際、*dataset.csv* ファイルを使用するときにオプションを指定する方法です。 

- ドライブの BitLocker 暗号化を手動で有効にします。 ドライブの準備中、WAImportExport ツール コマンド ラインを実行するとき、*driveset.csv* に暗号化キーを指定します。


エクスポート ジョブの場合、データがドライブにコピーされたドライブがこのサービスによって BitLocker で暗号化され、その後、返送されます。 暗号化キーは、Azure Portal を介して提供されます。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>価格

**ドライブ処理料金**

インポート/エクスポート ジョブの一環として処理されるドライブごとにドライブ処理料金が発生します。 詳細については、「 [Import/Export の料金](https://azure.microsoft.com/pricing/details/storage-import-export/)」をご覧ください。

**運送費**

ドライブを Azure に発送するときに、運送業者に運送費を支払います。 Microsoft からドライブが返送されるときには、ジョブの作成時に指定した運送業者アカウントに運送費が課金されます。

**トランザクション料金**

データを Azure Storage にインポートするときに、標準のストレージ トランザクション料金の他に、追加のトランザクション料金は必要ありません。 Blob Storage からデータをエクスポートするときは、標準の送信料金が適用されます。 トランザクション料金の詳細については、[データ転送の価格](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページを参照してください。



## <a name="next-steps"></a>次の手順

次の目的で Import/Export サービスを使用する方法について学習してください。
* [Azure Blobs にデータをインポートする](storage-import-export-data-to-blobs.md)
* [Azure Blobs からデータをエクスポートする](storage-import-export-data-from-blobs.md)
* [Azure Files にデータをインポートする](storage-import-export-data-to-files.md)

