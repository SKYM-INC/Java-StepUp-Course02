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

**最終コード**

Main.java
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
