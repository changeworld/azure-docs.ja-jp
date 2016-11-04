
次に、登録を試みる前にユーザーが認証されるように、通知を登録するタイミングを変更する必要があります。

1. Eclipse の Package Explorer で ToDoActivity.java ファイルを開き、`onCreate` メソッドを見つけます。`onCreate` メソッドの次のコードを `createTable` メソッドの開始部分に移動します。
   
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
   
     `authenticate` メソッドの完了時に、`createTable` メソッドが呼び出されます。`createTable` 全体は、次のようになります。
   
        private void createTable() {
   
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
   
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
   
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
   
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
   
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }    

<!---HONumber=August15_HO6-->