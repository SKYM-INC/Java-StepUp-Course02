### 新機能の追加：タスクの優先度設定

**目標**: タスク管理アプリケーションにタスクの優先度を設定する機能を追加し、基本的な新機能の実装方法を学ぶ。

**内容**:

1. **基本概念の説明**
   - **タスクの優先度**: タスクの重要度や緊急度を示す属性。優先度の数値が小さいほど重要なタスクであることを示します（1が最も高い優先度、5が最も低い優先度）。
   - **新しい属性の追加**: `Task`クラスに`priority`属性を追加し、タスクの優先度を管理する。

2. **ステップバイステップの実装**

**ステップ1**: `Task`クラスに`priority`属性を追加
   - 新しい属性を定義し、コンストラクタと`toString`メソッドを更新します。

```java
record Task(String description, int priority) {
    @Override
    public String toString() {
        return description + " (優先度: " + priority + ")";
    }
}
```

**ステップ2**: タスク追加機能の更新
   - `promptForNewTask`メソッドを更新し、優先度の入力を求めるようにします。

```java
private void promptForNewTask(Scanner scanner) {
    System.out.print("タスクを入力: ");
    String description = scanner.nextLine();
    System.out.print("優先度を入力 (1-5, 1が最も高い): ");
    int priority = Integer.parseInt(scanner.nextLine());
    addTask(description, priority);
    System.out.println("タスクが追加されました。");
}

public void addTask(String description, int priority) {
    tasks.add(new Task(description, priority));
}
```

**ステップ3**: タスク表示機能の更新
   - `displayTasks`メソッドを更新し、タスクの優先度を表示するようにします。

```java
public void displayTasks() {
    if (tasks.isEmpty()) {
        System.out.println("タスクがありません。");
    } else {
        for (int i = 0; i < tasks.size(); i++) {
            printTask(i, tasks.get(i));
        }
    }
}

private void printTask(int index, Task task) {
    System.out.println((index + 1) + ": " + task);
}
```

**まとめ**:
- 新しい機能の実装により、タスク管理アプリケーションはより柔軟で使いやすくなります。
- この過程で、新しい属性の追加、既存機能の更新、およびエラーハンドリングの強化を学ぶことができます。

**練習問題**:
1. `priority`の入力時に無効な値が入力された場合のエラーハンドリングを追加してください。
2. 優先度に基づいてタスクをソートする機能を実装してください。
