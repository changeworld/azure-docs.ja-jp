## <a name="prepare-your-intel-nuc"></a>Intel NUC を準備する

ハードウェアの設定を完了するには、以下のことを行う必要があります。

- Intel NUC を、キットに含まれている電源装置に接続します。
- イーサネット ケーブルを使用して Intel NUC をネットワークに接続します。

これで、Intel NUC ゲートウェイ デバイスのハードウェア設定が完了しました。

### <a name="sign-in-and-access-the-terminal"></a>ターミナルにサインインまたはアクセスする

Intel NUC でターミナル環境にアクセスする方法は 2 とおりあります。

- Intel NUC に接続されているキーボードとモニターがある場合は、シェルに直接アクセスできます。 既定の資格情報は、**root** というユーザー名と **root** というパスワードです。

- SSH を使用して、デスクトップ コンピューターから Intel NUC のシェルにアクセスします。

#### <a name="sign-in-with-ssh"></a>SSH を使用してサインインする

SSH でサインインするためには、Intel NUC の IP アドレスが必要です。 Intel NUC に接続されているキーボードとモニターがある場合は、`ifconfig` コマンドで IP アドレスを確認します。 別の方法として、ルーターに接続してネットワーク上のデバイスのアドレスを一覧表示することもできます。

**root** というユーザー名と **root** というパスワードでサインインします。

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>省略可能: Intel NUC でフォルダーを共有する

必要に応じて、デスクトップ環境を使用して Intel NUC でフォルダーを共有することができます。 フォルダーを共有すると、`nano` や `vi` を使用しなくても、任意のデスクトップ テキスト エディター ([Visual Studio Code](https://code.visualstudio.com/) や [Sublime Text](http://www.sublimetext.com/) など) を使用して、Intel NUC でファイルを編集できるようになります。

Windows でフォルダーを共有するためには、Intel NUC に Samba サーバーを構成します。 別の方法として、デスクトップ コンピューター上の SFTP クライアントで、Intel NUC 上の SFTP サーバーを使用することもできます。
