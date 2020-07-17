---
title: VM イメージの共有アクセス署名 URI を取得する | Azure Marketplace
description: この記事では、各仮想ハード ディスク (VHD) の共有アクセス署名 (SAS) URI を取得する方法について説明します。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732637"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>VM イメージの Shared Access Signature URI の取得

> [!IMPORTANT]
> Azure 仮想マシン オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、オファーを管理する Cloud パートナー ポータルの [VM イメージの共有アクセス署名 URI の取得](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)に関するページの手順に従ってください。

この記事では、各仮想ハード ディスク (VHD) の共有アクセス署名 (SAS) Uniform Resource Identifier (URI) を生成する方法について説明します。

発行プロセス中に、プランに関連付けられている各 VHD の URI を指定する必要があります。 このようなプランは、以前は SKU (Stock Keeping Unit) と呼ばれていました。 マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。 この URI は、パートナー センターの **[プラン]** タブで入力します。

VHD の SAS URI を生成するときは、次の要件に従ってください。

* サポートされるのはアンマネージド VHD だけです。
* `List` および `Read` のアクセス許可のみが必要です。 書き込みまたは削除アクセス権を付与しないでください。
* アクセスするための期間 (有効期限) は、SAS URI の作成時から最低 3 週間必要です。
* UTC 時刻の変更から保護するには、開始日を現在の日付の 1 日前に設定します。 たとえば、今日が 2019 年 10 月 6 日の場合は、2019 年 10 月 5 日を選択します。

## <a name="generate-the-sas-address"></a>SAS アドレスを生成する

SAS アドレス (URL) の作成には、次の 2 つの一般的なツールが使用されます。

* **Microsoft Storage Explorer** - Windows、macOS、Linux 向けに提供されているグラフィカル ツール。
* **Microsoft Azure CLI** - Windows 以外のオペレーティング システムや自動化された環境、継続的インテグレーション環境に推奨されます。

### <a name="use-microsoft-storage-explorer"></a>Microsoft ストレージ エクスプローラーを使用する

1. [Microsoft Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)をダウンロードしてインストールします。
2. エクスプローラーを開き、左側のメニューで **[アカウントの追加]** を選択します。 **[Azure Storage へ接続する]** ダイアログ ボックスが表示されます。
3. **[Azure アカウントを追加する]** を選択し、 **[サインイン]** を選択します。 必要な手順を完了して、Azure アカウントにサインインします。
4. 左側の **[エクスプローラー]** ペインで、 **[ストレージ アカウント]** に移動し、このノードを展開します。
5. VHD を右クリックし、 **[Get Share Access Signature]\(共有アクセス署名の取得\)** を選択します。
6. **[Shared Access Signature]** ダイアログ ボックスが表示されます。 次のフィールドに入力します。

    * **[開始時刻]** - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    * **[有効期限]** - VHD のアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。
    * **[アクセス許可]** - [読み取り] と [一覧表示] のアクセス許可を選択します。
    * **[Container-level]\(コンテナーレベル\)** - **[コンテナー レベルの Shared Access Signature URI の生成]** チェック ボックスをオンにします。

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="[Shared Access Signature] ダイアログ ボックスの図":::

7. この VHD に関連付けられている SAS URI を作成するには、 **[作成]** を選択します。 ダイアログ ボックスが更新され、この操作の詳細が表示されます。
8. **[URI]** をコピーし、テキスト ファイルとして安全な場所に保存します。

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="[Shared Access Signature] の詳細ボックスの図":::

    ここで生成された SAS URI のアクセス範囲は、コンテナーレベルとなります。 これを特定するには、テキスト ファイルを編集して VHD 名を追加します (次の手順)。

9. SAS URI の vhds 文字列の後に VHD 名を挿入します (スラッシュを含めます)。 最終的な SAS URI は次のようになります。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>` たとえば、VDH の名前が `TestRGVM2.vhd` である場合、最終的な SAS URI は次のようになります。

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. 発行するプラン内の各 VHD に対して、これらの手順を繰り返します。

### <a name="using-azure-cli"></a>Azure CLI の使用

1. [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/) をダウンロードしてインストールします。 Windows と macOS のほか、各種 Linux ディストリビューション向けのバージョンが用意されています。
2. PowerShell ファイル (.ps1 ファイル拡張子) を作成し、次のコードをコピーして、ローカルに保存します。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 次のパラメーター値を使用するようにファイルを編集します。 `2020-04-01T00:00:00Z` など、UTC の datetime 形式で日付を指定します。

    * `<account-name>` - 実際の Azure ストレージ アカウント名
    * `<account-key>` - 実際の Azure ストレージ アカウント キー
    * `<vhd-name>` - 実際の VHD 名
    * `<start-date>` - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    * `<expiry-date>` - VHD のアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。

    この例では、(本記事の執筆時点における) 適切なパラメーター値を示します。

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. 変更を保存します。
5. 次のいずれかの方法を使用して、管理者特権でこのスクリプトを実行し、コンテナーレベルのアクセスのための **SAS 接続文字列**を作成します。

    * コンソールからスクリプトを実行します。 Windows で、スクリプトを右クリックし、 **[管理者として実行]** を選択します。
    * [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) などの PowerShell スクリプト エディターからスクリプトを実行します。 この画面には、このエディター内の SAS 接続文字列の作成が示されています。

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Windows PowerShell ISE を使用した SAS 接続文字列の作成の図":::

6. SAS 接続文字列をコピーし、テキスト ファイルとして安全な場所に保存します。 この文字列を編集して、最終的な SAS URI を作成するために VHD の場所情報を追加します。
7. Azure portal で、新しい URI に関連付けられている VHD を含む BLOB ストレージにアクセスします。
8. 次のスクリーンショットに示すように、**Blob service エンドポイント**の URL をコピーします。

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Blob service エンドポイントの図":::

9. 手順 6. で得た SAS 接続文字列でテキスト ファイルを編集します。 次の形式を使用して、完全な SAS URI を作成します。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    たとえば、VHD の名前が `TestRGVM2.vhd` の場合、SAS URI は次のようになります。

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

発行する予定の SKU に含まれる各 VHD について、これらの手順を繰り返します。

## <a name="verify-the-sas-uri"></a>SAS URI の検証

次のチェックリストを使用して次の点を検証することで、作成された各 SAS URI を確認します。

* URI が `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>` のような形式です。
* URI に実際の VHD イメージのファイル名 (拡張子 ".vhd" を含む) が含まれています。
* `sp=rl` が URI の中間付近に出現します。 この文字列は、`Read` と `List` のアクセスが指定されていることを示します。
* `sr=c` が出現する場合は、コンテナーレベルのアクセスが指定されていることを意味します。
* URI をコピーしてブラウザーに貼り付け、BLOB のテストダウンロードを行うことができます (ダウンロードが完了する前に操作を取り消すことができます)。

## <a name="next-step"></a>次のステップ

SAS URI の作成に問題がある場合は、[SAS URL に関する一般的な問題](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues)に関するページを参照してください。 それ以外の場合は、SAS URI を後で使用できるよう安全な場所に保存します。 これは、パートナー センターで VM オファーを発行するために必要になります。

* [Azure 仮想マシン オファーを作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
