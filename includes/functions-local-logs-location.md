ローカルに実行している Functions ホストは、次のパスにログを書き込みます。

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Windows では、`<DefaultTempDirectory>` は TMP、TEMP、USERPROFILE 環境変数、または Windows ディレクトリで最初に見つかった値です。
MacOS または Linux では、`<DefaultTempDirectory>` は TMPDIR 環境変数です。

[!Note]
Functions ホストは、開始するときに、ディレクトリ内の既存のファイル構造を上書きします。