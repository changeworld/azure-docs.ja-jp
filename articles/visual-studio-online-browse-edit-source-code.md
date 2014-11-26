<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Browse and Edit Your Source Code | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, edit, code, commit" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# ソース コードの参照と編集

Microsoft Azure ポータル プレビューでは、Visual Studio Online のチーム プロジェクトのソース コードを手軽に参照し、編集できます。

ポータルでチーム プロジェクトを作成し、コードにチェックイン (Team Foundation バージョン管理) またはコミット (Git) したら、以降はポータルからコードに容易にアクセスして参照できます。

1.  最初にチーム プロジェクト ブレードを開き、コード レンズを見つけます。次に、ソース パーツで、参照するリポジトリの名前 (この例では "BrowseCode") をクリックします。注: リポジトリが 3 つ以上ある場合は、リポジトリ番号 (この例では 1) をクリックすると、一覧が表示されます。
    ![Code Lens][Code Lens]
2.  リポジトリ ブレードが開いたら、[コード] パーツをクリックして、ソース コードの参照を開始します。
    ![Repository Blade][Repository Blade]
3.  標準のツリー ビュー ナビゲーション ブレードには現在のプロジェクト全体が表示され、ここから個々の要素にアクセスできます。ノードをクリックしてフォルダーを開くか、個別のファイル (この例では Index.cshtml) をクリックして、ファイルの表示や編集を行います。
    ![Tree Navigation][Tree Navigation]
4.  ファイルを編集するには、コマンド バーの [編集] をクリックします。すると、ファイルの内容が編集モードに変わり、変更できるようになります。ヒント: 全画面モードで編集するには、右上の最大化ボタンをクリックしてください。
    ![Edit Mode][Edit Mode]
5.  ファイルを編集し終わったら、コマンド バーの \[コミット] (または [チェックイン]) ボタンをクリックします。これにより、コミット メッセージを入力できる新しいブレードが起動します。メッセージを入力し、[OK] をクリックすると、ファイルがリポジトリにコミットされます。
    ![Commit Code][Commit Code]
6.  ブレードの最上部に、リポジトリへのコミットが成功したことを示すフラッシュ メッセージが表示されます。
    ![Commit Success][Commit Success]

  [Code Lens]: ./media/visual-studio-online-browse-edit-source-code/Code-Lens.png
  [Repository Blade]: ./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png
  [Tree Navigation]: ./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png
  [Edit Mode]: ./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png
  [Commit Code]: ./media/visual-studio-online-browse-edit-source-code/Commit-Code.png
  [Commit Success]: ./media/visual-studio-online-browse-edit-source-code/Commit-Success.png
