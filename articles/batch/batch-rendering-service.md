---
title: "Azure Batch Rendering サービスを使用してクラウドでレンダリングする | Microsoft Docs"
description: "Azure 仮想マシン上のジョブを Maya から直接、従量課金ベースでレンダリングします。"
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 09/14/2017
ms.author: danlep
ms.openlocfilehash: 47ccbd89d5abf04034196ab735c6740d57099023
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-batch-rendering-service"></a>Batch Rendering サービスの概要

Azure Batch Rendering サービスは、クラウド規模のレンダリング機能を従量課金ベースで提供します。 Batch Rendering サービスは、ジョブのスケジューリングとキューの処理、エラーと再試行の管理、レンダリング ジョブの自動スケーリングを行います。 Batch Rendering サービスでは、[Autodesk Maya](https://www.autodesk.com/products/maya/overview)、[3ds Max](https://www.autodesk.com/products/3ds-max/overview)、[Arnold](https://www.autodesk.com/products/arnold/overview)、[V-Ray](https://www.chaosgroup.com/vray/maya) がサポートされます。 Maya 2017 用の Batch プラグインを使用すると、Azure 上でのレンダリング ジョブをデスクトップから簡単に始めることができます。

Maya および 3ds Max の場合、[Batch Labs](https://github.com/Azure/BatchLabs) デスクトップ アプリケーションまたは [Batch テンプレート CLI](batch-cli-templates.md) を使用してジョブを実行できます。 Azure Batch CLI を使用すると、コードを記述せずに Batch ジョブを実行できます。 代わりに、テンプレート ファイルを使用して、Batch プール、ジョブ、およびタスクを作成することができます。 詳細については、[Azure Batch CLI のテンプレートとファイル転送の使用](batch-cli-templates.md)に関するページを参照してください。


## <a name="supported-applications"></a>サポートされているアプリケーション

Batch Rendering サービスでは現在、以下のアプリケーションがサポートされています。

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold for Maya
- Autodesk Arnold for 3ds Max
- Chaos Group V-Ray for Maya
- Chaos Group V-Ray for 3ds Max

## <a name="prerequisites"></a>前提条件

Batch Rendering サービスを使用するには、以下のアカウントが必要です。

- [Azure アカウント](https://azure.microsoft.com/free/)。
- **Azure Batch アカウント。** Azure Portal で Batch アカウントを作成するためのガイダンスについては、「[Azure Portal で Batch アカウントを作成する](batch-account-create-portal.md)」を参照してください。
- **Azure ストレージ アカウント。** レンダリング ジョブのために使用されるアセットは、Azure Storage に格納されます。 Batch アカウントをセットアップするときに、ストレージ アカウントを自動的に作成できます。 既存のストレージ アカウントを使用することもできます。 ストレージ アカウントの詳細については、[Azure Portal でストレージ アカウントを作成、管理、削除する方法](https://docs.microsoft.com/azure/storage/storage-create-storage-account)に関するページを参照してください。

Maya 用の Batch プラグインを使用するには、以下のアプリケーションが必要です。

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview)。
- サポートされているレンダラー (たとえば、Arnold for Maya、V-Ray for Maya)。

## <a name="basic-batch-concepts"></a>Batch の基本的な概念

Batch Rendering サービスを使い始める前に、コンピューティング ノード、プール、ジョブなど、いくつかの Batch 概念について理解しておくことをお勧めします。 Azure Batch の一般的な詳細については、「[Batch で並列ワークロードを本質的に実行する](batch-technical-overview.md)」を参照してください。

### <a name="pools"></a>プール

Batch は、レンダリングなどのコンピューティング集中型の作業を**コンピューティング ノード**の**プール**上で実行するためのプラットフォーム サービスです。 プール内の各コンピューティング ノードは、Linux または Windows を実行している Azure 仮想マシン (VM) です。 

Batch プールとコンピューティング ノードの詳細については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」の「[プール](batch-api-basics.md#pool)」と「[コンピューティング ノード](batch-api-basics.md#compute-node)」のセクションを参照してください。

### <a name="jobs"></a>ジョブ

Batch **ジョブ**は、プール内のコンピューティング ノード上で実行されるタスクのコレクションです。 レンダリング ジョブを送信すると、Batch がジョブをタスクに分割し、タスクをプール内のコンピューティング ノードに分散して実行します。

[Azure Portal](https://ms.portal.azure.com/) を使用して、アプリケーション ログをダウンロードし、RDP または SSH を使って個々の VM にリモートで接続することによって、ジョブを監視して失敗したタスクを診断できます。 [Batch Labs クライアント](https://github.com/Azure/BatchLabs)を使用して管理、監視、デバッグを行うこともできます。

Batch ジョブの詳細については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」の「[ジョブ](batch-api-basics.md#job)」セクションを参照してください。

## <a name="options-for-provisioning-required-applications"></a>必要なアプリケーションをプロビジョニングするためのオプション

必要に応じて、Maya と Arnold、3ds Max と V-Ray、その他のサードパーティのプラグインの組み合わせなど、複数のアプリケーションがジョブのレンダリングに必要になる場合があります。 さらに、お客様によっては、これらのアプリケーションの特定のバージョンが求められる場合があります。 そのため、必要なアプリケーションとソフトウェアをプロビジョニングするための方法は複数あります。

### <a name="pre-configured-vm-images"></a>事前構成済みの VM イメージ

Azure には、単一バージョンの Maya、3ds Max、Arnold、V-Ray がプレインストール済みですぐに使用できるようになっている Windows および Linux イメージが用意されています。 これらのイメージは、プールを作成するときに [Azure Portal](https://portal.azure.com)、Maya プラグイン、または [Batch Labs](https://github.com/Azure/BatchLabs) で選択できます。

Azure Portal と Batch Labs では、アプリケーションがプレインストール済みのいずれかの VM イメージをインストールできます。そのためには、Batch アカウントの [プール] セクションで、**[新規]** を選択します。次に、**[プールの追加]** で、**[イメージの種類]** ボックスの一覧の **[グラフィックスとレンダリング (Linux/Windows)]** を選択します。

![Batch アカウントのイメージの種類を選択する](./media/batch-rendering-service/add-pool.png)

下へスクロールして **[グラフィックスとレンダリングのライセンス]** をクリックし、**[Choose licenses]\(ライセンスの選択\)** ブレードを開いて 1 つまたは複数のソフトウェア ライセンスを選択します。

![プールのグラフィックスとレンダリングのライセンスを選択する](./media/batch-rendering-service/graphics-licensing.png)

提供される特定のライセンス バージョンは次のとおりです。

- Maya 2017
- 3ds Max 2018
- Arnold for Maya 5.0.1.1
- Arnold for 3ds Max 1.0.836
- V-Ray for Maya 3.52.03
- V-Ray for 3ds Max 3.60.01

### <a name="custom-images"></a>カスタム イメージ

Azure Batch では、独自のカスタム イメージを指定することができます。 このオプションを使用すると、必要な目的のアプリケーションと特定のバージョンで VM を構成できます。 詳細については、[カスタム イメージを使用して仮想マシンのプールを作成する](https://docs.microsoft.com/en-us/azure/batch/batch-custom-images)方法に関するページを参照してください。 Autodesk と Chaos Group では Microsoft 独自のライセンス サービスに対する検証を行うために Arnold と V-Ray をそれぞれ修正していることに注意してください。 所有しているアプリケーションがこのサポートに対応するバージョンであることを確認する必要があります。そうでないと、従量課金ライセンスが動作しません。 現在の公開済みバージョンはヘッドレス (バッチ/コマンドライン モード) で実行するときにライセンス サーバーを必要としないため、このライセンス検証は Maya または 3ds Max では必要ありません。 このオプションの使用方法がわからない場合は、Azure サポートにお問い合わせください。

## <a name="options-for-submitting-a-render-job"></a>レンダリング ジョブを送信するためのオプション

使用する 3D アプリケーションに応じて、レンダリング ジョブをサービスに送信するためのさまざまなオプションがあります。

### <a name="maya"></a>Maya

Maya では、以下を使用できます。

- [Maya 用の Batch プラグイン](https://docs.microsoft.com/en-us/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- [Batch Labs](https://github.com/Azure/BatchLabs) デスクトップ アプリケーション
- [Batch テンプレート CLI](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

3ds Max では、以下を使用できます。

- [Batch Labs](https://github.com/Azure/BatchLabs) デスクトップ アプリケーション (3ds Max Batch Labs テンプレートの使用に関するガイダンスについては、[Batch Labs のデータ](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax)に関するページを参照してください)
- [Batch テンプレート CLI](batch-cli-templates.md)

3ds Max Batch Labs テンプレートを使用すると、Azure Batch Rendering サービスを使用して V-Ray および Arnold のシーンをレンダリングできます。 V-Ray と Arnold のテンプレートは 2 種類あります。1 つは、標準のシーン用で、もう 1 つはアセットおよびテクスチャへの 3ds Max パス ファイル (.mxp ファイル) を必要とする複雑なシーン用です。 3ds Max Batch Labs テンプレートの詳細については、GitHub の [Batch Labs データ](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) リポジトリを参照してください。

さらに、[Batch Python SDK](https://docs.microsoft.com/en-us/azure/batch/batch-python-tutorial) を使用して、レンダリング サービスを既存のパイプラインと統合することができます。


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Maya 用の Batch プラグインを使用したレンダリング ジョブの送信

Maya 用の Batch プラグインを使用すると、Maya から直接 Batch Rendering サービスにジョブを送信できます。 以降のセクションでは、プラグインからジョブを構成して、それを送信する方法について説明します。 

### <a name="load-the-batch-plug-in-for-maya"></a>Maya 用の Batch プラグインを読み込む

Batch プラグインは、[GitHub](https://github.com/Azure/azure-batch-maya/releases) で入手できます。 アーカイブを任意のディレクトリに解凍します。 *azure_batch_maya* ディレクトリからプラグインを直接読み込むことができます。

プラグインを Maya に読み込むには:

1. Maya を実行します。
2. **[ウィンドウ]** > **[設定/プリファレンス]** > **[プラグイン マネージャ]** の順に開きます。
3. **[参照]**をクリックします。
4. *azure_batch_maya/plug-in/AzureBatch.py* に移動して選択します。

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Batch アカウントとストレージ アカウントへのアクセスの認証を行う

プラグインを使用するには、Azure Batch アカウント キーと Azure ストレージ アカウント キーを使用して認証を行う必要があります。 アカウント キーを取得するには、次のように操作します。

1. Maya でプラグインを表示し、**[Config]\(構成\)** タブを選択します。
2. [Azure Portal](https://portal.azure.com) に移動します。
3. 左側のメニューで **[Batch アカウント]** を選択します。 必要に応じて、**[その他のサービス]** をクリックし、_Batch_ でフィルター処理します。
4. 一覧で目的の Batch アカウントを見つけます。
5. **[キー]** メニュー項目を選択し、アカウント名、アカウント URL、アクセス キーを表示します。
    - Batch アカウント URL を Batch プラグインの **[Service]\(サービス\)** フィールドに貼り付けます。
    - アカウント名を **[Batch Account]\(Batch アカウント\)** フィールドに貼り付けます。
    - プライマリ アカウント キーを **[Batch Key]\(Batch キー\)** フィールドに貼り付けます。
7. 左側のメニューで [ストレージ アカウント] を選択します。 必要に応じて、**[その他のサービス]** をクリックし、"_ストレージ_" でフィルター処理します。
8. 一覧で目的のストレージ アカウントを見つけます。
9. **[アクセス キー]** メニュー項目を選択し、ストレージ アカウント名とキーを表示します。
    - ストレージ アカウント名を Batch プラグインの **[Storage Account]\(ストレージ アカウント\)** フィールドに貼り付けます。
    - プライマリ アカウント キーを **[Storage Key]\(ストレージ キー\)** フィールドに貼り付けます。
10. **[Authenticate]\(認証\)** をクリックして、プラグインが両方のアカウントにアクセスできることを確認します。

認証が正常に行われると、プラグインによって状態フィールドが **[Authenticated]\(認証済み\)** に設定されます。 

![Batch アカウントとストレージ アカウントの認証](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>レンダリング ジョブのプールを構成する

Batch アカウントとストレージ アカウントの認証を行った後、レンダリング ジョブ用のプールをセットアップします。 この選択は、セッション終了後もプラグインによって保存されます。 望ましい構成をセットアップした後は、変化が生じない限り、変更する必要はありません。

以降のセクションでは、**[Submit]\(送信\)** タブで使用可能なオプションについて具体的に説明します。

#### <a name="specify-a-new-or-existing-pool"></a>新規または既存のプールを指定する

レンダリング ジョブを実行するプールを指定するには、**[Submit]\(送信\)** タブを選択します。このタブには、プールを作成するか、既存のプールを選択するためのオプションがあります。

- **このジョブのプールを自動プロビジョニング**できます (既定のオプション)。 このオプションを選択すると、Batch によって現在のジョブ専用のプールが作成され、レンダリング ジョブが完了すると、プールが自動的に削除されます。 実行するレンダリング ジョブが 1 つである場合は、このオプションが最適です。
- **既存の永続的なプールを再利用**できます。 アイドル状態である既存のプールがある場合は、ドロップダウンからそのプールを選択して、レンダリング ジョブを実行するように指定することができます。 既存の永続的なプールを再利用すると、プールのプロビジョニングに必要な時間を節約できます。  
- **新しい永続的なプールを作成**できます。 このオプションを選択すると、ジョブを実行するための新しいプールが作成されます。 ジョブが完了してもプールは削除されないため、以降のジョブで再利用することができます。 レンダリング ジョブを継続的に実行する必要がある場合は、このオプションを選択します。 後続のジョブでは、**既存の永続的なプールを再利用する**ことを選択して、最初のジョブのために作成した永続的なプールを使用することができます。

![プール、OS イメージ、VM サイズ、ライセンスの指定](./media/batch-rendering-service/submit.png)

Azure VM で料金が発生するしくみの詳細については、[Linux の価格の FAQ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) に関するページと [Windows の価格の FAQ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq) に関するページを参照してください。

#### <a name="specify-the-os-image-to-provision"></a>プロビジョニングする OS イメージを指定する

プール内のコンピューティング ノードのプロビジョニングに使用する OS イメージの種類を、**[Env]\(環境\)** タブで指定することができます。Batch では現在、レンダリング ジョブに対して以下のイメージ オプションがサポートされています。

|オペレーティング システム  |イメージ  |
|---------|---------|
|Linux     |Batch CentOS プレビュー |
|Windows     |Batch Windows プレビュー |

#### <a name="choose-a-vm-size"></a>VM サイズを選択する

VM サイズを **[Env]\(環境\)** タブで指定することができます。使用可能な VM サイズの詳細については、「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)」と「[Azure の Windows 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)」を参照してください。 

![[Env]\(環境\) タブでの VM OS イメージとサイズの指定](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>ライセンス オプションを指定する

使用するライセンスを **[Env]\(環境\)** タブで指定できます。次のオプションがあります。

- **Maya**。既定で有効になっています。
- **Arnold**。Arnold が Maya でのアクティブなレンダリング エンジンとして検出される場合に有効になります。

 独自のライセンスを使用してレンダリングする場合は、テーブルに適切な環境変数を追加することによって、ライセンス エンド ポイントを構成できます。 そうする場合は、既定のライセンス オプションの選択を解除します。

> [!IMPORTANT]
> VM が現在レンダリングに使用されていない場合でも、プールで VM が実行されている間は、ライセンスの使用に対して料金が発生します。 余分な課金を避けるには、**[Pools]\(プール\)** タブに移動し、別のレンダリング ジョブを実行するまで、プールのサイズを 0 ノードに変更します。 
>
>

#### <a name="manage-persistent-pools"></a>永続的なプールを管理する

既存の永続的なプールを、**[Pools]\(プール\)** タブで管理できます。一覧でプールを選択すると、プールの現在の状態が表示されます。

**[Pools]\(プール\)** タブで、プールを削除したり、プール内の VM の数を変更したりすることもできます。 ワークロード間のコストの発生を避けるために、プールのサイズを 0 ノードに変更することができます。

![プールの表示、サイズ変更、削除](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>レンダリング ジョブを送信用に構成する

レンダリング ジョブを実行するプールのパラメーターを指定し終わったら、ジョブ自体を構成します。 

#### <a name="specify-scene-parameters"></a>シーンのパラメーターを指定する

Batch プラグインは、現在 Maya でどのレンダリング エンジンが使用されているかを検出し、適切なレンダリング設定を **[Submit]\(送信\)** タブに表示します。設定は、開始フレーム、終了フレーム、出力プレフィックス、フレーム ステップなどです。 プラグインで別の設定を指定して、シーン ファイルのレンダリング設定を上書きできます。 プラグインの設定に対する変更は、シーン ファイルのレンダリング設定として永続化されないため、シーン ファイルを再アップロードせずに、ジョブごとに設定を変更できます。

Maya で選択したレンダリング エンジンがサポートされていない場合は、ブラグインからの警告が表示されます。

プラグインが開かれている間に新しいシーンを読み込む場合は、**[Refresh]\(更新\)** ボタンをクリックして、設定が更新されていることを確認します。

#### <a name="resolve-asset-paths"></a>アセット パスを解決する

プラグインを読み込むとき、シーン ファイル内の外部ファイル参照がスキャンされます。 これらの参照は、**[Assets]\(アセット\)** タブに表示されます。参照先されているパスが解決できない場合、プラグインは次のようないくつかの既定の場所で、ファイルを探そうとします。

- シーン ファイルの場所 
- 現在のプロジェクトの _sourceimages_ ディレクトリ
- 現在の作業ディレクトリ 

それでもアセットが見つからない場合は、次のように、警告アイコンと共に一覧に表示されます。

![見つからないアセットは警告アイコンと共に表示される](./media/batch-rendering-service/missing_assets.png)

未解決のファイル参照の場所がわかっている場合は、警告アイコンをクリックすると、検索パスを追加するように求めるメッセージが表示されます。 プラグインは、この検索パスを使用して、見つからないアセットを解決しようとします。 任意の数の追加の検索パスを追加することができます。

参照が解決されると、緑のアイコンと共に一覧に表示されます。

![解決されたアセットに緑のアイコンが表示される](./media/batch-rendering-service/found_assets.png)

プラグインによって検出されなかったその他のファイルがシーンに必要な場合は、追加のファイルまたはディレクトリを追加できます。 **[Add Files]\(ファイルの追加\)** ボタンと **[Add Directory]\(ディレクトリの追加\)** ボタンを使用します。 プラグインが開かれている間に新しいシーンを読み込む場合は、**[Refresh]\(更新\)** をクリックして、シーンの参照を更新します。

#### <a name="upload-assets-to-an-asset-project"></a>アセットをアセット プロジェクトにアップロードする

レンダリング ジョブを送信すると、**[Assets]\(アセット\)** タブに表示されている参照ファイルは、Azure Storage にアセット プロジェクトとして自動的にアップロードされます。 **[Assets]\(アセット\)** タブの **[Upload]\(アップロード\)** ボタンを使用して、レンダリング ジョブとは別に、アセット ファイルをアップロードすることもできます。アセット プロジェクト名は **[Project]\(プロジェクト\)** フィールドで指定され、既定では現在の Maya プロジェクトと同じ名前になります。 アセット ファイルがアップロードされるとき、ローカル ファイルの構造は保持されます。 

アップロード後は、任意の数のレンダリング ジョブでアセットを参照できます。 アップロードされたすべてのアセットは、シーンに含まれているかどうかにかかわらず、アセット プロジェクトを参照する任意のジョブで使用できます。 次のジョブによって参照されるアセット プロジェクトを変更するには、**[Assets]\(アセット\)** タブの **[Project]\(プロジェクト\)** フィールドの名前を変更します。アップロードから除外する参照ファイルがある場合は、一覧の横にある緑のボタンを使用して選択を解除します。

#### <a name="submit-and-monitor-the-render-job"></a>レンダリング ジョブを送信および監視する

プラグインでレンダリング ジョブを構成した後、**[Submit]\(送信\)** タブの **[Submit Job]\(ジョブの送信\)** ボタンをクリックして、ジョブを Batch に送信します。

![レンダリング ジョブの送信](./media/batch-rendering-service/submit_job.png)

プラグインの **[Jobs]\(ジョブ\)** タブで、進行中のジョブを監視できます。 ジョブの現在の状態を表示するには、一覧でジョブを選択します。 このタブを使用して、ジョブの取り消しや削除を行ったり、出力やレンダリング ログをダウンロードしたりすることができます。 

出力をダウンロードするには、**[Outputs]\(出力\)** フィールドを変更し、目的のダウンロード先ディレクトリに設定します。 進行中にジョブを監視し、出力をダウンロードするバックグラウンド プロセスを開始するには、歯車アイコンをクリックします。 

![ジョブの状態の表示と出力のダウンロード](./media/batch-rendering-service/jobs.png)

ダウンロード プロセスを中断せずに、Maya を閉じることができます。

## <a name="next-steps"></a>次のステップ

Batch の詳細については、「[Batch で並列ワークロードを本質的に実行する](batch-technical-overview.md)」を参照してください。