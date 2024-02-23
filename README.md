import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // Getter methods for username and password

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }
}

class Task {
    private String title;
    private String description;
    private boolean completed;

    public Task(String title, String description) {
        this.title = title;
        this.description = description;
        this.completed = false;
    }

    // Getter and Setter methods for title, description, and completion status

    public String getTitle() {
        return title;
    }

    public String getDescription() {
        return description;
    }

    public boolean isCompleted() {
        return completed;
    }

    public void setCompleted(boolean completed) {
        this.completed = completed;
    }
}

public class TaskManagementSystem {
    private static Map<String, User> users = new HashMap<>();
    private static Map<String, List<Task>> tasksByUser = new HashMap<>();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        // For simplicity, let's create a default user
        users.put("admin", new User("admin", "admin"));

        // Login
        User currentUser = login();

        // Display menu and perform actions based on user input
        int choice;
        do {
            displayMenu();
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    createTask(currentUser);
                    break;
                case 2:
                    viewTasks(currentUser);
                    break;
                case 3:
                    markTaskAsCompleted(currentUser);
                    break;
                case 4:
                    changePassword(currentUser);
                    break;
                case 5:
                    System.out.println("Exiting the Task Management System. Goodbye!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 5);
    }

    private static void displayMenu() {
        System.out.println("\n===== Task Management System =====");
        System.out.println("1. Create a new task");
        System.out.println("2. View your tasks");
        System.out.println("3. Mark a task as completed");
        System.out.println("4. Change password");
        System.out.println("5. Exit");
    }

    private static User login() {
        System.out.println("===== Login =====");
        System.out.print("Enter your username: ");
        String username = scanner.nextLine();
        System.out.print("Enter your password: ");
        String password = scanner.nextLine();

        User user = users.get(username);

        if (user != null && user.getPassword().equals(password)) {
            System.out.println("Login successful. Welcome, " + user.getUsername() + "!");
            return user;
        } else {
            System.out.println("Invalid credentials. Exiting the program.");
            System.exit(0);
            return null; // This line will never be reached, but needed for compilation
        }
    }

    private static void createTask(User user) {
        System.out.println("===== Create a New Task =====");
        System.out.print("Enter the title of the task: ");
        String title = scanner.nextLine();
        System.out.print("Enter the description of the task: ");
        String description = scanner.nextLine();

        Task newTask = new Task(title, description);

        tasksByUser.computeIfAbsent(user.getUsername(), k -> new ArrayList<>()).add(newTask);

        System.out.println("Task created successfully.");
    }

    private static void viewTasks(User user) {
        System.out.println("===== Your Tasks =====");

        List<Task> tasks = tasksByUser.get(user.getUsername());

        if (tasks != null && !tasks.isEmpty()) {
            for (int i = 0; i < tasks.size(); i++) {
                Task task = tasks.get(i);
                System.out.println((i + 1) + ". Title: " + task.getTitle() +
                        ", Description: " + task.getDescription() +
                        ", Completed: " + (task.isCompleted() ? "Yes" : "No"));
            }
        } else {
            System.out.println("You have no tasks at the moment.");
        }
    }

    private static void markTaskAsCompleted(User user) {
        System.out.println("===== Mark Task as Completed =====");

        List<Task> tasks = tasksByUser.get(user.getUsername());

        if (tasks != null && !tasks.isEmpty()) {
            System.out.print("Enter the number of the task you want to mark as completed: ");
            int taskNumber = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            if (taskNumber > 0 && taskNumber <= tasks.size()) {
                Task selectedTask = tasks.get(taskNumber - 1);
                selectedTask.setCompleted(true);
                System.out.println("Task marked as completed.");
            } else {
                System.out.println("Invalid task number. Please try again.");
            }
        } else {
            System.out.println("You have no tasks at the moment.");
        }
    }

    private static void changePassword(User user) {
        System.out.println("===== Change Password =====");
        System.out.print("Enter your current password: ");
        String currentPassword = scanner.nextLine();

        if (currentPassword.equals(user.getPassword())) {
            System.out.print("Enter your new password: ");
            String newPassword = scanner.nextLine();
            user = new User(user.getUsername(), newPassword);
            users.put(user.getUsername(), user);
            System.out.println("Password changed successfully.");
        } else {
            System.out.println("Incorrect current password. Password change failed.");
        }
    }
}
