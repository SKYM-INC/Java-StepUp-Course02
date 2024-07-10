### 練習問題: 優先度に基づいてタスクをソートする機能を実装する

**目標**: タスク管理アプリケーションにタスクを優先度順にソートする機能を追加する。

**手順**:

**ステップ1**: 基本的なソートの理解
   - **ヒント**: ソートはデータを特定の順序に並べ替えるプロセスです。Javaでは、`Collections.sort`メソッドを使用してリストをソートできます。

**ステップ2**: タスクの優先度に基づいてソートするためのメソッドを追加
   - **ヒント**: `Comparator`を使用して、タスクの優先度に基づいてリストをソートします。

**コード例**:
```java
import java.util.Collections;
import java.util.Comparator;

private void sortTasksByPriority() {
   tasks.sort(Comparator.comparingInt(Task::priority));
}
```

**ステップ3**: 新しいメソッドをタスク表示機能に統合
   - **ヒント**: `displayTasks`メソッドでタスクを表示する前に、`sortTasksByPriority`メソッドを呼び出します。

```java
public void displayTasks() {
    sortTasksByPriority();
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

###最終コード

**Main.java**
```java
package com.skym_inc;

import java.io.*;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
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
        int priority = getValidPriority(scanner);
        addTask(description, priority);
        System.out.println("タスクが追加されました。");
    }

    /**
     * 入力された優先度が1から5の範囲内であることを確認する
     * @param scanner スキャナーオブジェクト
     */
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
     * 新しいタスクを追加する
     * @param description タスクの説明
     * @param priority タスクの優先度
     */
    public void addTask(String description, int priority) {
        tasks.add(new Task(description, priority));
    }

    /**
     * 現在のタスクを表示する
     */
    public void displayTasks() {
        sortTasksByPriority();
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

    private void sortTasksByPriority() {
        Collections.sort(tasks, Comparator.comparingInt(Task::priority));
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
