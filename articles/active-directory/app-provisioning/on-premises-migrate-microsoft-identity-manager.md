---
title: Azure AD ECMA コネクタ ホストで使用するために Microsoft Identity Manager コネクタをエクスポートする
description: Azure AD ECMA コネクタ ホストで使用するために MIM Sync からコネクタを作成およびエクスポートする方法を説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 229da477e358a0efd85ea555762443de9859b253
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570002"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-azure-ad-ecma-connector-host"></a>Azure AD ECMA コネクタ ホストで使用するために Microsoft Identity Manager コネクタをエクスポートする

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

FIM Sync または MIM Sync から、Azure AD ECMA コネクタ ホストに特定のコネクタの構成をインポートできます。  MIM Sync のインストールは、MIM Sync からの継続的な同期ではなく、構成にのみ使用されます。

>[!IMPORTANT]
>現時点では、汎用 SQL (GSQL) コネクタだけが、Azure AD ECMA コネクタ ホストでの使用がサポートされています。


## <a name="creating-and-exporting-a-connector-configuration-in-mim-sync"></a>MIM Sync でのコネクタ構成の作成とエクスポート
ECMA コネクタと MIM Sync が既に構成されている場合は、手順 10 に進みます。

 1. Windows Server 2016 サーバーを準備します。これは、Azure AD ECMA コネクタ ホストで実行するために使用されるサーバーとは異なります。  このホスト サーバーでは、SQL Server 2016 データベースが同じ場所にあるか、SQL Server 2016 データベースにネットワーク接続されている必要があります。  このサーバーを設定する 1 つの方法は、イメージ **Windows Server 2016 の SQL Server 2016 SP1 Standard** を使用して Azure 仮想マシンをデプロイすることです。  このサーバーは、セットアップ目的でのリモート デスクトップ アクセス以外ではインターネット接続を必要としないことに注意してください。
 2. MIM Sync のインストール中に使用するアカウントを作成します。  これを、その Windows Server 上のローカル アカウントにすることが可能です。  ローカル アカウントを作成するには、コントロール パネルを起動し、ユーザー アカウントを開き、ユーザー アカウント **mimsync** を追加します。
 3. 前の手順で作成したアカウントをローカルの Administrators グループに追加します。
 4. 以前に作成したアカウントに、サービスを実行する機能を与えます。  ローカル セキュリティ ポリシーを起動し、[ローカル ポリシー]、 [ユーザー権利の割り当て]、 **[サービスとしてログオン]** をクリックします。  前述のアカウントを追加します。
 5. このホストに MIM Sync をインストールします。 MIM Sync バイナリをお持ちではない場合は、 [https://www.microsoft.com/en-us/download/details.aspx?id=48244](https://www.microsoft.com/en-us/download/details.aspx?id=48244) から ZIP ファイルをダウンロードし、ISO イメージをマウントし、フォルダー **Synchronization Service** を Windows Server ホストにコピーすることで、評価版をインストールできます。  次に、そのフォルダーに含まれているセットアップ プログラムを実行します。   評価ソフトウェアは期限付きであり、有効期限が切れます。そして実稼働環境での使用は意図されていない点に注意してください。
 6. MIM Sync のインストールが完了したら、ログアウトしてログインして戻ります。
 7. MIM Sync と同じサーバーにコネクタをインストールします。 (説明のため、このテスト ラボ ガイドでは、[https://www.microsoft.com/en-us/download/details.aspx?id=51495](https://www.microsoft.com/en-us/download/details.aspx?id=51495) からダウンロードできる Microsoft 提供のコネクタの 1 つを使用します)。
 8. Synchronization Service UI を起動します。  **[管理エージェント]** をクリックします。  **[作成 ]** をクリックし、コネクタ管理エージェントを指定します。  ECMA ベースのコネクタ管理エージェントを選択してください。
 9. コネクタに名前を付け、コネクタにデータをインポートおよびエクスポートするために必要なパラメーターを構成します。  コネクタが、ユーザーまたは人物オブジェクト型の単一値文字列属性をインポートおよびエクスポートできるよう構成してください。
 10. MIM Sync サーバー コンピューターで、まだ実行されていない場合は、同期サービス UI を起動します。  **[管理エージェント]** をクリックします。
 11. コネクタを選択し、 **[管理エージェントのエクスポート]** をクリックします。  XML ファイルと、コネクタの DLL および関連ソフトウェアを、ECMA コネクタ ホストを保持する Windows Server に保存します。

この時点で、MIM Sync サーバーは不要になります。

 1. Azure AD ECMA コネクタ ホストにより実行されるアカウントで Windows Server にサインインします。
 2. ディレクトリ c:\program files\Microsoft ECMA2host\Service\ECMA に移動し、そのディレクトリに既に 1 つ以上の DLL が存在していることを確認します。  (これらの DLL は、Microsoft が提供するコネクタに対応しています)。
 3. コネクタの MA DLL とその前提条件 DLL を、Service ディレクトリの同じ ECMA サブディレクトリにコピーします。
 4. C:\program files\Microsoft ECMA2Host\Wizard ディレクトリに移動し、プログラム Microsoft.ECMA2Host.ConfigWizard.exe を実行して ECMA コネクタ ホストの構成を設定します。
 5. コネクタの一覧が表示された新しいウィンドウが表示されます。 既定では、コネクタは存在しません。  **[;新しいコネクタ]** をクリックします。
 6. 前に MIM からエクスポートした管理エージェント xml ファイルを指定します。  上記のコネクタの構成セクションの構成とスキーマ マッピングの手順を進めます。



## <a name="next-steps"></a>次の手順


- [アプリ プロビジョニング](user-provisioning.md)
- [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)