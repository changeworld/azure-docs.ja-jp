---
title: 新しい更新プログラムのインポート方法 | Microsoft Docs
description: 新しい更新プログラムを IoT Hub の Device Update for IoT Hub にインポートするための How-To ガイド。
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660647"
---
# <a name="import-new-update"></a>新しい更新プログラムのインポート
新しい更新プログラムを Device Update for IoT Hub にインポートする方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Device Update for IoT Hub が有効になっている IoT ハブにアクセスできること](create-device-update-account.md)。 IoT ハブには、S1 (Standard) レベル以上を使用することをお勧めします。 
* Device Update 用にプロビジョニングされている、IoT Hub 内の IoT デバイス (またはシミュレーター)。
   * 実際のデバイスを使用する場合は、イメージ更新用の更新イメージ ファイル、またはパッケージの更新用の [APT マニフェスト ファイル](device-update-apt-manifest.md)が必要です。
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) 以降。
* サポートされているブラウザー:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> このサービスに送信された一部のデータは、このインスタンスが作成されたリージョン外のリージョンで処理される可能性があります。

## <a name="create-device-update-import-manifest"></a>Device Update のインポート マニフェストを作成する

1. 更新イメージ ファイルまたは APT マニフェスト ファイルが、PowerShell からアクセスできるディレクトリに配置されていることを確認します。

