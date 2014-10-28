<properties title="How to create a hybrid deployment of RemoteApp" pageTitle="How to create a hybrid deployment of RemoteApp" description="Learn how to create a deployment of RemoteApp that connects to your internal network." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo"></tags>

# RemoteApp のクラウドのデプロイメントの作成方法

RemoteApp のデプロイメントには、次の 2 種類の方法があります。

-   クラウド: Azure に完全に常駐し、Azure の管理ポータルの **[簡易作成]** オプションを使用して作成されます。
-   ハイブリッド: 内部設置型アクセス用の仮想ネットワークを含み、管理ポータルの **[VPN で作成]** オプションを使用して作成されます。

このチュートリアルでは、クラウドのデプロイメントを作成する手順について説明します。次の 4 つのステップがあります。

1.  RemoteApp サービスを作成する。
2.  オプションでディレクトリの同期を構成する。RemoteApp では、内部設置型の Active Directory のユーザー、グループ、連絡先、パスワードを Azure Active Directory テナントと同期するために、この構成が必要となります。
3.  RemoteApp プログラムを発行する。
4.  ユーザー アクセスを構成する。

**開始する前に**

サービスを作成する前に、以下の操作が必要です。

-   RemoteApp のプレビューにサインアップします。これは、[][][http://azure.microsoft.com/ja-jp/services/remoteapp/][http://azure.microsoft.com/ja-jp/services/remoteapp/]</a> で行うことができます。
-   アクセス権を付与するユーザーとグループに関する情報を集めます。この情報とは、ユーザーまたはグループの Microsoft アカウントの情報または Active Directory の組織アカウントの情報です。
-   この手順は、サブスクリプションの一部として提供されたテンプレートのイメージを使用しようとしているか、使用するテンプレートのイメージが既にアップロード済みであることが前提となっています。別のテンプレート イメージをアップロードする必要がある場合は、[テンプレート イメージ] ページから実行できます。**[テンプレート イメージのアップロード]** をクリックして、ウィザードで表示される手順に従います。

## **手順 1.RemoteApp サービスの作成**

1.  [Windows Azure の管理ポータル][Windows Azure の管理ポータル]で、[RemoteApp] ページに移動します。
2.  **[新規] \> [簡易作成]** をクリックします。

3.  サービスの名前を入力し、リージョンを選択します。
4.  このサービスの作成に使用するサブスクリプションを選択します。
5.  このサービス用に使用するテンプレートを選択します。

    **ヒント:** RemoteApp のサブスクリプションに付属するテンプレート イメージには、Office 2013 プログラムが含まれており、プログラムの一部は発行済み (Word など)、それ以外は発行の準備が完了しています。

6.  **[RemoteApp サービスの作成]** をクリックします。

    **重要:** サービスがプロビジョニングされるまで最大 30 分かかる場合があります。

RemoteApp サービスが作成されたら、RemoteApp の **[クイック スタート]** ページに進んでセットアップの手順を続行します。

## **手順 2.Active Directory ディレクトリの同期の構成 (オプション)**

Active Directory を使用する場合、RemoteApp では、Azure Active Directory と内部設置型 Active Directory との間でディレクトリの同期が必要になります。これにより、ユーザー、グループ、連絡先、およびパスワードが Azure Active Directory テナントと同期します。計画に関する情報、および詳しい手順については、「[ディレクトリ同期のロードマップ][ディレクトリ同期のロードマップ]」を参照してください。

## **手順 3.RemoteApp プログラムの発行**

RemoteApp プログラムは、ユーザーに提供するアプリケーションまたはプログラムのことです。このプログラムは、サービス用にアップロードしたテンプレート イメージに置かれています。ユーザーが RemoteApp プログラムにアクセスすると、プログラムはユーザーのローカル環境で実行しているように見えますが、実際には Azure で実行しています。

ユーザーから RemoteApp プログラムへのアクセスを可能にするには、最初にこのプログラムをエンド ユーザー フィードに発行する必要があります。エンド ユーザー フィードとは、ユーザーが Azure のポータルを通じてアクセスできるプログラムの一覧です。

RemoteApp サービスには複数のプログラムを発行できます。[RemoteApp プログラム] ページから、**[発行]** をクリックしてプログラムを追加します。プログラムは、テンプレート イメージの [スタート] メニューから発行することも、テンプレート イメージのパスをプログラムに指定して発行することもできます。[スタート] メニューから追加する場合は、発行するプログラムを選択します。プログラムのパスを指定して発行する場合は、プログラムの名前と、プログラムがインストールされるテンプレート イメージ上の保存場所までのパスを指定します。

## **手順 4.ユーザー アクセスの構成**

これで RemoteApp サービスが作成できたので、リモート リソースを使用可能にするユーザーとグループを追加する必要があります。Active Directory を使用している場合は、この RemoteApp サービスを作成するために使用したサブスクリプションに関連付けられた Active Directory テナントに、アクセス権を付与するユーザーまたはグループが存在している必要があります。

1.  [クイック スタート] ページの **[ユーザー アクセスの構成]** をクリックします。
2.  アクセス権を付与する (Active Directory の) 組織アカウントかグループ名、または Microsoft アカウントを入力します。

    ユーザーに対しては、"<user@domain.com>" 形式を使用するようにしてください。グループに対しては、グループ名を入力します。

3.  ユーザーまたはグループが検証されたら、**[保存]** をクリックします。

## 次のステップ

これで、RemoteApp クラウドのデプロイメントの作成とデプロイが正常に完了しました。次のステップは、ユーザーによるリモート デスクトップ クライアントのダウンロードとインストールです。このクライアントの URL は、RemoteApp の [クイック スタート] ページにあります。次に、ユーザーが Azure にログインし、発行した RemoteApp プログラムにアクセスします。

  []: http://azure.microsoft.com/ja-jp/services/remoteapp/
  [Windows Azure の管理ポータル]: http://manage.windowsazure.com
  [ディレクトリ同期のロードマップ]: http://msdn.microsoft.com/ja-jp/library/azure/hh967642.aspx
