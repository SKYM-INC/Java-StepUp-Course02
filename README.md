### 新機能の追加：タスクの優先度設定

**目標**: タスク管理アプリケーションにタスクの優先度を設定する機能を追加し、基本的な新機能の実装方法を学ぶ。

**内容**:

1. **基本概念の説明**
   - **タスクの優先度**: タスクの重要度や緊急度を示す属性。優先度の数値が小さいほど重要なタスクであることを示します（1が最も高い優先度、5が最も低い優先度）。
   - **新しい属性の追加**: `Task`クラスに`priority`属性を追加し、タスクの優先度を管理する。

2. **実装**

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

**ステップ4**: タスク読み込み機能の更新
   - `loadTasks`メソッドを更新し、ファイルからタスクの優先度を読み込むようにします。

```java
private void loadTasks() {
    File file = new File(FILE_NAME);
    if (!file.exists()) {
        // ファイルが存在しない場合は読み込みをスキップ
        return;
    }

    try (BufferedReader reader = new BufferedReader(new FileReader(FILE_NAME))) {
        String line;
        while ((line = reader.readLine()) != null) {
            String[] parts = line.split(",");
            String description = parts[0];
            int priority = Integer.parseInt(parts[1]);
            tasks.add(new Task(description, priority));
        }
    } catch (IOException e) {
        System.out.println("タスクの読み込み中にエラーが発生しました: " + e.getMessage());
    }
}
```

**ステップ5**: タスク保存機能の更新
   - `saveTasks`メソッドを更新し、ファイルにタスクの優先度を保存するようにします。

```java
private void saveTasks() {
    try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_NAME))) {
        for (Task task : tasks) {
            writer.write(task.description() + "," + task.priority());
            writer.newLine();
        }
    } catch (IOException e) {
        System.out.println("タスクの保存中にエラーが発生しました: " + e.getMessage());
    }
}
```


**まとめ**:
- 新しい機能の実装により、タスク管理アプリケーションはより柔軟で使いやすくなります。
- この過程で、新しい属性の追加、既存機能の更新、およびエラーハンドリングの強化を学ぶことができます。

**練習問題**:
1. `priority`の入力時に無効な値が入力された場合のエラーハンドリングを追加してください。
2. 優先度に基づいてタスクをソートする機能を実装してください。

### 最終コード

**Main.java**
```java
package com.skym_inc;

import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

/**
 * タスク管理アプリケーション
 */
public class Main {
    private final List<Task> tasks;
    private static final String FILE_NAME = "tasks.txt";

    /**
     * コンストラクタ
     * タスクリストを初期化し、ファイルからタスクを読み込む
     */
    public Main() {
        tasks = new ArrayList<>();
        loadTasks();
    }

    /**
     * メインメソッド
     * @param args コマンドライン引数
     */
    public static void main(String[] args) {
        // アプリケーションのエントリーポイント
        ToDoApp app = new ToDoApp();
        app.run();
    }

    /**
     * アプリケーションを実行する
     * ユーザーからのコマンド入力を処理する
     */
    public void run() {
        Scanner scanner = new Scanner(System.in);
        String command;
        label:
        while (true) {
            System.out.print("コマンドを入力 (add, show, delete, exit): ");
            command = scanner.nextLine();
            switch (command) {
                case "exit":
                    saveTasks();
                    break label;
                case "add":
                    promptForNewTask(scanner);
                    break;
                case "show":
                    displayTasks();
                    break;
                case "delete":
                    promptForTaskDeletion(scanner);
                    break;
                default:
                    System.out.println("無効なコマンドです。");
                    break;
            }
        }
        scanner.close();
    }

    /**
     * 新しいタスクの入力を促す
     * @param scanner スキャナーオブジェクト
     */
    private void promptForNewTask(Scanner scanner) {
        System.out.print("タスクを入力: ");
        String description = scanner.nextLine();
        System.out.print("優先度を入力 (1-5, 1が最も高い): ");
        int priority = Integer.parseInt(scanner.nextLine());
        addTask(description, priority);
        System.out.println("タスクが追加されました。");
    }

    /**
     * 削除するタスクの番号の入力を促す
     * @param scanner スキャナーオブジェクト
     */
    private void promptForTaskDeletion(Scanner scanner) {
        System.out.print("削除するタスク番号を入力: ");
        try {
            int index = Integer.parseInt(scanner.nextLine()) - 1;
            if (index >= 0 && index < tasks.size()) {
                deleteTask(index);
                System.out.println("タスクが削除されました。");
            } else {
                System.out.println("無効なタスク番号です。");
            }
        } catch (NumberFormatException e) {
            System.out.println("無効な入力です。数値を入力してください。");
        }
    }

    /**
     * 現在のタスクを表示する
     */
    public void displayTasks() {
        if (tasks.isEmpty()) {
            System.out.println("タスクがありません。");
        } else {
            for (int i = 0; i < tasks.size(); i++) {
                printTask(i, tasks.get(i));
            }
        }
    }

    /**
      * 指定されたタスクを表示する
      * @param index タスクのインデックス
      * @param task タスクオブジェクト
      */
    private void printTask(int index, Task task) {
        System.out.println((index + 1) + ": " + task);
    }

    /**
     * 新しいタスクを追加する
     * @param description タスクの説明
     * @param priority タスクの優先度
     */
    public void addTask(String description, int priority) {
        tasks.add(new Task(description, priority));
    }

    /**
     * タスクを削除する
     * @param index 削除するタスクのインデックス
     */
    public void deleteTask(int index) {
        if (index >= 0 && index < tasks.size()) {
            tasks.remove(index);
        } else {
            System.out.println("無効なタスク番号です。タスク番号は1から " + tasks.size() + " の範囲で指定してください。");
        }
    }

    /**
     * タスクをファイルに保存する
     */
    private void saveTasks() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_NAME))) {
            for (Task task : tasks) {
                writer.write(task.description() + "," + task.priority());
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("タスクの保存中にエラーが発生しました: " + e.getMessage());
        }
    }

    /**
     * ファイルからタスクを読み込む
     */
    private void loadTasks() {
        File file = new File(FILE_NAME);
        if (!file.exists()) {
            // ファイルが存在しない場合は読み込みをスキップ
            return;
        }

        try (BufferedReader reader = new BufferedReader(new FileReader(FILE_NAME))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(",");
                String description = parts[0];
                int priority = Integer.parseInt(parts[1]);
                tasks.add(new Task(description, priority));
            }
        } catch (IOException e) {
            System.out.println("タスクの読み込み中にエラーが発生しました: " + e.getMessage());
        }
    }
}
```

**Task.java**
```java
package com.skym_inc;

/**
 * タスクを表すクラス
 * @param description タスクの説明
 * @param priority タスクの優先度
 */
record Task(String description, int priority) {
    @Override
    public String toString() {
        return description + " (優先度: " + priority + ")";
    }
}
```
