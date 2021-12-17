---
title: Azure Stack Edge Pro GPU デバイスに更新プログラムをインストールする | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイスの Azure portal とローカル Web UI、およびデバイス上の Kubernetes クラスターを使用して更新プログラムを適用する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 13fc1ad892509d04750e51b593cc7aaf07f0c91a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082932"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU を更新する 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、GPU 搭載の Azure Stack Edge Pro にローカル Web UI および Azure portal を使用して更新プログラムをインストールするために必要な手順について説明します。 ソフトウェアの更新プログラムまたは修正プログラムを適用して、Azure Stack Edge Pro デバイスと、デバイス上の関連する Kubernetes クラスターを最新の状態に保ちます。

この記事で説明されている手順は別バージョンのソフトウェアで実行されましたが、現行バージョンのソフトウェアでもプロセスは同じです。

## <a name="about-latest-update"></a>最新の更新プログラムについて

現在の更新プログラムは、更新プログラム 2110 です。 この更新プログラムでは、デバイスの更新プログラム、Kubernetes の更新プログラムの順に、2 つの更新プログラムがインストールされます。 この更新プログラムに関連付けられているバージョンは次のとおりです。

- デバイス ソフトウェアのバージョン - **2.2.1758.4034**
- Kubernetes サーバーのバージョン - **v1.20.9**
- IoT Edge のバージョン: **0.1.0-beta15**
- GPU ドライバー バージョン: **460.32.03**
- CUDA バージョン: **11.2**

この更新プログラムに含まれる新機能については、[リリース ノート](azure-stack-edge-gpu-2110-release-notes.md)に関する記事を参照してください。

**2110 更新プログラムを適用するには、デバイスで 2106 が実行されている必要があります。** 

- サポートされる最小バージョンを実行していない場合は、*Update package cannot be installed as its dependencies are not met (依存関係が満たされていないため、更新プログラム パッケージをインストールできません)* いうエラーが表示されます。 
- 古いバージョンから 2106 に更新し、その後 2110 をインストールすることができます。

更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 Azure Stack Edge Pro GPU が単一ノード デバイスである場合、進行中のすべての I/O が中断され、更新のためにデバイスで最大 1.5 時間のダウンタイムが発生します。

デバイスに更新プログラムをインストールするには、次の手順を実行する必要があります。

1. 更新サーバーの場所を構成します。 
1. Azure portal UI またはローカル Web UI を使用して更新プログラムを適用します。

以下のセクションでは、これらの各手順について説明します。

## <a name="configure-update-server"></a>更新サーバーを構成する

