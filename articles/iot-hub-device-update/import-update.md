---
title: 新しい更新プログラムの追加方法 | Microsoft Docs
description: 新しい更新プログラムを Device Update for IoT Hub に追加するための攻略ガイド。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: caa58c1affc9e4a9cc9d3ec5b63e0b396e289144
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370424"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Device Update for IoT Hub に更新プログラムを追加する
新しい更新プログラムを Device Update for IoT Hub に追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Device Update for IoT Hub が有効になっている IoT ハブにアクセスできること](create-device-update-account.md)。 
* [Device Update 用にプロビジョニングされている](device-update-agent-provisioning.md)、IoT Hub 内の IoT デバイス (またはシミュレーター)。
* [PowerShell 5](/powershell/scripting/install/installing-powershell) 以降 (Linux、macOS、Windows のインストールを含む)
* サポートされているブラウザー:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> このサービスに送信された一部のデータは、このインスタンスが作成されたリージョン外のリージョンで処理される可能性があります。

## <a name="obtain-an-update-for-your-devices"></a>デバイスの更新プログラムを入手する

Device Update を設定し、デバイスをプロビジョニングしたので、これらのデバイスに配置する更新ファイルが必要になります。

OEM またはソリューション インテグレーターからデバイスを購入した場合、その組織によって更新ファイルが提供される可能性が高いため、更新プログラムを作成する必要はありません。 OEM またはソリューション インテグレーターに問い合わせて、更新プログラムの入手方法を確認してください。

