# Oasis-Infobyte-Task-04
Oasis Infobyte Internship Task- 04 -> ONLINE EXAMINATION



#Main.java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        UserManager userManager = new UserManager();
        SessionManager session = new SessionManager();

        userManager.addUser(new User("john", "1234", "John Doe", "john@email.com"));

        System.out.println("Online Exam Portal");

        while (true) {
            System.out.println("\n1. Login\n2. Exit");
            System.out.print("Enter choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); 

            if (choice == 1) {
                System.out.print("Username: ");
                String username = scanner.nextLine();
                System.out.print("Password: ");
                String password = scanner.nextLine();

                User user = userManager.login(username, password);

                if (user != null) {
                    session.login(user);
                    showUserMenu(scanner, userManager, session);
                } else {
                    System.out.println("Login failed.");
                }
            } else if (choice == 2) {
                System.out.println("Exiting system.");
                break;
            } else {
                System.out.println("Invalid choice.");
            }
        }

        scanner.close();
    }

    public static void showUserMenu(Scanner scanner, UserManager userManager, SessionManager session) {
        while (session.isLoggedIn()) {
            System.out.println("\nWelcome, " + session.getCurrentUser().getName());
            System.out.println("1. Update Profile");
            System.out.println("2. Change Password");
            System.out.println("3. Take Exam");
            System.out.println("4. Logout");
            System.out.print("Choice: ");

            int option = scanner.nextInt();
            scanner.nextLine();

            switch (option) {
                case 1:
                    userManager.updateProfile(scanner, session.getCurrentUser());
                    break;
                case 2:
                userManager.changePassword(scanner, session.getCurrentUser());
                    break;
                case 3:
                    Exam exam = new Exam();
                    exam.start(scanner, session.getCurrentUser());
                    break;
                case 4:
                    session.logout();
                    System.out.println("Logged out successfully.");
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        }
    }
}




#User.java

public class User {
    private String username;
    private String password;
    private String name;
    private String email;

    public User(String username, String password, String name, String email) {
        this.username = username;
        this.password = password;
        this.name = name;
        this.email = email;
    }

    public String getUsername() { return username; }
    public String getPassword() { return password; }
    public String getName() { return name; }
    public String getEmail() { return email; }

    public void setPassword(String password) { this.password = password; }
    public void setName(String name) { this.name = name; }
    public void setEmail(String email) { this.email = email; }
}



#UserManager.java

import java.util.HashMap;
import java.util.Scanner;

public class UserManager {
    private HashMap<String, User> users = new HashMap<>();

    public void addUser(User user) {
        users.put(user.getUsername(), user);
    }

    public User login(String username, String password) {
        User user = users.get(username);
        if (user != null && user.getPassword().equals(password)) {
            return user;
        }
        return null;
    }

    public void updateProfile(Scanner scanner, User user) {
        System.out.print("Enter new name: ");
        String name = scanner.nextLine();
        System.out.print("Enter new email: ");
        String email = scanner.nextLine();

        user.setName(name);
        user.setEmail(email);

        System.out.println("Profile updated.");
    }

    public void changePassword(Scanner scanner, User user) {
        System.out.print("Enter current password: ");
        String current = scanner.nextLine();

        if (!user.getPassword().equals(current)) {
            System.out.println("Incorrect password.");
            return;
        }

        System.out.print("Enter new password: ");
        String newPass = scanner.nextLine();
        user.setPassword(newPass);
        System.out.println("Password changed.");
    }
}



#Question.java

public class Question {
    private String questionText;
    private String[] options;
    private int correctIndex;

    public Question(String questionText, String[] options, int correctIndex) {
        this.questionText = questionText;
        this.options = options;
        this.correctIndex = correctIndex;
    }

    public boolean ask(Scanner scanner) {
        System.out.println("\n" + questionText);
        for (int i = 0; i < options.length; i++) {
            System.out.println((i + 1) + ". " + options[i]);
        }

        System.out.print("Your answer: ");
        int answer = scanner.nextInt();

        return (answer - 1) == correctIndex;
    }
}



#Exam.java

import java.util.Scanner;
import java.util.Timer;
import java.util.TimerTask;

public class Exam {
    private Question[] questions;
    private int score = 0;
    private boolean timeUp = false;

    public Exam() {
        questions = new Question[] {
            new Question("What is the capital of France?", new String[] {"London", "Paris", "Berlin", "Rome"}, 1),
            new Question("Which language is used for Android Development?", new String[] {"Swift", "Kotlin", "Python", "Ruby"}, 1),
            new Question("2 + 2 equals?", new String[] {"3", "4", "5", "2"}, 1)
        };
    }

    public void start(Scanner scanner, User user) {
        System.out.println("\nExam Started! You have 30 seconds.");
        Timer timer = new Timer();

        timer.schedule(new TimerTask() {
            public void run() {
                timeUp = true;
                System.out.println("\nTime is up! Auto-submitting...");
            }
        }, 30000); 

        for (int i = 0; i < questions.length && !timeUp; i++) {
            boolean correct = questions[i].ask(scanner);
            if (correct) score++;
        }

        timer.cancel();
        System.out.println("Exam submitted. Your score: " + score + "/" + questions.length);
    }
}



#SessionManager.java

public class SessionManager {
    private User currentUser;

    public void login(User user) {
        this.currentUser = user;
    }

    public void logout() {
        this.currentUser = null;
    }

    public boolean isLoggedIn() {
        return currentUser != null;
    }

    public User getCurrentUser() {
        return currentUser;
    }
}



