---
title: Microsoft Azure Data Box Disk のクイック スタート | Microsoft Docs
description: このクイック スタートを使用して、Azure portal からすばやく Azure Data Box Disk をデプロイしましょう。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 20dc414c5cdd309434ba53acf2d7f6716d3edfe5
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009928"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>クイック スタート: Azure portal を使用して Azure Data Box Disk をデプロイする (プレビュー)

このクイック スタートでは、Azure portal を使用して Azure Data Box Disk をデプロイする方法について説明します。 ここでは、すばやく注文を作成する手順や、ディスクを受け取る手順、開梱の手順、接続手順のほか、Azure にアップロードできるようデータをディスクにコピーする手順などについて取り上げています。 

デプロイと追跡の手順をステップ バイ ステップで詳しく紹介した記事については、[Azure Data Box Disk の注文に関するチュートリアル](data-box-disk-deploy-ordered.md)を参照してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F)を作成してください。

> [!IMPORTANT]
> Data Box Disk はプレビュー段階にあります。 このソリューションを展開する前に、[プレビューに関する Azure のサービス利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

- ご利用のサブスクリプションで Azure Data Box サービスが有効になっていることを確認してください。 ご利用のサブスクリプションでこのサービスを有効にするには、[サービスにサインアップ](http://aka.ms/azuredataboxfromdiskdocs)してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([http://aka.ms/azuredataboxfromdiskdocs](http://aka.ms/azuredataboxfromdiskdocs)) にサインインします。

## <a name="order"></a>順序

この手順には約 5 分かかります。

1. Azure portal で新しい Azure Data Box リソースを作成します。 
2. このサービスが有効になったサブスクリプションを選択し、転送の種類として **[インポート]** を選択します。 **[ソースの国]** にデータが存在する場所を、**[宛先 Azure リージョン]** にデータの転送先を指定します。
3. **[Data Box Disk]** を選択します。 ソリューションの最大容量は 35 TB ですが、それを超えるサイズのデータについては複数のディスク注文を作成することができます。  
4. 注文の詳細と発送情報を入力します。 ご利用のリージョンでこのサービスが提供されている場合、通知メール アドレスを指定し、概要を確認したうえで注文を作成します。 

注文が作成されると、ディスクの発送準備が行われます。 


## <a name="unpack"></a>開梱

この手順には約 5 分かかります。

Data Box Disk は、UPS Express Box で郵送されます。 開梱して同梱物をチェックしてください。

- エアークッションで包まれた USB ディスク (1 台から 5 台)。
- 接続ケーブル (ディスクごと)。 
- 返送用の配送ラベル。
 

## <a name="connect-and-unlock"></a>接続とロック解除

この手順には約 5 分かかります。

1. 同梱されているケーブルを使用して、サポート対象バージョンの Windows が実行されているコンピューターにディスクを接続します。 サポート対象 OS バージョンについて詳しくは、「[Azure Data Box Disk system requirements (Azure Data Box Disk のシステム要件)](data-box-disk-system-requirements.md)」を参照してください。 
2. 次の手順に従って、ディスクのロックを解除します。

    1. Azure portal で **[全般] > [デバイスの詳細]** に移動し、パスキーを取得します。
    2. ディスクへのデータのコピーに使用するコンピューターに、Data Box Disk のロック解除ツールをダウンロードして抽出します。 
    3. *DataBoxDiskUnlock.exe* を実行してパスキーを指定します。 すべてのディスクを接続し直しながら、この手順を繰り返します。
    4. ディスクに割り当てられたドライブ文字が、ツールによって表示されます。 ディスクのドライブ文字をメモしておいてください。 以降の手順で使用します。



## <a name="copy-data-and-verify"></a>データのコピーと確認

この工程にかかる時間は、実際のデータのサイズによって異なります。 

1. ドライブには、*PageBlob*、*BlockBlob*、*AzureImportExport* の各フォルダーが格納されています。 ブロック BLOB としてインポートするデータは、*BlockBlob* フォルダーにドラッグ アンド ドロップでコピーします。 同様に、VHD/VHDX などのデータは、*PageBlob* フォルダーにドラッグ アンド ドロップします。

    Azure Storage アカウントには、*BlockBlob* フォルダー下および *PageBlob* フォルダー下のサブフォルダーごとにコンテナーが 1 つ作成されます。 *BlockBlob* フォルダー下のファイルと *PageBlob* フォルダー下のファイルはすべて、Azure Storage アカウントの既定のコンテナー `$root` にコピーされます。

    > [!NOTE] 
    > - すべてのコンテナーと BLOB は、[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions)に準拠している必要があります。 これらの規則に従っていない場合、Azure へのデータのアップロードに失敗します。
    > - ファイルが約 4.7 TiB (ブロック BLOB の場合) および約 8 TiB (ページ BLOB の場合) を超えないようにしてください。

2. (省略可) コピーが完了したら、*AzureImportExport* フォルダーに格納されている `AzureExpressDiskService.ps1` を実行して、検証のためにチェックサムを生成することをお勧めします。 データのサイズによっては、この手順に時間がかかることがあります。 
3. ドライブを取り外します。 


## <a name="ship-to-azure"></a>Azure への発送

この手順の所要時間は 5 分から 7 分程度です。

1. すべてのディスクを元のパッケージに梱包します。 同梱されている配送先住所ラベルを使用してください。 ラベルを破損または紛失した場合は、ポータルからダウンロードしてください。 **[概要]** に移動し、コマンド バーの **[出荷ラベルをダウンロード]** をクリックします。
2. パッケージに封をし、集荷場所に持ち込みます。  

Data Box Disk サービスからメール通知が送信され、Azure portal で注文の状態が更新されます。


## <a name="verify-your-data"></a>データの検証

この工程にかかる時間は、実際のデータのサイズによって異なります。

1. Data Box ディスクが Azure データセンターのネットワークに接続されると、Azure へのデータのアップロードが自動的に開始されます。 
2. Azure Data Box サービスは、データのコピーが完了したことを Azure portal 経由でお客様に通知します。 
    
    1. 失敗していないかエラー ログで確認し、適切な措置を講じます。
    2. コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

この手順を完了するには、2 分から 3 分かかります。

クリーンアップするには、Data Box の注文をキャンセルしたうえで削除してください。

- Data Box の注文は、注文が処理される前であれば、Azure portal からキャンセルできます。 注文が処理された後は、キャンセルできません。 注文は、完了ステージに到達するまで続行されます。 

    注文をキャンセルするには、**[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。  

- Azure portal で **完了済み**または**キャンセル済み**の状態になった注文は削除することができます。 

    注文を削除するには、**[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-step"></a>次のステップ

このクイック スタートでは、Azure へのデータのインポートを支援する Azure Data Box Disk をデプロイしました。 Azure Data Box Disk の管理について詳しくは、次のチュートリアルをご覧ください。 

> [!div class="nextstepaction"]
> [Azure portal を使用して Data Box Disk を管理する](data-box-portal-ui-admin.md)


