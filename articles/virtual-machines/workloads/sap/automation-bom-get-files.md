---
title: 部品表の SAP メディアを取得する
description: Azure 上の SAP デプロイ自動化フレームワーク用の部品表 (BOM) で使用する SAP メディアをダウンロードする方法。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 85680b151a108e3ee9854b4d531669ba1995bf7d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725227"
---
# <a name="acquire-media-for-bom-creation"></a>BOM 作成用のメディアを取得する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、部品表 (BOM) が使用されます。 BOM を作成するには、関連する SAP インストール メディアを見つけてダウンロードする必要があります。 次に、これらのメディア ファイルを Azure ストレージ アカウントにアップロードする必要があります。

> [!NOTE]
> このガイドでは、デプロイに関する詳細なトピックを紹介します。 自動化フレームワークをデプロイする方法の基本的な説明については、代わりに[概要ガイド](automation-get-started.md)のページを参照してください。

このガイドは、SAP アプリケーション (DB) または HANA データベースのいずれかを使用する構成を対象としています。

## <a name="prerequisites"></a>前提条件

- SAP ソフトウェアをダウンロードし、メンテナンス プランナーにアクセスするためのアクセス許可がある SAP アカウント。
- お使いのコンピューターへの [SAP ダウンロード マネージャー](https://support.sap.com/en/my-support/software-downloads.html)のインストール。
- SAP システムに関する情報:
    - SAP アカウントのユーザー名とパスワード
    - デプロイする SAP システム製品 (**S/4HANA** など)
    - SAP システム ID (SAP SID)
    - 任意の言語パックの要件
    - アプリケーション インフラストラクチャで使用するオペレーティング システム (OS)
- Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。

## <a name="acquire-media"></a>メディアを取得する

[SAP インストール メディアのダウンロード](#download-media)を準備するには、次のようにします。

1. お使いのコンピューターに、スタック SAP ダウンロード用の一意のディレクトリを作成します。 たとえば、`~/Downloads/S4HANA_1909_SP2/` のようにします。

1. [SAP ONE Support Launchpad](https://launchpad.support.sap.com/) にサインインします。

1. ダウンロード バスケットをクリアします。

    1. **[Software Downloads]** にアクセスします。

    1. **[Download Basket]** を選択します。

    1. バスケット内のすべての項目を選択します。

    1. **[X]** を選択して、バスケット内のすべての項目を削除します。

1. ユーティリティ SAPCAR をダウンロード バスケットに追加します。

    1. 検索バーで検索の種類が **[Downloads]** に設定されていることを確認します。

    1. 検索バーに `SAPCAR` と入力し、 **[Search]** を選択します。

    1. **[Items Available to Download]** テーブルで、 **[Maintenance Software Component]** で **[SAPCAR]** の行を選択します。 この手順では、ユーティリティの最新バージョンで使用可能なダウンロードをフィルター処理します。

    1. テーブルのドロップダウン メニューに正しい OS の種類が表示されていることを確認します。 たとえば、`LINUX ON X86_64 64BIT` のようにします。

    1. SAPCAR 実行可能ファイルのファイル名の横にあるチェックボックスをオンにします。 たとえば、`SAPCAR_1320-80000935.EXE` のようにします。

    1. ショッピング カートのアイコンを選択して、選択したものをダウンロード バスケットに追加します。

1. [メンテナンス プランナー](https://support.sap.com/en/alm/solution-manager/processes-72/maintenance-planner.html)にサインインします。

1. SAP システムを設計します。 たとえば、 **S/4HANA** を使用している場合は、次のようにします。

    1. **SAP S/4HANA** のプランを選択します。

    1. 必要に応じて、メンテナンス プランの名前を変更します。

    1. **[Install New S4HANA System]** を選択します。 
    
    1. **[次へ]** を選択します

    1. **[Install a New System]** には、使用している SAP SID を入力します。

    1. **[Target Version]** では、ターゲットの SAP バージョンを選択します。 たとえば、 **[SAP S/4HANA 2020]** など。

    1. **[Target Stack]** では、ターゲット スタックを選択します。 たとえば、 **[Initial Shipment Stack]** など。

    1. 必要に応じて、 **[Target Product Instances]** を選択します。

    1. **[次へ]** を選択します

1. 共同デプロイを設計します。

    1. **[Co-Deployed with Backend]** を選択します。

    1. **[Target Version]** では、共同デプロイのターゲット バージョンを選択します。 たとえば、 **[SAP FIORI FOR SAP S/4HANA 2020]** など。

    1. **[Target Stack]** では、共同デプロイのターゲット スタックを選択します。 たとえば、 **[Initial Shipment Stack]** など。

    1. **[次へ]** を選択します

1. **[Continue Planning]** を選択します。 "*新しいシステム*" を使用している場合は、 **[次へ]** を選択します。 "*既存のシステム*" を使用している場合は、次の変更を行います。

    1. **[OS/DB dependent files]** では、 **[Linux on x86_64 64bit]** を選択します。

    1. **[選択の確認]** を選択します。

    1. **[次へ]** を選択します。

1. 必要に応じて、 **[Select Stack Independent Files]** で、ABAP 以外のデータベースの設定を構成します。 データベースを展開し、必要ない言語ファイルの選択を解除できます。

1. **[次へ]** を選択します。

1. 前に作成したスタック ダウンロード ディレクトリにスタック XML ファイルをダウンロードします。

    1. **[Push to Download Basket]** を選択します。

    1. **[Additional Downloads]** を選択します。

    1. **[Download Stack Text File]** を選択します。

    1. **[Download PDF]** を選択します。

    1. **[Excel にエクスポート]** を選択します。

    1. SAP ランチパッドで、もう一度ダウンロード バスケットにアクセスします。 新しい選択内容を表示するには、ページを最新の情報に更新する必要がある場合があります。

    1. **T** アイコンを選択して、ダウンロード バスケットの URL を含むファイルをダウンロードします。

## <a name="get-download-basket-manifest"></a>ダウンロード バスケットのマニフェストを取得する

> [!IMPORTANT]
> これらの手順は、スクリプト化された BOM 生成を実行する場合に実行してください。 SAP ダウンロード マネージャーを実行する前に、これらのアクションを実行する必要があります。 スクリプト化された BOM 生成を実行しない場合は、[次のセクションに進んでください](#download-media)。

SAP ダウンロード バスケットのマニフェスト JSON ファイル (`DownloadBasket.json`) を取得するには、次のようにします。

1. **Postman** ユーティリティを開きます。

1. ワークスペース タブでプラス記号 ( **+** ) ボタンを選択して、新しい要求を追加します。要求が含まれた新しいページが開きます。

1. **[Params]** タブで、要求の種類を `GET` に設定します。

1. 要求 URL については、`https://tech.support.sap.com:443/odata/svt/swdcuisrv/DownloadContentSet?_MODE=BASKET_CONTENT&_VERSION=3.1.2&$format=json` と入力します。

1. **[Authorization]\(承認\)** タブを選択します。

1. **[Type]** では、 **[Basic Auth]** を選択します。

1. **[Username]** には、SAP のユーザー名を入力します。

1. **[Password]** には、SAP のパスワードを入力します。

1. **[Headers]\(ヘッダー\)** タブを選択します。

1. [Accept-Encoding] と [User-Agent] のチェックボックスをオフにします。

1. **[送信]** ボタンを選択します。

1. **[Body]** タブでは、 **[Raw]** ビューが選択されていることを確認します。

1. 未加工の JSON 応答本文をコピーします。 応答をスタック ダウンロード ディレクトリに保存します。

## <a name="download-media"></a>メディアのダウンロード

SAP インストールメディアをダウンロードするには、次のようにします。

1. お使いのコンピューターで、SAP ダウンロード マネージャーを実行します。

1. SAP ダウンロード マネージャーにサインインします。

1. SAP ダウンロード バスケットにアクセスします。

1. ダウンロード ディレクトリを、作成したスタック ダウンロード ディレクトリに設定します。 たとえば、"~/Downloads/S4HANA_1909_SP2/" など。

1. ダウンロード バスケットからこのディレクトリにすべてのファイルをダウンロードします。

> [!NOTE]
> SAP ダウンロード URL が含まれたテキスト ファイルは常に `myDownloadBasketFiles.txt` です。 ただし、このファイルはアプリケーションまたはデータベースに固有です。 このファイルは、後のセクションで使用するために、この特定セクションの他のダウンロードと一緒に保持しておいてください。

## <a name="upload-media"></a>メディアをアップロードする

SAP メディアファイルとスタック ファイルを Azure ストレージ アカウントにアップロードするには、次のようにします。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure サービス]** の **[リソース グループ]** を選択します。 または、検索バーに `resource groups` と入力します。 

1. SAP ライブラリのリソース グループを選択します。

1. リソース グループ ページで、 **[リソース]** テーブルから `saplib` ストレージ アカウントを選択します。

1. ストレージ アカウント ページのメニューで、 **[データ ストレージ]** の下の **[コンテナー]** を選びます。

1. `sapbits` コンテナーを選択します。

1. コンテナー ページで、アーカイブとツールをアップロードします。

    1. **[アップロード]** ボタンを選択します。

    1. **[ファイルの選択]** を選択します。

    1. [前に SAP メディアをダウンロードしたディレクトリ](#download-media)に移動します。

    1. すべてのアーカイブ ファイルを選択します。 これらのファイル名は、`*.SAR`、`*.RAR`、`*.ZIP`、および `SAPCAR*.EXE` に類似したものになります。

    1. **[詳細]** を選択して、詳細オプションを表示します。

    1. **[アップロード ディレクトリ]** には、`archives` と入力します。

1. スタック ファイルをアップロードします。

    1. **[アップロード]** ボタンを選択します。

    1. **[ファイルの選択]** を選択します。

    1. [前のセクションで作成した](#acquire-media)ダウンロード ディレクトリに移動します。

    1. すべてのスタック ファイルを選択します。 これらのファイル名は、`MP_*.(xml|xls|pdf|txt)` に類似したものになります。

    1. **[詳細]** を選択して、詳細オプションを表示します。

    1. **[アップロード ディレクトリ]** には、`boms/<Stack_Version>/stackfiles` と入力します。ここで、`<Stack_Version>` は、お使いの製品情報の組み合わせです。 たとえば、`S4HANA_2020_ISS_v001` は製品の種類が `S4HANA`、製品リリースが `2020`、最初のソフトウェア出荷のサービス パックが `ISS`、スタックが `v001` であることを示しています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [BOM を準備する](automation-bom-prepare.md)
