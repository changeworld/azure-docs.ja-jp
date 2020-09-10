---
title: VM イメージの SAS URI を取得する - Azure Marketplace
description: Azure Marketplace で仮想ハード ディスク (VHD) の Shared Access Signature (SAS) URI を生成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144520"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>VM イメージの SAS URI を取得する

発行プロセス中に、プラン (以前は SKU と呼ばれていました) に関連付けられている各 VHD の SAS (Shared Access Signature) URI を指定する必要があります。 マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。 この URI は、パートナー センターの **[プラン]** タブで入力します。

VHD の SAS URI の生成には、次の要件があります。

- アンマネージド VHD のみがサポートされます。
- リストおよび読み取りアクセス許可のみが必要です。 書き込みまたは削除アクセス権を付与しないでください。
- アクセスするための期間 (有効期限) は、SAS URI の作成時から最低 3 週間必要です。
- UTC 時刻の変更から保護するには、開始日を現在の日付の 1 日前に設定します。 たとえば、現在の日付が 2020 年 6 月 16 日である場合は、6/15/2020 を選択します。

## <a name="generate-the-sas-address"></a>SAS アドレスを生成する

SAS アドレス (URL) の作成には、次の 2 つの一般的なツールが使用されます。

1. **Microsoft Storage Explorer** - Windows、macOS、Linux 向けに提供されているグラフィカル ツール。
2. **Microsoft Azure CLI** - Windows 以外のオペレーティング システムや自動化された環境、継続的インテグレーション環境に推奨されます。

### <a name="using-tool-1-microsoft-storage-explorer"></a>ツール 1 の使用: Microsoft Storage Explorer

1. [Microsoft Azure ストレージ エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)をダウンロードしてインストールします。
2. エクスプローラーを開き、左側のメニューで **[アカウントの追加]** を選択します。
3. **[Azure Storage への接続]** ダイアログ ボックスで、 **[Azure アカウントを追加する]** を選択し、Azure アカウントにサインインします。
4. 左側の [エクスプローラー] ウィンドウで、 **[ストレージ アカウント]** ノードを展開します。
5. VHD を右クリックし、 **[共有アクセス署名の取得]** を選択します。
6. **[共有アクセス署名]** ダイアログ ボックスで、次のフィールドに入力します。

    1. [開始時刻] - VHD のアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    2. [有効期限] – VHD アクセスのためのアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。
    3. [アクセス許可] - [読み取り] と [一覧表示] のアクセス許可を選択します。
    4. [Container-level]\(コンテナーレベル\) - [コンテナー レベルの Shared Access Signature URI の生成] チェック ボックスをオンにします。

    ![[共有アクセス署名] ダイアログ ボックス。](media/vm/create-sas-uri-storage-explorer.png)

7. この VHD に関連付けられている SAS URI を作成するには、 **[作成]** を選択します。 ダイアログ ボックスが更新され、この操作の詳細が表示されます。

8. [URI] をコピーし、テキスト ファイルとして安全な場所に保存します。

    ![URI のコピー。](media/vm/create-sas-uri-shared-access-signature-details.png)

    ここで生成された SAS URI のアクセス範囲は、コンテナーレベルとなります。 これを固有のものにするには、テキスト ファイルを編集して VHD 名を追加します。

9. SAS URI の vhds 文字列の後に VHD 名を挿入します (スラッシュを含めます)。 最終的な SAS URI は次のようになります。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. 発行するプラン内の各 VHD に対して、これらの手順を繰り返します。

### <a name="using-tool-2-azure-cli"></a>ツール 2 の使用: Azure CLI

1. [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/) をダウンロードしてインストールします。 Windows と macOS のほか、各種 Linux ディストリビューション向けのバージョンが用意されています。
2. PowerShell ファイル (.ps1 ファイル拡張子) を作成し、次のコードをコピーして、ローカルに保存します。

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 次のパラメーター値を使用するようにファイルを編集します。 UTC 日時形式の日付 (2020-04-01T00:00:00Z など) を指定します。

    - account-name – Azure ストレージ アカウント名。
    - account-key – Azure ストレージ アカウント キー。
    - vhd-name – VHD 名。
    - start-date – VHD アクセスのためのアクセス許可の開始日。 現在の日付の 1 日前の日付を指定します。
    - expiry-date – VHD アクセスのためのアクセス許可の有効期限。 現在の日付から 3 週間以上先の日付を指定します。

    適切なパラメーター値の例を次に示します (この記事の作成時点)。

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. 変更を保存します。
2. 次のいずれかの方法を使用して、管理者特権でこのスクリプトを実行し、コンテナーレベルのアクセスのための SAS 接続文字列を作成します。

    - コンソールからスクリプトを実行します。 Windows で、スクリプトを右クリックし、 **[管理者として実行]** を選択します。
    - [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) などの PowerShell スクリプト エディターからスクリプトを実行します。 この画面には、このエディター内の SAS 接続文字列の作成が示されています。

    [![PowerShell エディター内での SAS 接続文字列の作成](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. SAS 接続文字列をコピーし、テキスト ファイルとして安全な場所に保存します。 この文字列を編集して、最終的な SAS URI を作成するために VHD の場所情報を追加します。
7. Azure portal で、新しい URI に関連付けられている VHD を含む BLOB ストレージにアクセスします。
8. Blob サービス エンドポイントの URL をコピーします。

    ![BLOB サービス エンドポイントの URL のコピー。](media/vm/create-sas-uri-blob-endpoint.png)

9. 手順 6. で得た SAS 接続文字列でテキスト ファイルを編集します。 次の形式を使用して、完全な SAS URI を作成します。

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>SAS URI の検証

要求を送信した後に SAS URI に関連した問題が発生しないようにするために、パートナー センターで発行する前に SAS URI を確認します。 このプロセスは省略可能ですが、実行することをお勧めします。

- URI には、VHD イメージのファイル名 (ファイル名拡張子 `.vhd` を含む) が含まれています。
- `Sp=rl` が URI の中間付近に出現します。 この文字列は、読み取りおよび一覧表示アクセスが指定されていることを示します。
- `sr=c` が出現する場合は、コンテナーレベルのアクセスが指定されていることを意味します。
- URI をコピーしてブラウザーに貼り付け、BLOB のテストダウンロードを行うことができます (ダウンロードが完了する前に操作を取り消すことができます)。

## <a name="next-step"></a>次のステップ

- [Azure 仮想マシン オファーの作成](azure-vm-create-offer.md)に関するページを参照してください。
