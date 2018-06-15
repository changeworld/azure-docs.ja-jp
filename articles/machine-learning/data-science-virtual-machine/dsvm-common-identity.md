---
title: データ サイエンス仮想マシンの共通の ID のセットアップ - Azure | Microsoft Docs
description: エンタープライズ チームの DSVM 環境で共通の ID をセットアップします。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 70c6b8cd147cefaa3128bc1e6a414a6fa61dba6d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830896"
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>データ サイエンス VM に共通の ID をセットアップする

既定で、データ サイエンス VM (DSVM) を含む Azure VM では、VM のプロビジョニング中にローカル ユーザー アカウントが作成され、ユーザーはこれらの資格情報で VM に対して認証します。 アクセスする必要のある複数の VM がある場合、このアプローチはすぐに資格情報の管理が面倒になりがちです。 共通のユーザー アカウントと標準ベースの ID プロバイダーを使用した管理により、単一の資格情報セットを使用して、複数の DSVM を含む Azure 上の複数のリソースにアクセスできます。 

Active Directory (AD) は、一般的な ID プロバイダーであり、オンプレミスと同様に、サービスとして Azure でもサポートされます。 AD または Azure AD を利用して、スタンドアロンのデータ サイエンス VM (DSVM) または Azure 仮想マシン スケール セットでの DSVM のクラスターのどちらでもユーザーを認証できます。 これを行うには、DSVM インスタンスを AD ドメインに結合します。 ID を管理する Active Directory が既にある場合は、それを共通の ID プロバイダーとして使用できます。 AD がない場合は、[Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) というサービスを通じて Azure で管理された AD を実行できます。 

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) のドキュメントで、Azure AD とオンプレミス ディレクトリ (ある場合) の接続など、Active Directory を管理するための詳細な[手順](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity)を説明しています。 

この記事の残りの部分では、Azure AD DS を使用して Azure 上に完全に管理された AD ドメイン サービスをセットアップし、DSVM を管理された AD ドメインに参加させ、ユーザーが共通のユーザー アカウントと資格情報を使用して、DSVM (およびその他の Azure リソース) のプールにアクセスできるようにする手順について説明します。 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Azure 上に完全に管理された Active Directory ドメインをセットアップする

Azure AD DS により、Azure 上で完全に管理されたサービスを提供して、ID を簡単に管理できます。 この Active directory ドメインで、ユーザーとグループが管理されます。  Azure でホストされる AD ドメインとユーザー アカウントをディレクトリにセットアップする手順は次のようになります。

1. ポータル上の Active Directory にユーザーを追加します 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントでサインインします。
    
    b. **[Azure Active Directory]** を選択し、**[ユーザーとグループ]** を選択します。
    
    c. **[ユーザーとグループ]** で、**[すべてのユーザー]** を選択し、**[新しいユーザー]** を選択します。
   
   ![[追加] をクリックする](./media/add-user.png)
    
    d. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 "[ドメイン名].onmicrosoft.com"、または検証済みの非フェデレーション [カスタム ドメイン名](../../active-directory/add-custom-domain.md) ("contoso.com" など) のいずれかである必要があります。
    
    e. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
    
    f. 必要に応じて、ユーザーの **[プロファイル]**、**[グループ]**、または **[ディレクトリ ロール]** を開き、情報を入力します。 
    
    g. **[ユーザー]** で、**[作成]** を選択します。
    
    h. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

2.  Azure AD Domain Services を作成します

    Azure ADDS を作成するには、記事「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)」の手順 (タスク 1 からタスク 5) を参照してください。 Azure AD DS 内のパスワードが同期されるように、Active Directory 内の既存のユーザー パスワードが更新されることが重要です。 さらに、上記の記事のタスク番号 4 に示されているように、DNS を Azure AD DS に追加することも重要です。 

3.  先述の手順のタスク番号 2 で作成された仮想ネットワークに個別の DSVM サブネットを作成します
4.  DSVM サブネットに 1 つまたは複数のデータ サイエンス VM インスタンスを作成します 
5.  [手順](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )に従って、DSVM を AD に追加します。 
6.  次に、ホームまたはノートブック ディレクトリをホストするための共有 Azure ファイルをマウントし、任意のマシンにワークスペースをマウントできるようにします。 (厳格なファイル レベルのアクセス許可が必要な場合は、1 つまたは複数の VM で NFS を実行する必要があります)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 [Azure ファイル共有を作成します](../../storage/files/storage-how-to-create-file-share.md)
    
    b. それを Linux DSVM にマウントします。 Azure Portal のストレージ アカウントで Azure ファイルの [接続] ボタンをクリックすると、Linux DSVM 上の bash シェルで実行するコマンドが表示されます。 コマンドは次のようになります。
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  /Data/workspace に Azure ファイルをマウントしたとします。 共有内に各ユーザーのディレクトリを作成します。 /data/workspace/user1、/data/workspace/user2 などです。 各ユーザーのワークスペースに、```notebooks``` ディレクトリを作成します。 
8. ```$HOME/userx/notebooks/remote``` に ```notebooks``` のシンボリック リンクを作成します。   

これで、Azure でホストされる Active Directory 内にユーザーが作成され、AD 資格情報を使用して、Azure AD DS に参加している任意の DSVM (SSH、Jupyterhub の両方) にログインできます。 ユーザー ワークスペースが共有の Azure ファイル上にあるため、ユーザーは、Jupyterhub の使用時に、任意の DSVM から自分のノートブックやその他の作業にアクセスできます。 

自動スケールの場合、仮想マシン スケール セットを使用して、この様式で、共有ディスクがマウントされているドメインにすべて参加している VM のプールを作成できます。 ユーザーは、仮想マシン スケール セット内の任意の使用可能なマシンにログインし、ノートブックが保存されている共有ディスクにアクセスできます。 

## <a name="next-steps"></a>次の手順

* [クラウド リソースにアクセスするための資格情報を安全に保存する](dsvm-secure-access-keys.md)



