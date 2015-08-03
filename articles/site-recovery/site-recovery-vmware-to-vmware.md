<properties 
	pageTitle="オンプレミスの VMWare サイト間の保護の設定" 
	description="この記事を使用して、2 つの VMware サイト間の保護を Azure Site Recovery を使用して構成します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="raynew"/>


# オンプレミスの VMWare サイト間の保護の設定


## 概要

Azure Site Recovery の InMage Scout は、オンプレミスの VMWare サイト間のリアルタイムのレプリケーションを実現します。InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれています。


## 前提条件

- **Azure アカウント** - [Microsoft Azure](http://azure.microsoft.com/) アカウントが必要です。アカウントがなくても、[無料試用版](pricing/free-trial/)を使用できます。


## ステップ 1: コンテナーの作成

1. [管理ポータル](https://portal.azure.com)にサインインします。
2. **[Data Services]**、**[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。
3. **[新規作成]**、**[簡易作成]** の順にクリックします。
4. **[名前]** ボックスに、資格情報コンテナーを識別するフレンドリ名を入力します。
5. **[リージョン]** ボックスで、資格情報コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)](pricing/details/site-recovery/)」で利用可能地域をご覧ください。

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メイン [復旧サービス] ページで、コンテナーは **[アクティブ]** と表示されています。

## 手順 2: コンテナーの構成および InMage Scout コンポーネントのダウンロード

1. **[コンテナーの作成]** をクリックします。
2. **[復旧サービス]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。
3. ドロップダウン リストで、**[2 つのオンプレミス VMWare サイト間]** を選択します。
4. InMage Scout をダウンロードします。必要なすべてのコンポーネントのセットアップ ファイルは、ダウンロードした zip ファイルに含まれています。


## 手順 3: コンポーネント更新プログラムのインストール

最新の[更新プログラム](#updates)について確認します。更新プログラムのファイルは、次の順序でインストールします。

1. RX サーバー (存在する場合)
2. 構成サーバー
3. プロセス サーバー
3. マスター ターゲット サーバー
4. vContinuum サーバー

次のようにインストールします。

1. [update](http://download.microsoft.com/download/9/F/D/9FDC6001-1DD0-4C10-BDDD-8A9EBFC57FDF/ASR Scout 8.0.1 Update1.zip) zip ファイルをダウンロードします。この zip ファイルには、次のファイルが含まれています。

	-  RX_8.0.1.0_GA_Update_1_3279231_23Jun15.tar.gz
	-  CX_Windows_8.0.1.0_GA_Update_1_3259146_23Jun15.exe
	-  UA_Windows_8.0.1.0_GA_Update_1_3259401_23Jun15.exe
	-  UA_RHEL6-64_8.0.1.0_GA_Update_1_3259401_23Jun15.tar.gz
	-  vCon_Windows_8.0.1.0_GA_Update_1_3259523_23Jun15.exe
2. zip ファイルを抽出します。
2. **RX server**: **RX_8.0.1.0_GA_Update_1_3279231_23Jun15.tar.gz** を RX サーバーにコピーし、抽出します。抽出したフォルダで、**/Install** を実行します。
2. **Configuration server/process server**: **CX_Windows_8.0.1.0_GA_Update_1_3259146_23Jun15.exe** を構成サーバーとプロセス サーバーにコピーします。ダブルクリックして実行します。
3. **Windows マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_Windows_8.0.1.0_GA_Update_1_3259401_23Jun15.exe** をマスター ターゲット サーバーにコピーします。ダブルクリックして実行します。Windows 用の統合エージェントは、ソース サーバーでは適用できません。これは、Windows マスター ターゲット サーバーのみにインストールしてください。
4. **Linux マスター ターゲット サーバー**: 統合エージェントを更新するには、**UA_RHEL6-64_8.0.1.0_GA_Update_1_3259401_23Jun15.tar.gz** をマスター ターゲット サーバーにコピーし、抽出します。抽出したフォルダで、**/Install** を実行します。
5. **vContinuum サーバー**: **vCon_Windows_8.0.1.0_GA_Update_1_3259523_23Jun15.exe** を vContinuum サーバーにコピーします。vContinuum ウィザードを閉じたことを確認してください。ファイルをダブルクリックして実行します。

## 手順 4: レプリケーションの設定
5. ソースとターゲットの VMware サイト間にレプリケーションを設定します。
6. 手順については、製品と共にダウンロードされる InMage Scout の資料をご覧ください。または、次から資料にアクセスすることもできます。

	- [リリース ノート](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [互換性対応表](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [ユーザー ガイド](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [RX ユーザー ガイド](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [クイック インストール ガイド](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## 更新プログラム

### ASR Scout 8.0.1 更新プログラム 1

この最新の更新プログラムには、バグ修正と新機能が含まれています。

- サーバー インスタンスあたり、31 日間の無料保護が提供されます。これにより、機能をテストしたり、概念実証を設定したりすることができます。
	- フェールオーバーとフェールバックを含む、サーバー上のすべての操作は、サーバーが ASR Scout を使用して最初に保護された時点から 31 日間は無料で使用できます。
	- 32 日目以降は、お客様が所有するサイトに対して、Azure Site Recovery 保護の標準インスタンスで各保護対象サーバーへの課金が行われます。
	- 現在課金されている保護対象サーバーの数は、いつでも Azure Site Recovery コンテナーのダッシュボード ページで確認できます。
- vCLI 5.5 Update 2 にサポートが追加されました。
- ソース サーバーの Linux オペレーティング システムに追加されたサポートは次のとおりです。
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- 次の問題に対処するバグの修正が提供されます。
	- 構成サーバーまたは RX サーバーに対するコンテナー登録に失敗する。
	- クラスター化された仮想マシンが再開時に保護されている場合、クラスターのボリュームが適切に表示されない。
	- マスター ターゲット サーバーがオンプレミスの運用仮想マシンとは異なる ESXi サーバーでホストされている場合、フェールバックが失敗する。
	- 8.0.1 にアップグレードすると構成ファイルのアクセス許可が変更され、保護や操作に影響が出る。
	- 再同期のしきい値が適切に適用されないため、レプリケーションの動作に不整合が生じる。
	- 構成サーバーのインターフェイスに、RPO 設定が正しく表示されない。圧縮されていないデータ値で圧縮された値が正しく表示されない。
	-  vContinuum ウィザードで削除操作を行っても適切に削除されず、レプリケーションが構成サーバーのインターフェイスから削除されない。
	-  vContinuum ウィザードで、MSCS 仮想マシンの保護中にディスク ビューで **[詳細]** をクリックすると、ディスクが自動的に選択解除される。
	- CIMnotify など、P2V シナリオが必要な HP サービス中に、復元仮想マシンで CqMgHost が手動に移行せず、起動時間が長くなる。
	- マスター ターゲット サーバーに 27 個以上のディスクがある場合、保護されている Linux 仮想マシンが失敗する。
	
## 次のステップ

ご質問があれば、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

<!---HONumber=July15_HO4-->