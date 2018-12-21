---
title: Azure および Azure Stack を使用して Staged Data Analytics ソリューションを作成する | Microsoft Docs
description: Azure および Azure Stack を使用して Staged Data Analytics ソリューションを作成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: d63faf63012360d4448166ac5d69eba6ede9d0ed
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969534"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>チュートリアル:Azure と Azure Stack を使用してステージング データ分析ソリューションを作成する 

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

オンプレミス環境とパブリック クラウド環境の両方を使用して、複数施設の企業の需要に応える方法について説明します。 Azure Stack は、特にセキュリティ、機密性、企業ポリシー、および規制の諸要件が場所やユーザーごとに異なる場合に、ローカルおよびリモートデータを収集、処理、保存、および分散するための迅速で安全で柔軟なソリューションを提供します。

このパターンでは、顧客が迅速な意思決定ができるように、収集の時点での分析を必要とするデータを収集します。 このデータ収集はインターネットにアクセスせずに行われることがあります。 接続が確立されたら、リソース集約的データ分析を行って、さらに洞察を得ることが必要になる場合があります。 パブリック クラウドが遅すぎるまたは使用できないときにも、データを分析できます。

このチュートリアルでは、以下を実現するためのサンプル環境を構築します。

> [!div class="checklist"]
> - 生データのストレージ Blob を作成します。
> - クリーン データを Azure Stack から Azure に移動する新しい Azure Stack 関数を作成します。
> - Blob Storage でトリガーされる関数を作成します。
> - Blob とキューを含む Azure Stack ストレージ アカウントを作成します。
> - キューによってトリガーされる関数を作成します。
> - キューによってトリガーされる関数をテストします。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack は Azure の拡張機能です。 Azure Stack は、オンプレミスの環境にクラウド コンピューティングの俊敏性とイノベーションを提供します。これにより、どこでもハイブリッド アプリをビルドしてデプロイできる唯一のハイブリッド クラウドが実現されます。  
> 
> [ハイブリッド アプリケーションのための設計の考慮事項](https://aka.ms/hybrid-cloud-applications-pillars)に関するホワイト ペーパーでは、ハイブリッド アプリケーションを設計、デプロイ、および運用するためのソフトウェア品質の重要な要素 (配置、スケーラビリティ、可用性、回復性、管理容易性、およびセキュリティ) についてレビューしています。 これらの設計の考慮事項は、ハイブリッド アプリケーションの設計を最適化したり、運用環境での課題を最小限に抑えたりするのに役立ちます。

## <a name="prerequisites"></a>前提条件

このソリューションのビルドには、いくつかの準備が必要です。

-   インストールされ機能している Azure Stack (詳細については「[Azure Stack の概要](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview)」を参照してください)

-   Azure サブスクリプション。 ([無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールする](http://storageexplorer.com/)。

-   関数で処理するデータは独自に用意することが必要。 データは生成する必要があり、Azure Stack ストレージ Blob コンテナーへのアップロードに使用できる必要があります。

## <a name="issues-and-considerations"></a>問題と注意事項

### <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項

Azure の関数とストレージ ソリューションは、データ ボリュームおよび処理の需要に応じて拡張します。 Azure のスケーラビリティ情報とターゲットについては、[Azure スケーラビリティに関するドキュメント](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)を参照してください。

### <a name="availability-considerations"></a>可用性に関する考慮事項

ストレージは、このパターンの可用性に関する主要な考慮事項です。 大規模データ ボリュームの処理および分散には、高速リンク経由の接続が必要です。

### <a name="manageability-considerations"></a>管理容易性に関する考慮事項

開発ツールおよびソース管理でソリューションを管理する方法を検討してください。

## <a name="create-the-raw-data-storage-blob"></a>生データのストレージ Blob を作成する

ストレージ アカウントと Blob コンテナーでは、マシンと従業員のアクティビティ、施設のデータ、運用環境のメトリック、およびその他のレポートを含む、オンプレミスのアクティビティによって生成されたすべての元のデータを保持します。

1.  [*Azure Stack Portal*](https://portal.local.azurestack.external/) にサインインします。

2.  Azure Stack Portal で左側のメニューを展開してサービスのメニューを開き、**[すべてのサービス]** を選択します。 **[ストレージ]** まで下へスクロールし、**[ストレージ アカウント]** を選択します。 [ストレージ アカウント] ウィンドウで、**[追加]** を選択します。

3.  アカウントに関する次の情報を使用します。

    a.  名前:**<任意>**

    b.  デプロイ モデル: **Resource Manager**

    c.  アカウントの種類: **Storage (汎用 V1)**

    d.  場所: **米国西部**

    e.  レプリケーション: **ローカル冗長ストレージ (LRS)**

    f.  パフォーマンス: **Standard**

    g.  安全な転送が必須: **無効**

    h.  サブスクリプション:いずれかを選択します。

    i.  リソース グループ: 新しいリソース グループを指定するか、既定のリソース グループを選択します。

    j.  仮想ネットワークの構成: **無効**

4.  **[作成]** をクリックしてストレージ アカウントを作成します。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  作成し終えたら、ストレージ アカウントの名前を選択します。

6.  アカウント ブレードの **BLOB SERVICE** の見出しの下で **[コンテナー]** を選択します。

7.  ブレードの上部で **[+ Container] (+ コンテナー)** を選択し、**[コンテナー]** を選択します。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  名前:**任意**

9.  パブリック アクセス レベル: **コンテナー** (コンテナーと BLOB 用の匿名読み取りアクセス)

10.  **[OK]** を選択します。

## <a name="create-an-azure-stack-function"></a>Azure Stack 関数を作成する

クリーン データを Azure Stack から Azure に移動する新しい Azure Stack 関数を作成します。

### <a name="create-the-azure-stack-function-app"></a>Azure Stack 関数アプリを作成する

1. [Azure Stack ポータル](https://portal.local.azurestack.external)にサインインします。
2. **[すべてのサービス]** を選択します。
3. **[Web + モバイル]** グループの **[Function Apps]** を選択します。

4.  イメージの下にある表で指定された設定を使用して、関数アプリを作成します。

    | Setting | 推奨値 | 説明 |
    | ---- | ---- | ---- |
    | アプリの名前 | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a`-`z`、`0``-9`、および `-` です。 |
    | サブスクリプション | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **リソース グループ** |  |  |
    | myResourceGroup | Function App を作成するための新しいリソース グループの名前。 |  |
    | OS |  Windows | サーバーなしのホスティングは、現時点では、Windows で実行される場合のみ使用できます。 |
    | **ホスティング プラン** |  |  |
    | 従量課金プラン | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 既定の [従量課金プラン] では、リソースは関数の必要に応じて動的に追加されます。 このサーバーなしのホスティングでは、関数が実行された時間にのみ課金されます。 |  |
    | Location | 最寄りのリージョン | ユーザーに近いリージョン、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |
    | **ストレージ アカウント** |  |  |
    | \<上記で作成したストレージ アカウント> | Function App によって使用される新しいストレージ アカウントの名前。 ストレージ アカウント名の長さは 3 文字から 24 文字でなければなりません。 名前に使用できるのは、数字と小文字のみです。 既存のアカウントを使用することもできます。 |  |

    **例:**

    ![新しい関数アプリの設定を定義する](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  **[作成]** を選択して、Function App をプロビジョニングし、デプロイします。

6.  ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

    ![新しい関数アプリの設定を定義する](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  **[リソースに移動]** を選択して、新しい関数アプリを確認します。

![Function App が正常に作成されました。](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Azure Stack 関数アプリに関数を追加する

1.  **[関数]**、続いて **[+New Function] (+ 新しい関数)** ボタンをクリックして、新しい関数を作成します。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  **[タイマー トリガー]** を選択します。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  言語として [**C\#**] を選択し、関数に `upload-to-azure` という名前を付けます。スケジュールを `0 0 * * * *` に設定します (これは CRON 表記で 1 時間に一度を意味します)。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Blob Storage でトリガーされる関数の作成

1.  Function App を展開し、**[関数]** の横にある **[+]** ボタンを選択します。

2.  検索フィールドに、「`blob`」と入力し、**BLOB トリガー** テンプレート用の目的の言語を選択します。

  ![Blob Storage トリガー テンプレートを選択します。](media/azure-stack-solution-staged-data-analytics/image10.png)

3.  次の表で指定されている設定を使用してください。

    | 設定 | 推奨値 | 説明 |
    | ------- | ------- | ------- |
    | 名前 | Function App 内で一意 | この BLOB によってトリガーされる関数の名前。 |
    | パス | \<上記のストレージの場所からのパス> | 監視されている Blob Storage ストレージ内の位置。 Blob のファイル名は、name パラメーターとしてバインディングで渡されます。 |
    | ストレージ アカウント接続 | Function App 接続 | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。 |

    **例:**

    ![Blob Storage でトリガーされる関数を作成します。](media/azure-stack-solution-staged-data-analytics/image11.png)

4.  **[作成]** を選択すると、関数が作成されます。

### <a name="test-the-function"></a>関数をテストする

1.  Microsoft Azure portal で関数を参照します。 ページの下部にある **[ログ]** を展開し、ログ ストリーミングが一時停止していないことを確認します。

2.  Storage Explorer を開き、このセクションの冒頭で作成したストレージ アカウントに接続します。

3.  ストレージ アカウント、**Blob コンテナー**、以前に作成した Blob を展開します。 **[アップロード]**、**[ファイルのアップロード]** の順に選択します。

    ![Blob コンテナーにファイルをアップロードします。](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  [ファイルのアップロード] ダイアログ ボックスで、[ファイル] フィールドを選択します。 画像ファイルなど、ローカル コンピューター上のファイルを参照して選択し、**[開く]**、**[アップロード]** の順に選択します。

5.  関数ログに戻り、Blob が読み取られたことを確認します。

    **例:**

    ![ログ内のメッセージを表示します。](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Azure Stack ストレージ アカウントを作成する

Blob とキューを含む Azure Stack ストレージ アカウントを作成します。

### <a name="storage-blob--data-archiving"></a>ストレージ Blob データのアーカイブ

このストレージ アカウントには、2 つのコンテナーが格納されています。 これらのコンテナーは、アーカイブ データを保持するために使用される 1 つの Blob と、主要オフィスの分散用に割り当てられたデータの処理に使用されるキューです。

上記の手順と設定を使用して、別のストレージ アカウントと Blob コンテナーをアーカイブ ストレージとして作成します。

### <a name="storage-queue--filtered-data-holding"></a>ストレージ キューのフィルター処理済みデータの保持

1.  上記の手順と設定を使用して、新しいストレージ アカウントにアクセスします。

2.  [Storage Account Overview] (ストレージ アカウントの概要) セクションで **[キュー]** を選択します。

3.  **[+ Queue] (+ キュー)** を選択し、**[名前]** フィールドに新しいキューの名前を入力します。

4.  **[OK]** を選択します。

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>キューによってトリガーされる関数の作成

1.  上記の関数の作成セクションの手順を使用して、BLOB トリガーではなくキュー トリガーで追加の Azure Stack 関数を作成します。

2.  次の表で指定されている設定を使用してください。

    | 設定 | 推奨値 | 説明 |
    | ------- | ------- | ------- |
    | 名前 | Function App 内で一意 | このキューによってトリガーされる関数の名前。 |
    | パス | \<上記のストレージの場所からのパス> | 監視されているストレージ内の位置。 キューのファイル名が name パラメーターとしてバインディングで渡されます。 |
    | ストレージ アカウント接続 | Function App 接続 | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。 |

3.  **[作成]** を選択すると、関数が作成されます。

## <a name="test-the-queue-triggered-function"></a>キューによってトリガーされる関数をテストする

1.  Azure Stack ポータルで関数を参照します。 ページの下部にある **[ログ]** を展開し、ログ ストリーミングが一時停止していないことを確認します。

2.  Storage Explorer を開き、このセクションの冒頭で作成したストレージ アカウントに接続します。

3.  ストレージ アカウント、**Blob コンテナー**、以前に作成した Blob を展開します。 **[アップロード]**、**[ファイルのアップロード]** の順に選択します。

    ![Blob コンテナーにファイルをアップロードします。](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  [ファイルのアップロード] ダイアログ ボックスで、[ファイル] フィールドを選択します。 画像ファイルなど、ローカル コンピューター上のファイルを参照して選択し、**[開く]**、**[アップロード]** の順に選択します。

5.  関数ログに戻り、Blob が読み取られたことを確認します。

  **例:**

    ![ログ内のメッセージを表示します。](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>安全に格納およびアクセスされる準拠データ

Azure および Azure Stack Staged Data Analytics とカスタム エンドポイント ディレクティブによって、グローバル企業のデータの格納、処理、分散、アクセスが安全に行われます。 リモート オフィスの従業員と機械のアクティビティ、施設のデータ、およびビジネス メトリックは、企業のポリシーと地域の規制に従って、継続的にコンパイル、格納、コンプライアンスのテスト、および分散が行われます。

## <a name="next-steps"></a>次の手順

- Azure のクラウド パターンの詳細については、「[Cloud Design Pattern (クラウド設計パターン)](https://docs.microsoft.com/azure/architecture/patterns)」を参照してください。