使用するデバイス用のソフトウェアが組織で既に作成されている場合は、その同じグループによって、そのソフトウェア用の更新プログラムが作成されます。 Device Update for IoT Hub を使用して配置される更新プログラムを作成する場合は、シナリオに応じて、[イメージベースまたはパッケージベースの方法](understand-device-update.md#support-for-a-wide-range-of-update-artifacts)のいずれかで開始します。 注: 独自の更新プログラムを作成しようとしていますが、まだ着手したばかりの場合、GitHub が、開発を管理するための優れたオプションになります。 [GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration) を使用すると、ソース コードを格納して管理し、継続的インテグレーション (CI) と継続的配置 (CD) を実施できます。

## <a name="create-a-device-update-import-manifest"></a>Device Update のインポート マニフェストを作成する

基本的な[インポートの概念](import-concepts.md)をまだ理解していない場合は、それを理解し、まず、[イメージベース](device-update-raspberry-pi.md)か[パッケージベース](device-update-ubuntu-agent.md)をお試しください。

1. 更新ファイルが、PowerShell からアクセスできるディレクトリに配置されていることを確認します。

2. 更新イメージファイルまたは APT マニフェスト ファイルが配置されているディレクトリに、 **AduUpdate.psm1** という名前のテキスト ファイルを作成します。 次に、 [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell コマンドレットを開き、内容をテキスト ファイルにコピーして、テキスト ファイルを保存します。

3. PowerShell で、手順 2 で作成した PowerShell コマンドレットを作成したディレクトリに移動します。 下の [コピー] オプションを使用し、PowerShell に貼り付けてコマンドを実行します。

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

    次の表は、上記のパラメーターを設定する方法に関するクイック リファレンスです。 さらに詳しい情報が必要な場合は、完全な「[インポート マニフェスト スキーマ](import-schema.md)」も参照してください。

    | パラメーター | 説明 |
    | --------- | ----------- |
    | deviceManufacturer | 更新プログラムと互換性があるデバイスの製造元 (Contoso など)。 _製造元_ の [デバイス プロパティ](./device-update-plug-and-play.md#device-properties)と一致する必要があります。
    | deviceModel | 更新プログラムと互換性があるデバイスのモデル (toaster など)。 _モデル_ の [デバイス プロパティ](./device-update-plug-and-play.md#device-properties)と一致する必要があります。
    | updateProvider | 更新プログラムを作成または直接担当するエンティティ。 多くの場合、会社名になります。
    | updateName | 更新プログラムのクラスの識別子。 クラスには、任意のものを選択できます。 多くの場合、デバイス名またはモデル名になります。
    | updateVersion | 同じプロバイダーと名前を持つ他の更新プログラムとこの更新プログラムを区別するためのバージョン番号。 デバイス上の個々のソフトウェア コンポーネントのバージョンと一致しない場合があります (選択した場合は可能です)。
    | updateType | <ul><li>イメージの更新には `microsoft/swupdate:1` を指定します</li><li>パッケージの更新には `microsoft/apt:1` を指定します</li></ul>
    | installedCriteria | デプロイ時に、既にデバイスにインストールされているバージョンと更新プログラムのバージョンを比較するために使用されます。 installedCriteria の値がデバイス上のバージョンと一致しない場合、更新プログラムをデバイスにデプロイすると、"failed" という結果が返されます。<ul><li>更新の種類が `microsoft/swupdate:1` の場合は、SWVersion の値を指定します </li><li>更新の種類が `microsoft/apt:1` の場合は、**name-version** を指定します。この _name_ は APT マニフェストの名前で、_version_ は APT マニフェストのバージョンです。 例: contoso-iot-edge-1.0.0.0。
    | updateFilePath(s) | コンピューター上の更新プログラム ファイルへのパス。


## <a name="review-the-generated-import-manifest"></a>生成されたインポート マニフェストを確認する

マニフェストの出力例を以下に示します。 この例では、この更新プログラムを構成するファイルとして、.json ファイルと .zip ファイルの 2 つのファイルがあります。 いずれかの項目について不明な点がある場合は、完全な「[インポート マニフェスト スキーマ](import-schema.md)」を参照してください。 
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

## <a name="import-an-update"></a>更新プログラムをインポートする

> [!NOTE]
> 次の手順は、Azure portal ユーザー インターフェイスを使用して更新プログラムをインポートする方法を示しています。 代わりに、[Device Update for IoT Hub の API](#if-youre-importing-via-apis-instead) を使用して、更新プログラムをインポートすることもできます。

1. [Azure portal](https://portal.azure.com) にログインし、Device Update がある IoT Hub に移動します。

2. ページの左側で、[デバイスの自動管理] の下にある [デバイスの更新プログラム] を選択します。

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="更新プログラムをインポートします" lightbox="media/import-update/import-updates-3.png":::

3. 画面の上部に複数のタブが表示されます。 [更新プログラム] タブを選択します。

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="更新プログラム" lightbox="media/import-update/updates-tab.png":::

4. [デプロイの準備完了] ヘッダーの下にある [+ 新しい更新プログラムのインポート] を選択します。

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="新しい更新プログラムをインポートします" lightbox="media/import-update/import-new-update-2.png":::

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 PowerShell コマンドレットを使用して、さきほど作成したインポート マニフェストを選択します。 次に、[1 つまたは複数の更新プログラム ファイルの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 インポート マニフェストの作成時に含めたものと同じ更新プログラム ファイルを選択します。

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

## <a name="if-youre-importing-via-apis-instead"></a>代わりに API を使用してインポートしている場合

Azure portal 経由でインポートするのではなく、[Device Update for IoT Hub の更新 API](/rest/api/deviceupdate/updates) を使用して更新プログラムをインポートする場合は、次の点にご注意ください。
  - 更新 API を呼び出す前に、更新プログラム ファイルを Azure Blob Storage の場所にアップロードする必要があります。
  - 先ほど作成したインポート マニフェストを使用する、この[サンプル API 呼び出し](import-schema.md#example-import-request-body)を参照できます。
  - テスト中に同じ SAS URL を再利用すると、トークンの有効期限が切れたときにエラーが発生することがあります。 これは、更新プログラムのコンテンツ自体だけでなくインポート マニフェストも送信する場合に当てはまります。