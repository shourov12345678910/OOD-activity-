 import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

class Customer {
    private String id;
    private String name;
    private String password;
    private List<Account> accounts;

    public Customer(String id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
        this.accounts = new ArrayList<>();
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public boolean authenticate(String password) {
        return this.password.equals(password);
    }

    public void addAccount(Account account) {
        accounts.add(account);
    }

    public List<Account> getAccounts() {
        return accounts;
    }
}

class Account {
    private String accountNumber;
    private double balance;
    private List<Transaction> transactions;

    public Account(String accountNumber) {
        this.accountNumber = accountNumber;
        this.balance = 0.0;
        this.transactions = new ArrayList<>();
    }

    public String getAccountNumber() {
        return accountNumber;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
        transactions.add(new Transaction("Deposit", amount));
    }

    public boolean withdraw(double amount) {
        if (balance >= amount) {
            balance -= amount;
            transactions.add(new Transaction("Withdrawal", amount));
            return true;
        }
        return false;
    }

    public void addBalance(double amount) {
        balance += amount;
        transactions.add(new Transaction("Add Balance", amount));
    }

    public List<Transaction> getTransactions() {
        return transactions;
    }
}

class Transaction {
    private String type;
    private double amount;
    private LocalDateTime dateTime;

    public Transaction(String type, double amount) {
        this.type = type;
        this.amount = amount;
        this.dateTime = LocalDateTime.now();
    }

    public String toString() {
        return "Transaction{" +
                "type='" + type + '\'' +
                ", amount=" + amount +
                ", dateTime=" + dateTime +
                '}';
    }
}

class Bank {
    private Map<String, Customer> customers;

    public Bank() {
        customers = new HashMap<>();
    }

    public boolean registerCustomer(String id, String name, String password) {
        if (customers.containsKey(id)) {
            return false;
        }
        customers.put(id, new Customer(id, name, password));
        return true;
    }

    public Customer authenticateCustomer(String id, String password) {
        Customer customer = customers.get(id);
        if (customer != null && customer.authenticate(password)) {
            return customer;
        }
        return null;
    }

    public void addAccountToCustomer(String customerId, String accountNumber) {
        Customer customer = customers.get(customerId);
        if (customer != null) {
            customer.addAccount(new Account(accountNumber));
        }
    }
}

public class Main {
    private static Bank bank = new Bank();
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        while (true) {
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine();
            switch (choice) {
                case 1:
                    register();
                    break;
                case 2:
                    login();
                    break;
                case 3:
                    System.exit(0);
            }
        }
    }

    private static void register() {
        System.out.print("Enter ID: ");
        String id = scanner.nextLine();
        System.out.print("Enter name: ");
        String name = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();
        if (bank.registerCustomer(id, name, password)) {
            System.out.println("Registration successful.");
        } else {
            System.out.println("Customer ID already exists.");
        }
    }

    private static void login() {
        System.out.print("Enter ID: ");
        String id = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();
        Customer customer = bank.authenticateCustomer(id, password);
        if (customer != null) {
            System.out.println("Login successful.");
            customerMenu(customer);
        } else {
            System.out.println("Invalid ID or password.");
        }
    }

    private static void customerMenu(Customer customer) {
        while (true) {
            System.out.println("1. View accounts");
            System.out.println("2. Open account");
            System.out.println("3. Add balance");
            System.out.println("4. Withdraw money");
            System.out.println("5. Logout");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine();
            switch (choice) {
                case 1:
                    viewAccounts(customer);
                    break;
                case 2:
                    openAccount(customer);
                    break;
                case 3:
                    addBalance(customer);
                    break;
                case 4:
                    withdrawMoney(customer);
                    break;
                case 5:
                    return; // Logout
            }
        }
    }

    private static void viewAccounts(Customer customer) {
        System.out.println("Accounts:");
        for (Account account : customer.getAccounts()) {
            System.out.println("Account Number: " + account.getAccountNumber() + ", Balance: " + account.getBalance());
        }
    }

    private static void openAccount(Customer customer) {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();
        bank.addAccountToCustomer(customer.getId(), accountNumber);
        System.out.println("Account opened successfully.");
    }

    private static void addBalance(Customer customer) {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();
        System.out.print("Enter amount to add: ");
        double amount = scanner.nextDouble();
        scanner.nextLine();
        for (Account account : customer.getAccounts()) {
            if (account.getAccountNumber().equals(accountNumber)) {
                account.addBalance(amount);
                System.out.println("Balance added successfully. New balance: " + account.getBalance());
                return;
            }
        }
        System.out.println("Account not found.");
    }

    private static void withdrawMoney(Customer customer) {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();
        System.out.print("Enter amount to withdraw: ");
        double amount = scanner.nextDouble();
        scanner.nextLine();
        for (Account account : customer.getAccounts()) {
            if (account.getAccountNumber().equals(accountNumber)) {
                if (account.withdraw(amount)) {
                    System.out.println("Withdrawal successful. Remaining balance: " + account.getBalance());
                } else {
                    System.out.println("Insufficient balance.");
                }
                return;
            }
        }
        System.out.println("Account not found.");
    }
}

