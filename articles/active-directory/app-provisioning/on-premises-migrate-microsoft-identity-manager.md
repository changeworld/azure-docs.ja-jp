---
title: Azure AD ECMA コネクタ ホストで使用するために Microsoft Identity Manager コネクタをエクスポートする
description: Azure AD ECMA コネクタ ホストで使用するために MIM Sync からコネクタを作成およびエクスポートする方法を説明します。
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/11/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6659e28e1f2814f8bf2b0f08e21db25edae3dd66
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370690"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA コネクタ ホストで使用するために Microsoft Identity Manager コネクタをエクスポートする

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューです。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

Forefront Identity Manager 同期サービスまたは Microsoft Identity Manager 同期サービス (MIM Sync) のインストールから特定のコネクタの構成を Azure Active Directory (Azure AD) ECMA Connector Host にインポートすることができます。 MIM Sync のインストールは、Azure AD からの継続的な同期ではなく、構成にのみ使用されます。

>[!IMPORTANT]
>現時点では、汎用の SQL コネクタと LDAP コネクタだけが、Azure AD ECMA コネクタ ホストで使用できます。

## <a name="create-and-export-a-connector-configuration-in-mim-sync"></a>MIM Sync のコネクタ構成を作成してエクスポートする
ECMA コネクタと MIM Sync が既に構成されている場合は、手順 10 に進みます。

 1. Windows Server 2016 サーバーを準備します。これは、Azure AD ECMA コネクタ ホストで実行するために使用されるサーバーとは異なります。 このホスト サーバーでは、SQL Server 2016 データベースが同じ場所にあるか、SQL Server 2016 データベースにネットワーク接続されている必要があります。 このサーバーを設定する 1 つの方法は、イメージ **Windows Server 2016 の SQL Server 2016 SP1 Standard** を使用して Azure 仮想マシンをデプロイすることです。 このサーバーは、セットアップ目的でのリモート デスクトップ アクセス以外ではインターネット接続を必要としません。
 1. MIM Sync のインストール中に使用するアカウントを作成します。 これを、その Windows Server インスタンス上のローカル アカウントにすることが可能です。 ローカル アカウントを作成するために、 **[コントロール パネル]**  >  **[ユーザー アカウント]** を開いて、**mimsync** というユーザー アカウントを追加します。
 1. 前の手順で作成したアカウントをローカルの Administrators グループに追加します。
 1. 以前に作成したアカウントに、サービスを実行する機能を与えます。 **[ローカル セキュリティ ポリシー]** を起動し、 **[ローカル ポリシー]**  >  **[ユーザー権利の割り当て]**  >  **[サービスとしてログオン]** を選択します。 前述のアカウントを追加します。
 1. このホストに MIM Sync をインストールします。 MIM Sync バイナリをお持ちではない場合は、[Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=48244)から ZIP ファイルをダウンロードし、ISO イメージをマウントして、フォルダー **Synchronization Service** を Windows Server ホストにコピーすることで、評価版をインストールできます。 次に、そのフォルダーに含まれているセットアップ プログラムを実行します。 評価ソフトウェアは期限付きであり、有効期限が切れます。 実稼働環境での使用は意図されていません。
 1. MIM Sync のインストールが完了したら、一度サインアウトしてから再度サインインします。
 1. MIM Sync と同じサーバーにコネクタをインストールします。説明のため、このテスト ラボ ガイドでは、[Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=51495)からダウンロードできる Microsoft 提供のコネクタの 1 つを使用します。
 1. 同期サービスの UI を起動します。 **[管理エージェント]** を選択します。 **[作成]** を選択し、コネクタ管理エージェントを指定します。 ECMA ベースのコネクタ管理エージェントを選択してください。
 1. コネクタに名前を付け、コネクタにデータをインポートおよびエクスポートするために必要なパラメーターを構成します。 コネクタが、ユーザーまたは人物オブジェクト型の単一値文字列属性をインポートおよびエクスポートできるよう構成してください。
 1. MIM Sync サーバー コンピューターで、まだ実行されていない場合は、同期サービスの UI を起動します。 **[管理エージェント]** を選択します。
 1. コネクタを選択し、 **[管理エージェントのエクスポート]** を選択します。 XML ファイルと、コネクタの DLL および関連ソフトウェアを、ECMA コネクタ ホストを保持する Windows Server に保存します。

この時点で、MIM Sync サーバーは不要になります。

 1. Azure AD ECMA コネクタ ホストにより実行されるアカウントで Windows Server にサインインします。
 1. C:\Program Files\Microsoft ECMA2host\Service\ECMA ディレクトリに移動します。 そのディレクトリに既に 1 つ以上の DLL が存在していることを確認します。 これらの DLL は、Microsoft が提供するコネクタに対応しています。
 1. コネクタの MA DLL とその前提条件 DLL を、Service ディレクトリの同じ ECMA サブディレクトリにコピーします。
 1. C:\Program Files\Microsoft ECMA2Host\Wizard ディレクトリに移動します。 プログラム Microsoft.ECMA2Host.ConfigWizard.exe を実行して ECMA コネクタ ホストの構成を設定します。
 1. コネクタの一覧が表示された新しいウィンドウが表示されます。 既定では、コネクタは存在しません。 **[新しいコネクタ]** を選択します。
 1. 前に MIM Sync からエクスポートした管理エージェント XML ファイルを指定します。 コネクタの構成に関するセクションの構成とスキーマ マッピングの手順を進めます。

## <a name="next-steps"></a>次のステップ

- [アプリ プロビジョニング](user-provisioning.md)
- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md)
