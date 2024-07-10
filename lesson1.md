### 練習問題: タスクの優先度入力時のエラーハンドリング

**目標**: ユーザーが優先度を入力する際に無効な値が入力された場合、適切にエラーメッセージを表示し、再入力を促す機能を追加する。

**手順**:

**ステップ1**: 基本的なエラーハンドリングの理解
   - **ヒント**: エラーハンドリングとは、プログラムが異常な状態に遭遇した際に適切に対応するための方法です。

**ステップ2**: 優先度入力の検証
   - **ヒント**: 入力された優先度が1から5の範囲内であることを確認します。

```java
private int getValidPriority(Scanner scanner) {
    int priority;
    while (true) {
        try {
            System.out.print("優先度を入力 (1-5, 1が最も高い): ");
            priority = Integer.parseInt(scanner.nextLine());
            if (priority < 1 || priority > 5) {
                System.out.println("無効な優先度です。1から5の間で入力してください。");
            } else {
                break;
            }
        } catch (NumberFormatException e) {
            System.out.println("無効な入力です。数値を入力してください。");
        }
    }
    return priority;
}
```

**ステップ3**: 新しいメソッドをタスク追加機能に統合
   - **ヒント**: `promptForNewTask`メソッドで`getValidPriority`メソッドを使用します。

```java
private void promptForNewTask(Scanner scanner) {
    System.out.print("タスクを入力: ");
    String description = scanner.nextLine();
    int priority = getValidPriority(scanner);
    addTask(description, priority);
    System.out.println("タスクが追加されました。");
}
```

**まとめ**: この練習を通じて、無効な入力を適切に処理する方法を学び、ユーザーが正しいデータを入力することを保証する重要性を理解します。

