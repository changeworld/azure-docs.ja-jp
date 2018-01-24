
特定の関数アプリ内のすべての関数のコードは、ホスト構成ファイルと 1 つ以上のサブフォルダーを含むルート フォルダーにあります。 各サブフォルダーには、次の例のように、個別の関数のコードが含まれています。

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

host.json ファイルにはランタイム固有の構成が含まれています。このファイルは関数アプリのルート フォルダーにあります。 使用可能な設定の詳細については、[host.json のリファレンス](../articles/azure-functions/functions-host-json.md)に関するページを参照してください。

関数にはそれぞれ、1 つ以上のコード ファイル、function.json 構成、およびその他の依存関係を含むフォルダーがあります。

