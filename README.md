import java.io.*;
import java.util.*;

class Employee implements Serializable {
    private int id;
    private String name;
    private String gender;
    private String location;
    private double salary;

    public Employee(int id, String name, String gender, String location, double salary) {
        this.id = id;
        this.name = name;
        this.gender = gender;
        this.location = location;
        this.salary = salary;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public String getGender() { return gender; }
    public String getLocation() { return location; }
    public double getSalary() { return salary; }

    public void setName(String name) { this.name = name; }
    public void setGender(String gender) { this.gender = gender; }
    public void setLocation(String location) { this.location = location; }
    public void setSalary(double salary) { this.salary = salary; }

    public String toString() {
        return "Employee ID: " + id + ", Name: " + name + ", Gender: " + gender +
                ", Location: " + location + ", Salary: " + salary;
    }
}

class EmployeeManager {
    private List<Employee> employees = new ArrayList<>();
    private final String fileName = "employees.dat";

    public EmployeeManager() {
        loadEmployees();
    }

    @SuppressWarnings("unchecked")
    private void loadEmployees() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(fileName))) {
            employees = (List<Employee>) ois.readObject();
        } catch (FileNotFoundException e) {
            System.out.println("No existing employee records found.");
        } catch (IOException | ClassNotFoundException e) {
            System.out.println("Error loading employee records: " + e.getMessage());
        }
    }

    private void saveEmployees() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(fileName))) {
            oos.writeObject(employees);
        } catch (IOException e) {
            System.out.println("Error saving employee records: " + e.getMessage());
        }
    }

    public void addEmployee(Employee employee) {
        employees.add(employee);
        saveEmployees();
    }

    public void modifyEmployee(int id, String name, String gender, String location, double salary) {
        for (Employee emp : employees) {
            if (emp.getId() == id) {
                emp.setName(name);
                emp.setGender(gender);
                emp.setLocation(location);
                emp.setSalary(salary);
                saveEmployees();
                return;
            }
        }
        System.out.println("Employee with ID " + id + " not found.");
    }

    public void deleteEmployee(int id) {
        employees.removeIf(emp -> emp.getId() == id);
        saveEmployees();
    }

    public void displayAllEmployees() {
        if (employees.isEmpty()) {
            System.out.println("No employee records available.");
        } else {
            employees.forEach(System.out::println);
        }
    }

    public void filterByGender(String gender) {
        employees.stream()
                 .filter(emp -> emp.getGender().equalsIgnoreCase(gender))
                 .forEach(System.out::println);
    }

    public void filterByLocation(String location) {
        employees.stream()
                 .filter(emp -> emp.getLocation().equalsIgnoreCase(location))
                 .forEach(System.out::println);
    }
}

class User implements Serializable {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() { return username; }
    public String getPassword() { return password; }
}

class UserManager {
    private final String userFile = "users.dat";
    private List<User> users = new ArrayList<>();

    public UserManager() {
        loadUsers();
    }

    @SuppressWarnings("unchecked")
    private void loadUsers() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(userFile))) {
            users = (List<User>) ois.readObject();
        } catch (FileNotFoundException e) {
            System.out.println("No users registered yet.");
        } catch (IOException | ClassNotFoundException e) {
            System.out.println("Error loading users: " + e.getMessage());
        }
    }

    private void saveUsers() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(userFile))) {
            oos.writeObject(users);
        } catch (IOException e) {
            System.out.println("Error saving users: " + e.getMessage());
        }
    }

    public boolean register(String username, String password) {
        for (User u : users) {
            if (u.getUsername().equals(username)) {
                return false; // Username already exists
            }
        }
        users.add(new User(username, password));
        saveUsers();
        return true;
    }

    public boolean login(String username, String password) {
        for (User u : users) {
            if (u.getUsername().equals(username) && u.getPassword().equals(password)) {
                return true;
            }
        }
        return false;
    }
}

public class EmployeeManagementSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        UserManager userManager = new UserManager();

        while (true) {
            System.out.println("\n=== Welcome to Employee Management System ===");
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Select an option: ");
            String option = scanner.nextLine();

            switch (option) {
                case "1":
                    System.out.print("Enter username: ");
                    String newUser = scanner.nextLine();
                    System.out.print("Enter password: ");
                    String newPass = scanner.nextLine();
                    if (userManager.register(newUser, newPass)) {
                        System.out.println("Registration successful!");
                    } else {
                        System.out.println("Username already exists.");
                    }
                    break;

                case "2":
                    System.out.print("Enter username: ");
                    String user = scanner.nextLine();
                    System.out.print("Enter password: ");
                    String pass = scanner.nextLine();
                    if (userManager.login(user, pass)) {
                        System.out.println("Login successful. Welcome, " + user + "!");
                        runSystem();
                    } else {
                        System.out.println("Invalid credentials. Try again.");
                    }
                    break;

                case "3":
                    System.out.println("Exiting system...");
                    return;

                default:
                    System.out.println("Invalid choice.");
            }
        }
    }

    private static void runSystem() {
        EmployeeManager manager = new EmployeeManager();
        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("\n--- Employee Management Menu ---");
            System.out.println("1. Add Employee");
            System.out.println("2. Modify Employee");
            System.out.println("3. Delete Employee");
            System.out.println("4. Display All Employees");
            System.out.println("5. Filter Employees by Gender");
            System.out.println("6. Filter Employees by Location");
            System.out.println("7. Logout");
            System.out.print("Enter your choice: ");

            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter ID: ");
                    int id = scanner.nextInt();
                    scanner.nextLine();
                    System.out.print("Enter Name: ");
                    String name = scanner.nextLine();
                    System.out.print("Enter Gender: ");
                    String gender = scanner.nextLine();
                    System.out.print("Enter Location: ");
                    String location = scanner.nextLine();
                    System.out.print("Enter Salary: ");
                    double salary = scanner.nextDouble();
                    manager.addEmployee(new Employee(id, name, gender, location, salary));
                    break;
                case 2:
                    System.out.print("Enter ID of Employee to Modify: ");
                    id = scanner.nextInt();
                    scanner.nextLine();
                    System.out.print("Enter New Name: ");
                    name = scanner.nextLine();
                    System.out.print("Enter New Gender: ");
                    gender = scanner.nextLine();
                    System.out.print("Enter New Location: ");
                    location = scanner.nextLine();
                    System.out.print("Enter New Salary: ");
                    salary = scanner.nextDouble();
                    manager.modifyEmployee(id, name, gender, location, salary);
                    break;
                case 3:
                    System.out.print("Enter ID of Employee to Delete: ");
                    id = scanner.nextInt();
                    manager.deleteEmployee(id);
                    break;
                case 4:
                    manager.displayAllEmployees();
                    break;
                case 5:
                    System.out.print("Enter Gender (Male/Female): ");
                    gender = scanner.nextLine();
                    manager.filterByGender(gender);
                    break;
                case 6:
                    System.out.print("Enter Location: ");
                    location = scanner.nextLine();
                    manager.filterByLocation(location);
                    break;
                case 7:
                    System.out.println("Logging out...");
                    return;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }
}
