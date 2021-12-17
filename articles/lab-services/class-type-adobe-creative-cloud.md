---
title: Azure Lab Services を使用した Adobe Creative Cloud のラボの設定 | Microsoft Docs
description: Adobe Creative Cloud を使用するデジタル アートおよびメディア クラスのラボを設定する方法について説明します。
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 3802687b76929bda96e34a9b1cb402096bee40c5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177158"
---
# <a name="set-up-a-lab-for-adobe-creative-cloud"></a>Adobe Creative Cloud のラボを設定する
[Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) は、写真、デザイン、ビデオ、Web、ユーザー エクスペリエンス (UX) などに使用されるデスクトップ アプリケーションと Web サービスのコレクションです。  大学と K-12 教育機関は、デジタル アートおよびメディア クラスで Creative Cloud を使用しています。  一部の Creative Cloud のメディア プロセスでは、一般的なタブレット、ラップトップ、またはワークステーションのサポートよりも多くのコンピューティングと視覚化 (GPU) の性能を必要とする場合があります。  Azure Lab Services によって、GPU サイズを含むさまざまな仮想マシン (VM) サイズから柔軟に選択できます。

この記事では、Creative Cloud を使用するクラスを設定する方法について説明します。

## <a name="licensing"></a>ライセンス
ラボ VM で Creative Cloud を使用するには、[名前付きユーザー ライセンス](https://helpx.adobe.com/enterprise/kb/technical-support-boundaries-virtualized-server-based.html#main_Licensing_considerations)を使用する必要があります。これは、仮想マシンへのデプロイをサポートする唯一のライセンスの種類です。  各ラボ VM にはインターネット アクセスがあるため、学生はソフトウェアにサインインして Creative Cloud アプリをアクティブ化できます。  学生がサインインすると、それぞれの認証トークンがユーザー プロファイルにキャッシュされるため、VM 上で再度サインインする必要がなくなります。  詳細については、[ライセンスに関する Adobe の記事](https://helpx.adobe.com/enterprise/using/licensing.html)を参照してください。

## <a name="lab-configuration"></a>ラボの構成
このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 マーケットプレース イメージを有効にする方法の詳細については、[ラボ作成者が利用できる Marketplace イメージを指定する方法](./specify-marketplace-images.md)に関する記事を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
|Marketplace イメージ| ラボ アカウント内で使用する Windows 10 イメージを有効にします。|

### <a name="lab-settings"></a>ラボの設定

ラボで使用する必要がある VM のサイズは、学生が作成するプロジェクトの種類によって異なります。  ほとんどの [Creative Cloud アプリ](https://helpx.adobe.com/creative-cloud/system-requirements.html) では GPU ベースのアクセラレーションがサポートされており、機能を正常に動作させるために GPU が必要です。  適切な VM サイズを選択できるように、学生が作成するプロジェクトをテストし、適切なパフォーマンスを確認することが推奨されます。  次の表は、Creative Cloud で使用するために推奨される [VM サイズ](./administrator-guide.md#vm-sizing) を示しています。  

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| **小規模 GPU (視覚化)** 。  この VM は、OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。|  
|仮想マシン イメージ| Windows 10 |

> [!NOTE]
> **小規模 GPU (視覚化)** 仮想マシン サイズは、高パフォーマンスのグラフィックス エクスペリエンスを実現するように構成されており、[各アプリケーションの Adobe のシステム要件](https://helpx.adobe.com/creative-cloud/system-requirements.html)を満たします。  小規模 GPU (コンピューティング) ではなく、小規模 GPU (視覚化) を選択してください。  この仮想マシン サイズの詳細については、[GPU を使用したラボの設定方法](./how-to-setup-lab-gpu.md)に関する記事を参照してください。

## <a name="template-virtual-machine-configuration"></a>テンプレート仮想マシンの構成

### <a name="creative-cloud-deployment-package"></a>Creative Cloud 展開パッケージ
Creative Cloud をインストールするには、展開パッケージを使用する必要があります。 通常、展開パッケージは、Adobe の管理コンソールを使用して、IT 部門によって作成されます。  IT が展開パッケージを作成する場合、セルフサービスを有効にするオプションもあります。  展開パッケージのセルフサービスを有効にするには、いくつかの方法があります。
-    セルフサービス パッケージを作成します。
-    セルフサービスの昇格された特権が有効になっているマネージド パッケージを作成します。

セルフサービスが有効になっている場合、アプリの Creative Cloud コレクションの全体をインストールしません。  代わりに、学生は Creative Cloud デスクトップ アプリを使用して、自分でアプリをインストールすることができます。  このアプローチの主な利点を次に示します。
- Creative Cloud インストールの全体は約 25 GB です。  学生がオンデマンドで必要なアプリのみをインストールすると、ディスク領域を最適化するのに役立ちます。 ラボ VM のディスクサイズは 128 GB です。
- 発行前に、テンプレート VM にアプリのサブセットをインストールすることを選択できます。  これにより、学生の VM には既定で一部のアプリがインストールされ、学生は必要に応じて自分でアプリを追加できます。
- 学生はラボの有効期間中、いつでも VM に追加のアプリをインストールできるため、テンプレート VM の再発行を回避できます。  さもないと、IT や教師がテンプレート VM に追加のアプリをインストールして、再発行することが必要になる場合があります。  再発行によって、学生の VM がリセットされ、外部に保存されていないすべての作業が失われます。

セルフサービスが無効になっているマネージド展開パッケージを使用する場合、学生は独自のアプリをインストールすることができません。  この場合、IT がインストールする Creative Cloud アプリを指定する必要があります。

詳細については、[Adobe のパッケージを作成する手順](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/create-nul-packages.ug.html)を参照してください。

### <a name="install-creative-cloud"></a>Creative Cloud のインストール
テンプレートマシンが作成されたら、以下の手順に従って、Creative Cloud でラボのテンプレート仮想マシン (VM) を設定します。
1. テンプレート VM を起動し、RDP を使用して接続します。
1. Creative Cloud をインストールするには、与えられた展開パッケージをダウンロードするか、[Adobe の管理コンソール](https://adminconsole.adobe.com/)から直接ダウンロードします。
1. 展開パッケージ ファイルを実行します。  セルフサービスが有効になっているか無効になっているかに応じて、これにより、Creative Cloud デスクトップまたは指定された Creative Cloud アプリがインストールされます。
詳細については、[Adobe のデプロイ手順](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/deploy-packages.ug.html)を参照してください。
1. テンプレート VM が設定されたら、ラボですべての学生の VM を作成するために使用される[テンプレート VM のイメージを発行](how-to-create-manage-template.md)します。

### <a name="storage"></a>Storage
前述のように、Azure Lab VM のディスク サイズは 128 GB です。  学生が大きなメディア アセットを保存するために追加のストレージを必要とする場合、または共有メディア アセットにアクセスする必要がある場合は、外部ファイル ストレージの使用を検討してください。  詳細については、次の記事を参照してください。
-    [Lab Services で 外部ファイル ストレージを使用する](how-to-attach-external-storage.md)
-    [OneDrive をインストールして構成する](./how-to-prepare-windows-template.md#install-and-configure-onedrive)

### <a name="save-template-vm-image"></a>テンプレート VM イメージの保存
将来使用するためにテンプレート VM を保存することを検討してください。  テンプレート VM を保存するには、「[Shared Image Gallery に既存のイメージを保存する](./how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)」を参照してください。
- セルフサービスが *有効になっている* 場合、テンプレート VM のイメージによって Creative Cloud デスクトップがインストールされます。  教師はこのイメージを再利用して、ラボを作成し、インストールする Creative Cloud アプリを選択できます。  これにより、教師は個別にラボを設定し、クラスに必要な Creative Cloud アプリのインストールを完全に制御できるため、IT のオーバーヘッドが軽減されます。
- セルフサービスが *無効になっている* 場合、テンプレート VM のイメージによって、既に指定された Creative Cloud アプリがインストールされます。  教師はこのイメージを再利用して、ラボを作成できますが、追加の Creative Cloud アプリをインストールすることはできません。

## <a name="cost"></a>コスト

このセクションでは、このクラスの予想されるコスト見積もりについて説明します。  予定されているクラス時間が 20 時間の、25 人の学生のクラスを使用します。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。 ここで選択した仮想マシンのサイズは **小規模 GPU (視覚化)** であり、160 ラボ ユニットです。

25 人の学生 \* (予定された 20 時間 + 割り当てられた 10 時間) \* 160 ラボ ユニット * 0.01 USD/時間 = 1200.00 USD

>[!IMPORTANT]
> コストの見積もりはあくまでも例です。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。  

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートの作成と管理](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)