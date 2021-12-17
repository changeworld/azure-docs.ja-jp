---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: a46552639b9cdea135216e544ec4c51f4d9dda3d
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736256"
---
Microsoft がディスクを受け取ってスキャンすると、注文の状態は "**受取済み**" に更新されます。 その後、デバイスの損傷または改ざんの印がないか物理的に検証されます。

検証が完了すると、Data Box は Azure データセンターのネットワークに接続されます。 データのコピーが自動的に開始されます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

### <a name="review-copy-errors-from-upload"></a>アップロードで発生したコピー エラーを確認する

再試行できないエラーのためにファイルをアップロードできない場合は、続行する前にエラーを確認するように通知されます。 エラーはデータ コピー ログに一覧表示されます。

これらのエラーは修正できません。 アップロードは完了しましたが、エラーが発生しました。 この通知は、ネットワーク転送または新しいインポート注文を使用して別のアップロードを実行する前に修正する必要がある構成の問題について通知しています。 ガイダンスについては、「[Azure Data Box および Azure Data Box Heavy デバイスからのアップロードのコピー エラーを確認する](../articles/databox/data-box-troubleshoot-data-upload.md)」を参照してください。

エラーを確認し、続行する準備ができたことを確認して応答すると、データがデバイスから安全に消去されます。 注文は 14 日後に自動的に完了します。 通知に応答することで、より迅速に進めることができます。

[!INCLUDE [data-box-review-nonretryable-errors](data-box-review-nonretryable-errors.md)]


### <a name="verify-data-in-completed-upload"></a>アップロードの完了後にデータを検証する

データのアップロードが完了すると、注文の状態が **[完了]** に更新されます。

コピー元からデータを削除する前に、データが Azure にアップロードされていることを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータが Azure Storage アカウントの次のいずれかのパスにアップロードされます。

  - ブロック BLOB およびページ BLOB の場合: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files の場合: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    あるいは、Azure Portal で Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。 

    - Azure のマネージド ディスクへのコピーが正常に完了した場合は、Azure portal の **[注文の詳細]** に移動して、マネージド ディスクに指定されているリソース グループをメモすることができます。

        ![マネージド ディスク リソース グループを識別する](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        メモしたリソース グループに移動し、目的のマネージド ディスクを見つけます。

        ![リソース グループに接続されているマネージド ディスク](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - VHDX または (ダイナミックまたは差分) VHD をコピーした場合、VHDX または VHD はページ BLOB としてステージング ストレージ アカウントにアップロードされますが、VHD からマネージド ディスクへの変換は失敗します。 ステージング環境の **[ストレージ アカウント] > [BLOB]** に移動し、適切なコンテナー (Standard SSD、Standard HDD、または Premium SSD) を選択します。 VHD は、ステージング ストレージ アカウントにページ BLOB としてアップロードされ、料金が発生します。


## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

次の記事に進み、ローカル Web UI 経由で Data Box を管理する方法を学習します。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Data Box を管理する](../articles/databox/data-box-local-web-ui-admin.md)