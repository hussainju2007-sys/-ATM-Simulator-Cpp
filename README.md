# -ATM-Simulator-Cpp
This is a basic ATM Simulator project developed in C++. It simulates real-world ATM functions such as balance inquiry, cash deposit, cash withdrawal, and PIN authentication through a console-based user interface. The project is designed using simple logic and menu-driven programming, ideal for beginners learning object-oriented programming in C++.
#include <iostream>
#include <vector>
#include <string>

class Account {
    int accountNumber;
    double balance;
public:
    Account(int accNum, double bal) : accountNumber(accNum), balance(bal) {}
    int getAccountNumber() { return accountNumber; }
    double getBalance() { return balance; }
    void deposit(double amount) { balance += amount; }
    bool withdraw(double amount) {
        if (amount > balance) return false;
        balance -= amount;
        return true;
    }
};

class User {
    std::string name;
    int pin;
    Account account;
public:
    User(std::string n, int p, int accNum, double bal) 
        : name(n), pin(p), account(accNum, bal) {}
    bool checkPIN(int enteredPIN) { return enteredPIN == pin; }
    Account& getAccount() { return account; }
    std::string getName() { return name; }
};

class ATM {
    std::vector<User> users;
    User* currentUser = nullptr;
public:
    ATM(std::vector<User> us) : users(us) {}
    bool login(int accNum, int pin) {
        for (auto& user : users) {
            if (user.getAccount().getAccountNumber() == accNum && user.checkPIN(pin)) {
                currentUser = &user;
                return true;
            }
        }
        return false;
    }
    void logout() { currentUser = nullptr; }
    void showMenu() {
        int choice;
        do {
            std::cout << "\n1. View Balance\n2. Deposit\n3. Withdraw\n4. Exit\nChoose: ";
            std::cin >> choice;
            switch (choice) {
                case 1:
                    std::cout << "Balance: " << currentUser->getAccount().getBalance() << "\n";
                    break;
                case 2: {
                    double amount;
                    std::cout << "Enter deposit amount: ";
                    std::cin >> amount;
                    currentUser->getAccount().deposit(amount);
                    std::cout << "Deposited!\n";
                    break;
                }
                case 3: {
                    double amount;
                    std::cout << "Enter withdrawal amount: ";
                    std::cin >> amount;
                    if (currentUser->getAccount().withdraw(amount))
                        std::cout << "Withdrawn!\n";
                    else
                        std::cout << "Insufficient funds!\n";
                    break;
                }
                case 4:
                    std::cout << "Goodbye!\n";
                    break;
                default:
                    std::cout << "Invalid choice.\n";
            }
        } while (choice != 4);
    }
    void run() {
        int accNum, pin;
        std::cout << "=== ATM Login ===\n";
        std::cout << "Account Number: "; std::cin >> accNum;
        std::cout << "PIN: "; std::cin >> pin;
        if (login(accNum, pin)) {
            std::cout << "Welcome, " << currentUser->getName() << "!\n";
            showMenu();
        } else {
            std::cout << "Login failed.\n";
        }
        logout();
    }
};

int main() {
    std::vector<User> users = {
        User("Alice", 1234, 1001, 5000.0),
        User("Bob", 4321, 1002, 3000.0)
    };
    ATM atm(users);
    atm.run();
    return 0;
}
