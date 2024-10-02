
# Astronaut Daily Schedule Organizer - Code and Output

## Problem Statement

Design and implement a console-based application that helps astronauts organize their daily schedules. The application should allow users to add, remove, and view daily tasks. Each task will have a description, start time, end time, and priority level.

The solution applies key design patterns like Singleton, Factory, and Observer.

---

## Code Implementation

### 1. Singleton Pattern: `ScheduleManager` Class

```java
import java.util.*;

class ScheduleManager {
    private static ScheduleManager instance;
    private List<Task> tasks = new ArrayList<>();
    private List<ScheduleObserver> observers = new ArrayList<>();

    private ScheduleManager() {}

    public static ScheduleManager getInstance() {
        if (instance == null) {
            instance = new ScheduleManager();
        }
        return instance;
    }

    public void addTask(Task task) {
        if (isConflict(task)) {
            notifyObservers(task);
        } else {
            tasks.add(task);
            System.out.println("Task added successfully. No conflicts.");
        }
    }

    public void removeTask(String description) {
        Task taskToRemove = null;
        for (Task task : tasks) {
            if (task.getDescription().equals(description)) {
                taskToRemove = task;
                break;
            }
        }
        if (taskToRemove != null) {
            tasks.remove(taskToRemove);
            System.out.println("Task removed successfully.");
        } else {
            System.out.println("Error: Task not found.");
        }
    }

    public void viewTasks() {
        if (tasks.isEmpty()) {
            System.out.println("No tasks scheduled for the day.");
        } else {
            tasks.sort(Comparator.comparing(Task::getStartTime));
            for (Task task : tasks) {
                System.out.println(task);
            }
        }
    }

    private boolean isConflict(Task newTask) {
        for (Task task : tasks) {
            if (task.isConflict(newTask)) {
                return true;
            }
        }
        return false;
    }

    public void addObserver(ScheduleObserver observer) {
        observers.add(observer);
    }

    private void notifyObservers(Task task) {
        for (ScheduleObserver observer : observers) {
            observer.update(task);
        }
    }
}
```

---

### 2. Factory Pattern: `TaskFactory` Class

```java
import java.time.LocalTime;

class TaskFactory {
    public static Task createTask(String description, String startTime, String endTime, String priority) {
        try {
            LocalTime start = LocalTime.parse(startTime);
            LocalTime end = LocalTime.parse(endTime);
            return new Task(description, start, end, priority);
        } catch (Exception e) {
            throw new IllegalArgumentException("Error: Invalid time format.");
        }
    }
}
```

---

### 3. Observer Pattern: `ScheduleObserver` Interface and `ConflictObserver` Class

```java
interface ScheduleObserver {
    void update(Task task);
}

class ConflictObserver implements ScheduleObserver {
    public void update(Task task) {
        System.out.println("Error: Task conflicts with existing schedule - " + task.getDescription());
    }
}
```

---

### 4. Task Class for Handling Tasks

```java
import java.time.LocalTime;

class Task {
    private String description;
    private LocalTime startTime;
    private LocalTime endTime;
    private String priority;

    public Task(String description, LocalTime startTime, LocalTime endTime, String priority) {
        this.description = description;
        this.startTime = startTime;
        this.endTime = endTime;
        this.priority = priority;
    }

    public String getDescription() {
        return description;
    }

    public LocalTime getStartTime() {
        return startTime;
    }

    public LocalTime getEndTime() {
        return endTime;
    }

    public boolean isConflict(Task otherTask) {
        return !(this.endTime.isBefore(otherTask.startTime) || this.startTime.isAfter(otherTask.endTime));
    }

    @Override
    public String toString() {
        return startTime + " - " + endTime + ": " + description + " [" + priority + "]";
    }
}
```

---

### 5. Main Class to Run the Application

```java
import java.util.Scanner;

public class AstronautDailySchedule {
    public static void main(String[] args) {
        ScheduleManager manager = ScheduleManager.getInstance();
        manager.addObserver(new ConflictObserver());
        
        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("1. Add Task");
            System.out.println("2. Remove Task");
            System.out.println("3. View Tasks");
            System.out.println("4. Exit");
            int choice = scanner.nextInt();
            scanner.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter description: ");
                    String description = scanner.nextLine();
                    System.out.print("Enter start time (HH:MM): ");
                    String startTime = scanner.nextLine();
                    System.out.print("Enter end time (HH:MM): ");
                    String endTime = scanner.nextLine();
                    System.out.print("Enter priority (Low, Medium, High): ");
                    String priority = scanner.nextLine();

                    try {
                        Task task = TaskFactory.createTask(description, startTime, endTime, priority);
                        manager.addTask(task);
                    } catch (IllegalArgumentException e) {
                        System.out.println(e.getMessage());
                    }
                    break;

                case 2:
                    System.out.print("Enter task description to remove: ");
                    String removeDesc = scanner.nextLine();
                    manager.removeTask(removeDesc);
                    break;

                case 3:
                    manager.viewTasks();
                    break;

                case 4:
                    System.exit(0);

                default:
                    System.out.println("Invalid option. Try again.");
            }
        }
    }
}
```

---

## Example Output

### 1. Adding tasks without conflict

```
1. Add Task
Enter description: Morning Exercise
Enter start time (HH:MM): 07:00
Enter end time (HH:MM): 08:00
Enter priority (Low, Medium, High): High
Task added successfully. No conflicts.

1. Add Task
Enter description: Team Meeting
Enter start time (HH:MM): 09:00
Enter end time (HH:MM): 10:00
Enter priority (Low, Medium, High): Medium
Task added successfully. No conflicts.
```

### 2. Viewing tasks

```
3. View Tasks
07:00 - 08:00: Morning Exercise [High]
09:00 - 10:00: Team Meeting [Medium]
```

### 3. Adding a task with conflict

```
1. Add Task
Enter description: Training Session
Enter start time (HH:MM): 09:30
Enter end time (HH:MM): 10:30
Enter priority (Low, Medium, High): High
Error: Task conflicts with existing schedule - Training Session
```

### 4. Removing a task

```
2. Remove Task
Enter task description to remove: Morning Exercise
Task removed successfully.
```

---

### Negative Case Examples

1. Input: `Add Task("Training Session", "09:30", "10:30", "High")`
   Output: `Error: Task conflicts with existing schedule - Training Session`

2. Input: `Add Task("Invalid Task", "25:00", "26:00", "Low")`
   Output: `Error: Invalid time format.`