2. [Device Update for IoT Hub リポジトリ](https://github.com/azure/iot-hub-device-update)を複製するか、PowerShell からアクセスできる場所に .zip ファイルとしてダウンロードします (zip ファイルをダウンロードしたら、[`Properties`]  >  [`General`] タブを右クリックし、[`Security`] セクションの [`Unblock`] をオンにして、PowerShell のセキュリティ警告プロンプトが表示されないようにします)。

3. PowerShell で、`tools/AduCmdlets` ディレクトリに移動し、次のように実行します。

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. サンプルのパラメーター値を置き換えて次のコマンドを実行し、インポート マニフェスト (更新内容を記述する JSON ファイル) を生成します。
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    参考として、次に前述のパラメーター値の例を示します。 ドキュメント全体については、以下のインポート マニフェスト スキーマを参照してください。

    | パラメーター | 説明 |
    | --------- | ----------- |
    | deviceManufacturer | 更新プログラムと互換性があるデバイスの製造元 (Contoso など)
    | deviceModel | 更新プログラムと互換性があるデバイスのモデル (toaster など)
    | updateProvider | 更新プログラム ID のプロバイダー部分 (Fabrikam など)
    | updateName | 更新プログラム ID の名前部分 (ImageUpdate など)
    | updateVersion | 更新プログラムのバージョン (2.0 など)
    | updateType | <ul><li>イメージの更新には `microsoft/swupdate:1` を指定します</li><li>パッケージの更新には `microsoft/apt:1` を指定します</li></ul>
    | installedCriteria | <ul><li>更新の種類 `microsoft/swupdate:1` には SWVersion の値を指定します</li><li>更新の種類 `microsoft/apt:1` には推奨値を指定します
    | updateFilePath(s) | コンピューター上の更新プログラム ファイルへのパス

    インポート マニフェスト スキーマ (全体)

    | 名前 | Type | 説明 | 制限 |
    | --------- | --------- | --------- | --------- |
    | UpdateId | `UpdateId` オブジェクト | 更新プログラム ID。 |
    | UpdateType | string | 更新の種類: <ul><li>参照エージェントを使用してパッケージベースの更新を実行する場合は、`microsoft/apt:1` を指定します。</li><li>参照エージェントを使用してイメージベースの更新を実行する場合は、`microsoft/swupdate:1` を指定します。</li><li>サンプル エージェント シミュレーターを使用する場合は、`microsoft/simulator:1` を指定します。</li><li>カスタム エージェントを開発する場合は、カスタムの種類を指定します。</li></ul> | <ul><li>形式: `{provider}/{type}:{typeVersion}`</li><li>最大 32 文字</li></ul> |
    | InstalledCriteria | string | 更新プログラムが正常に適用されたかどうかを判断するために、エージェントによって解釈される文字列。  <ul><li>更新の種類 `microsoft/swupdate:1` には SWVersion の **値** を指定します。</li><li>更新の種類 `microsoft/apt:1` には `{name}-{version}` を指定します。これは、APT ファイルから取得した名前とバージョンです。</li><li>更新の種類 `microsoft/simulator:1` には更新プログラム ファイルのハッシュを指定します。</li><li>カスタム エージェントを開発する場合は、カスタムの文字列を指定します。</li></ul> | 最大 64 文字 |
    | 互換性 | `CompatibilityInfo` オブジェクトの配列 | この更新プログラムと互換性のあるデバイスの互換性情報。 | 最大 10 個の項目 |
    | CreatedDateTime | 日付/時刻 | 更新プログラムが作成された日時。 | ISO 8601 の、区切られた日付と時刻の形式 (UTC) |
    | ManifestVersion | string | マニフェスト スキーマのバージョンをインポートします。 `2.0` を指定します。これは、`urn:azureiot:AzureDeviceUpdateCore:1` インターフェイスおよび `urn:azureiot:AzureDeviceUpdateCore:4` インターフェイスと互換性があります。</li></ul> | `2.0` である必要があります。 |
    | ファイル | `File` オブジェクトの配列 | ペイロード更新ファイル | 最大 5 個のファイル |

注: すべてのフィールドが必須です。

## <a name="review-generated-import-manifest"></a>生成されたインポート マニフェストを確認する

例:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>更新プログラムのインポート

1. [Azure portal](https://portal.azure.com) にログインし、Device Update がある IoT Hub に移動します。

2. ページの左側で、[デバイスの自動管理] の下にある [デバイスの更新プログラム] を選択します。

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="更新プログラムをインポートします" lightbox="media/import-update/import-updates-3.png":::

3. 画面の上部に複数のタブが表示されます。 [更新プログラム] タブを選択します。

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="更新プログラム" lightbox="media/import-update/updates-tab.png":::

4. [デプロイの準備完了] ヘッダーの下にある [+ 新しい更新プログラムのインポート] を選択します。

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="新しい更新プログラムをインポートします" lightbox="media/import-update/import-new-update-2.png":::

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 PowerShell コマンドレットを使用して、さきほど作成したインポート マニフェストを選択します。 次に、[1 つまたは複数の更新プログラム ファイルの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 更新プログラム ファイルを選択します。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="更新プログラム ファイルを選択します" lightbox="media/import-update/select-update-files.png":::

6. [ストレージ コンテナーの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 次に、適切なストレージ アカウントを選択します。 ストレージ コンテナーは、更新プログラム ファイルを一時的にステージするために使用されます。

   :::image type="content" source="media/import-update/storage-account.png" alt-text="ストレージ アカウント" lightbox="media/import-update/storage-account.png":::

7. コンテナーを既に作成してある場合は、再利用できます (そうでない場合は、[+ コンテナー] を選択して、更新用の新しいストレージ コンテナーを作成します)。  使用するコンテナーを選択し、[選択] をクリックします。

   :::image type="content" source="media/import-update/container.png" alt-text="コンテナーを選択します" lightbox="media/import-update/container.png":::

8. [送信] を選択してインポート プロセスを開始します。

   :::image type="content" source="media/import-update/publish-update.png" alt-text="更新プログラムを発行する" lightbox="media/import-update/publish-update.png":::

9. インポート プロセスが開始され、画面が [インポートの履歴] セクションに切り替わります。 インポート処理が完了するまでの進行状況を表示するには、[更新] を選択します (更新プログラムのサイズによっては、数分で完了することも、長時間かかることもあります)。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="インポート シーケンスを更新します" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. [状態] 列にインポートが正常に完了したと表示されたら、[デプロイの準備完了] ヘッダーを選択します。 インポートされた更新プログラムが一覧に表示されるはずです。

   :::image type="content" source="media/import-update/update-ready.png" alt-text="ジョブの状態" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>次の手順

[グループを作成する](create-update-group.md)

[インポートの概念について学習する](import-concepts.md)
