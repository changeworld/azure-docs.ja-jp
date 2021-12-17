---
title: SAP S/4HANA ソースに接続して管理する
description: このガイドでは、Azure Purview で SAP S/4HANA に接続する方法と、Purview の機能を使用して SAP S/4HANA ソースをスキャンおよび管理する方法について説明します。
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fdf5f8ed70d0af65bc80ace02d1e4db503a04abe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549586"
---
# <a name="connect-to-and-manage-sap-s4hana-in-azure-purview"></a>Azure Purview での SAP S/4HANA への接続と管理

この記事では、Azure Purview で、SAP S/4HANA を登録する方法、および SAP S/4HANA を認証して操作する方法の概要を説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい**](how-to-lineage-sapecc.md)|

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

* 最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。 詳細については、[セルフホステッド統合ランタイムの作成および構成ガイド](../data-factory/create-self-hosted-integration-runtime.md)に関する記事を参照してください。

    >[!NOTE]
    >SAP S/4HANA のスキャンはメモリを集中的に使用する操作であり、少なくとも 128 GB 以上のメモリを搭載したマシンにセルフホステッド統合ランタイムをインストールすることをお勧めします。

* セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

* セルフホステッド統合ランタイム マシンに Visual Studio 2012 Update 4 の Visual C++ 再頒布可能パッケージがインストールされていることを確認します。 この更新プログラムがインストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/download/details.aspx?id=30679)。

* [Microsoft .NET 3.0 用の SAP コネクタ](https://support.sap.com/en/product/connectors/msnet.html) (64 ビット) を SAP の Web サイトからダウンロードし、それをセルフホステッド統合ランタイム コンピューターにインストールします。 インストール時に、 **[Optional setup steps]\(省略可能なセットアップ手順\)** ウィンドウで、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを必ず選択します。

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="前提条件" border="true":::

* コネクタにより、[SAP Java コネクタ (JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0 API を使用して SAP からメタデータが読み取られます。 そのため、セルフホステッド統合ランタイムがインストールされている仮想マシンで Java コネクタが使用可能であることを確認します。 環境に合った JCo ディストリビューションを使用していることを確認してください。 たとえば、Microsoft Windows コンピューターで、sapjco3.jar と sapjco3.dll ファイルが使用可能であることを確認します。

    > [!Note]
    >このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

* [ABAP 関数のデプロイ ガイド](abap-functions-deployment-guide.md)に記載されている手順に従って、SAP サーバーにメタデータ抽出 ABAP 関数モジュールをデプロイします。 SAP サーバーに RFC 関数モジュールを作成するには、ABAP 開発者アカウントが必要です。 ユーザー アカウントには、SAP サーバーに接続し、次の RFC 関数モジュールを実行するための十分なアクセス許可が必要です。
  * STFC_CONNECTION (接続を確認する)
  * RFC_SYSTEM_INFO (システム情報を確認する)

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に SAP S/4HANA を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

SAP S/4HANA ソースでサポートされている認証は **基本認証** のみです。

### <a name="steps-to-register"></a>登録する手順

1. ご自分の Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. [Register sources]\(ソースの登録\) で、 **[SAP S/4HANA]** を選択します。 **[続行]** を選択します。

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="SAPS/4Hana の登録オプション" border="true":::

**[Register sources (SAP S/4HANA)]\(ソースの登録 (SAP S/4HANA)\)** 画面で、次のようにします。

1. データ ソースがカタログに一覧表示されるときの **名前** を入力します。

1. SAP S/4HANA ソースに接続するための **アプリケーション サーバー** 名を入力します。 SAP アプリケーション サーバー ホストの IP アドレスにすることもできます。

1. SAP の **システム番号** を入力します。 これは、00 から 99 の 2 桁の整数です。

1. コレクションを選択するか、新しいものを作成します (省略可能)

1. データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="SAP S/4HANA を登録する" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、SAP S/4HANA をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

1. 管理センターで、[統合ランタイム] を選択します。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](./manage-integration-runtimes.md)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します

1. **[ソース]** に移動します。

1. 登録されている SAP S/4HANA ソースを選択します。

1. **[+ 新しいスキャン]** を選択します

1. 次の詳細を指定します。

    1. **[名前]** : スキャンの名前

    1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    1. **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。

        * 資格情報を作成するときに [基本認証] を選択します。
        * ユーザー名入力フィールドで、SAP サーバーに接続するためのユーザー ID を指定します。
        * SAP サーバーに接続するために使用されるユーザー パスワードを、秘密鍵に格納します。

    1. **[クライアント ID]:** SAP システム クライアント ID をここに入力します。 クライアントは、000 から 999 の 3 桁の数値で識別されます。

    1. **[JCo library path]\(JCo ライブラリ パス\)** : JCo ライブラリが配置されているフォルダーへのパスを指定します。

    1. **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする SAP S/4HANA ソースのサイズによって異なります。 使用可能な大きなメモリ (100 など) を指定することをお勧めします。

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="SAP S/4HANA をスキャンする" border="true":::

1. **[続行]** を選択します。

1. **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

1. 自分のスキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
