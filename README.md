# ATM-Interface

import java.util.*;

class Transaction {
    String type;
    double amount;
    Date date;

    Transaction(String type, double amount) {
        this.type = type;
        this.amount = amount;
        this.date = new Date();
    }

    public String toString() {
        return date + " - " + type + ": " + amount;
    }
}

class Account {
    String userId;
    String pin;
    double balance;
    List<Transaction> history = new ArrayList<>();

    Account(String userId, String pin, double balance) {
        this.userId = userId;
        this.pin = pin;
        this.balance = balance;
    }

    boolean checkPin(String inputPin) {
        return pin.equals(inputPin);
    }

    void deposit(double amt) {
        balance += amt;
        history.add(new Transaction("Deposit", amt));
    }

    boolean withdraw(double amt) {
        if (amt <= balance) {
            balance -= amt;
            history.add(new Transaction("Withdraw", amt));
            return true;
        }
        return false;
    }

    boolean transfer(Account toAcc, double amt) {
        if (amt <= balance) {
            balance -= amt;
            toAcc.deposit(amt);
            history.add(new Transaction("Transfer to " + toAcc.userId, amt));
            return true;
        }
        return false;
    }

    void printHistory() {
        if (history.isEmpty()) {
            System.out.println("No transactions yet.");
            return;
        }
        for (Transaction t : history) {
            System.out.println(t);
        }
    }
}

public class Main {
    static Scanner sc = new Scanner(System.in);
    static Map<String, Account> accounts = new HashMap<>();

    public static void main(String[] args) {
        // Pre-create some accounts
        accounts.put("user1", new Account("user1", "1234", 1000));
        accounts.put("user2", new Account("user2", "4321", 500));

        System.out.println("=== Welcome to ATM Interface ===");

        Account current = login();
        if (current == null) {
            System.out.println("Login failed. Exiting.");
            return;
        }

        while (true) {
            System.out.println("\nChoose option:");
            System.out.println("1. Transaction History");
            System.out.println("2. Withdraw");
            System.out.println("3. Deposit");
            System.out.println("4. Transfer");
            System.out.println("5. Quit");

            int choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {
                case 1:
                    System.out.println("Transaction History:");
                    current.printHistory();
                    break;
                case 2:
                    System.out.print("Enter amount to withdraw: ");
                    double wAmt = sc.nextDouble();
                    sc.nextLine();
                    if (current.withdraw(wAmt)) {
                        System.out.println("Withdrawal successful. New balance: " + current.balance);
                    } else {
                        System.out.println("Insufficient balance.");
                    }
                    break;
                case 3:
                    System.out.print("Enter amount to deposit: ");
                    double dAmt = sc.nextDouble();
                    sc.nextLine();
                    current.deposit(dAmt);
                    System.out.println("Deposit successful. New balance: " + current.balance);
                    break;
                case 4:
                    System.out.print("Enter user ID to transfer to: ");
                    String toUser = sc.nextLine();
                    Account toAcc = accounts.get(toUser);
                    if (toAcc == null) {
                        System.out.println("User not found.");
                        break;
                    }
                    System.out.print("Enter amount to transfer: ");
                    double tAmt = sc.nextDouble();
                    sc.nextLine();
                    if (current.transfer(toAcc, tAmt)) {
                        System.out.println("Transfer successful. New balance: " + current.balance);
                    } else {
                        System.out.println("Insufficient balance.");
                    }
                    break;
                case 5:
                    System.out.println("Thank you for using ATM. Goodbye!");
                    return;
                default:
                    System.out.println("Invalid option.");
            }
        }
    }

    static Account login() {
        System.out.print("Enter user ID: ");
        String userId = sc.nextLine();
        System.out.print("Enter PIN: ");
        String pin = sc.nextLine();

        Account acc = accounts.get(userId);
        if (acc != null && acc.checkPin(pin)) {
            System.out.println("Login successful!");
            return acc;
        }
        System.out.println("Invalid user ID or PIN.");
        return null;
    }
}
