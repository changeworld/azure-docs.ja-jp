<properties 
	pageTitle="オンプレミスの VMWare サイト間でのデプロイ" 
	description="Azure Site Recovery の InMage Scout は、オンプレミスの VMWare サイト間でのレプリケーション、フェールオーバー、回復を実現します。" 
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
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# オンプレミスの VMWare サイト間でのデプロイ


##概要

Azure Site Recovery の InMage Scout は、オンプレミスの VMWare サイト間のリアルタイムのレプリケーションを実現します。InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれます。


## 前提条件

- **Azure アカウント** - [Microsoft Azure](http://azure.microsoft.com/) アカウントが必要です。アカウントがなくても、[無料試用版](pricing/free-trial/)を使用できます。


##手順 1: 資格情報コンテナーの作成と InMage Scout のダウンロード

1. [管理ポータル](https://portal.azure.com)にサインインします。
2. **[Data Services]**、**[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。
3. **[新規作成]**、**[簡易作成]** の順にクリックします。
4. **[名前]** ボックスに、資格情報コンテナーを識別するフレンドリ名を入力します。
5. **[リージョン]** ボックスで、資格情報コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の料金の詳細)](pricing/details/site-recovery/)」で利用可能地域をご覧ください。

<P>ステータス バーを確認して、資格情報コンテナーが正常に作成されたことを確かめます。資格情報コンテナーは、Recovery Services のメイン ページに <b>[アクティブ]</b> として表示されます。</P>

##手順 2: 資格情報コンテナーの構成

1. **[コンテナーの作成]** をクリックします。
2. **[復旧サービス]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。
3. ドロップダウン リストで、**[2 つの内部設置型 VMWare サイト間]** を選択します。
4. InMage Scout をダウンロードします。
5. 製品と共にダウンロードされる InMage Scout のドキュメントを参照して、2 つの VMWare サイト間のレプリケーションを設定します。


##次のステップ

ご質問があれば、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

<!---HONumber=July15_HO2-->