1. ローカル Web UI で **[構成]**  >  **[Update server]\(更新サーバー\)** に移動します。
   
    ![更新プログラムを構成する 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. **[Select update server type]\(更新サーバーの種類を選択する\)** で、ドロップダウン リストから Microsoft Update サーバー (既定値) または Windows Server Update Services を選択します。  
   
    Windows Server Update Services から更新する場合は、サーバーの URI を指定します。 この URI のサーバーによって、このサーバーに接続されているすべてのデバイスに更新プログラムが展開されます。

    ![更新プログラムを構成する 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS サーバーは、管理コンソールを使用して更新プログラムを管理および配布するために使用されます。 WSUS サーバーには、組織内で他の WSUS サーバーの更新ソースことができます。 更新プログラムの供給元として機能する WSUS サーバーは、"アップストリーム サーバー" と呼ばれます。 WSUS 実装では、ネットワーク上の少なくとも 1 つの WSUS サーバーは、利用可能な更新情報を取得する Microsoft Update に接続できる必要があります。 管理者は、指定できます - ネットワーク セキュリティと構成 - に基づくその他の WSUS サーバーの数に直接接続 Microsoft Update。
    
    詳細については、「[Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)」を参照してください。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal から更新プログラムをインストールすることをお勧めします。 デバイスでは 1 日に 1 回、更新プログラムのスキャンが自動的に行われます。 更新プログラムが利用可能になると、ポータルに通知が表示されます。 その後、更新プログラムをダウンロードしてインストールすることができます。

> [!NOTE]
> デバイスが正常であり、状態が **[デバイスは正常に動作しています]** と表示されることを、 更新プログラムのインストールを続行する前に確認してください。

実行しているソフトウェアのバージョンによっては、インストール プロセスが若干異なる場合があります。 

- 2106 から 2110 に更新する場合は、ワンクリックでインストールできます。 手順については、「**バージョン 2106**」のタブを参照してください。
- 2110 より前のバージョンに更新する場合は、2 回のクリックでインストールできます。 手順については、「**バージョン 2105 以前**」のタブを参照してください。

### <a name="version-2106"></a>[バージョン 2106](#tab/version-2106)

[!INCLUDE [azure-stack-edge-install-2110-updates](../../includes/azure-stack-edge-install-2110-updates.md)]

### <a name="version-2105-and-earlier"></a>[バージョン 2105 以前](#tab/version-2105-and-earlier)

1. デバイスの更新プログラムが利用可能になると、Azure Stack Edge リソースの **[概要]** ページに通知が表示されます。 通知を選択するか、上部のコマンド バーで **[デバイスの更新]** を選択します。 これにより、デバイスのソフトウェア更新プログラムが適用できるようになります。

    ![更新後のソフトウェア バージョン](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. **[デバイスの更新プログラム]** ブレードで、リリース ノートの新機能に関連付けられているライセンス条項を確認したことを示すチェック ボックスをオンにします。

    更新プログラムを **ダウンロードしてインストール** するか、単に更新プログラムを **ダウンロード** するかを選択できます。 後でこれらの更新プログラムのインストールを選択できます。

    ![更新後のソフトウェア バージョン 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    更新プログラムをダウンロードしてインストールする場合は、ダウンロードの完了後に更新プログラムが自動的にインストールされるオプションをオンにします。

    ![更新後のソフトウェア バージョン 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. 更新プログラムのダウンロードが開始されます。 ダウンロードが進行中であることが通知されます。

    ![更新後のソフトウェア バージョン 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    通知バナーは Azure portal にも表示されます。 これは、ダウンロードの進行状況を示します。

    ![更新後のソフトウェア バージョン 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    更新の詳細な状態を確認するには、この通知を選択するか、 **[デバイスの更新]** を選択します。

    ![更新後のソフトウェア バージョン 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. ダウンロードが完了すると、通知バナーが更新され、完了が示されます。 更新プログラムをダウンロードしてインストールすることを選択した場合は、インストールが自動的に開始されます。

    更新プログラムのダウンロードのみを選択した場合は、通知を選択して **[デバイスの更新プログラム]** ブレードを開きます。 **[インストール]** を選択します。
  
5. インストールが進行中であることが通知されます。 ポータルには、インストールが進行中であることを示す情報アラートも表示されます。 デバイスがオフラインになり、メンテナンス モードになります。
   
    ![更新後のソフトウェア バージョン 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. これは 1 ノードのデバイスなので、更新プログラムのインストール後にデバイスが再起動されます。 再起動中の重大なアラートは、デバイスのハートビートが失われたことを示しています。

    ![更新後のソフトウェア バージョン 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    アラートを選択して、対応するデバイス イベントを確認します。
    
    ![更新後のソフトウェア バージョン 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. 再起動後、デバイス ソフトウェアの更新が完了します。 更新が完了したら、デバイス ソフトウェアが更新されたことをローカル Web UI から確認できます。 Kubernetes ソフトウェアのバージョンが更新されていません。

    ![更新後のソフトウェア バージョン 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. デバイスの更新プログラムが利用可能であることを示す通知バナーが表示されます。 このバナーを選択して、デバイス上の Kubernetes ソフトウェアの更新を開始します。 

    ![更新後のソフトウェア バージョン 13a](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![更新後のソフトウェア バージョン 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    上部のコマンド バーから **[デバイスの更新]** を選択すると、更新プログラムの進行状況を確認できます。  

    ![更新後のソフトウェア バージョン 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. 更新プログラムのインストール後に、デバイスの状態が **[デバイスは正常に動作しています]** に更新されます。 

    ![更新後のソフトウェア バージョン 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    ローカル Web UI にアクセスして、 **[ソフトウェア更新プログラム]** ページにアクセスします。 Kubernetes の更新プログラムが正常にインストールされたこと、およびソフトウェアのバージョンにそれが反映されていることを確認します。

    ![更新後のソフトウェア バージョン 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


デバイス ソフトウェアと Kubernetes の更新プログラムが正常にインストールされると、バナー通知は表示されなくなります。 

---

デバイスのデバイス ソフトウェアと Kubernetes が最新バージョンになりました。


## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する

ローカル Web UI を使用するときは、2 つの手順があります。

* 更新プログラムまたは修正プログラムをダウンロードする
* 更新プログラムまたは修正プログラムをインストールする

以下のセクションでは、これらの各手順について詳しく説明します。


### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする

次の手順を行い、更新プログラムをダウンロードします。 更新プログラムは、Microsoft が提供する場所または Microsoft Update カタログからダウンロードできます。


次の手順を行い、Microsoft Update カタログから更新プログラムをダウンロードします。

1. ブラウザーを起動し、[https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。

    ![カタログの検索](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号または更新プログラムの用語を入力します。 たとえば、「**Azure Stack Edge**」と入力し、 **[検索]** をクリックします。
   
    更新プログラムの一覧に **Azure Stack Edge Update 2110** と表示されます。
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. **[ダウンロード]** を選択します。 ダウンロードするパッケージが 2 つあります。 <!--KB 4616970 and KB 4616971--> 1 つはデバイス ソフトウェア更新プログラム (*SoftwareUpdatePackage.exe*) 用で、もう 1 つは Kubernetes 更新プログラム (*Kubernetes_Package.exe*) 用です。 パッケージをローカル システム上のフォルダーにダウンロードします。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする

更新プログラムまたは修正プログラムをインストールする前に、次のことを確認してください。

 - 更新プログラムまたは修正プログラムがローカルでホストにダウンロードされている、またはネットワーク共有を介してアクセスできる。
 - ローカル Web UI の **[概要]** ページに示されているように、デバイスの状態が正常であること。

   ![デバイスの更新](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

この手順の所要時間は約 20 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

1. ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 実行しているソフトウェアのバージョンをメモしておきます。 
   
   ![デバイスの更新 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 更新プログラム ファイルのパスを指定します。 更新プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 *SoftwareUpdatePackage.exe* サフィックスが付いたソフトウェア更新プログラム ファイルを選択します。

   ![デバイスの更新 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. **[更新の適用]** を選択します。

   <!--![update device 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)-->

4. 確認を求められたら、 **[はい]** を選択して続行します。 デバイスは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。 
   
   ![デバイスの更新 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。
   
6. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 現行リリースについては、表示されるソフトウェア バージョンは **Azure Stack Edge 2110** になるはずです。 


7. 次に、Kubernetes ソフトウェアのバージョンを更新します。 上記の手順を繰り返します。 *Kubernetes_Package.exe* のサフィックスが付いた Kubernetes 更新プログラム ファイルのパスを指定します。  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. **[更新の適用]** を選択します。

   <!--![update device 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)-->

9. 確認を求められたら、 **[はい]** を選択して続行します。

10. Kubernetes の更新プログラムが正常にインストールされた後は、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に表示されるソフトウェアに変更はありません。

    ![デバイスの更新 6](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro の管理](azure-stack-edge-manage-access-power-connectivity-mode.md)について確認します。
