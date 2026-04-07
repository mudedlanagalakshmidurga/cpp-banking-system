# cpp-banking-system
 A console-based banking system built in C++ that allows users to create accounts, deposit and withdraw money, and view account details. It uses file handling for persistent storage and demonstrates object-oriented programming concepts.
#include <iostream>
#include <fstream>
using namespace std;

class Account {
private:
    int accNo;
    char name[50];
    float balance;

public:
    void createAccount();
    void showAccount() const;
    void modifyAccount();
    void deposit(float);
    void withdraw(float);
    int getAccNo() const;
    float getBalance() const;
};

// Create new account
void Account::createAccount() {
    cout << "\nEnter Account Number: ";
    cin >> accNo;
    cout << "Enter Account Holder Name: ";
    cin.ignore();
    cin.getline(name, 50);
    cout << "Enter Initial Balance: ";
    cin >> balance;
    cout << "\nAccount Created Successfully!\n";
}

// Display account details
void Account::showAccount() const {
    cout << "\nAccount No: " << accNo;
    cout << "\nName: " << name;
    cout << "\nBalance: " << balance << endl;
}

// Modify account
void Account::modifyAccount() {
    cout << "\nModify Account Holder Name: ";
    cin.ignore();
    cin.getline(name, 50);
    cout << "Modify Balance: ";
    cin >> balance;
}

// Deposit money
void Account::deposit(float amount) {
    balance += amount;
}

// Withdraw money
void Account::withdraw(float amount) {
    if (amount <= balance)
        balance -= amount;
    else
        cout << "\nInsufficient Balance!\n";
}

int Account::getAccNo() const {
    return accNo;
}

float Account::getBalance() const {
    return balance;
}

// Function declarations
void writeAccount();
void displayAccount(int);
void depositWithdraw(int, int);
void displayAll();

// Write account to file
void writeAccount() {
    Account acc;
    ofstream outFile("accounts.dat", ios::binary | ios::app);
    acc.createAccount();
    outFile.write(reinterpret_cast<char*>(&acc), sizeof(Account));
    outFile.close();
}

// Display single account
void displayAccount(int n) {
    Account acc;
    ifstream inFile("accounts.dat", ios::binary);
    bool found = false;

    while (inFile.read(reinterpret_cast<char*>(&acc), sizeof(Account))) {
        if (acc.getAccNo() == n) {
            acc.showAccount();
            found = true;
        }
    }

    if (!found)
        cout << "\nAccount not found!\n";

    inFile.close();
}

// Deposit or Withdraw
void depositWithdraw(int n, int option) {
    Account acc;
    fstream file("accounts.dat", ios::binary | ios::in | ios::out);
    bool found = false;

    while (!file.eof() && !found) {
        streampos pos = file.tellg();
        file.read(reinterpret_cast<char*>(&acc), sizeof(Account));

        if (acc.getAccNo() == n) {
            acc.showAccount();
            float amt;
            cout << "\nEnter amount: ";
            cin >> amt;

            if (option == 1)
                acc.deposit(amt);
            else
                acc.withdraw(amt);

            file.seekp(pos);
            file.write(reinterpret_cast<char*>(&acc), sizeof(Account));
            cout << "\nTransaction Successful!\n";
            found = true;
        }
    }

    if (!found)
        cout << "\nAccount not found!\n";

    file.close();
}

// Display all accounts
void displayAll() {
    Account acc;
    ifstream inFile("accounts.dat", ios::binary);

    cout << "\n\n=== ALL ACCOUNTS ===\n";

    while (inFile.read(reinterpret_cast<char*>(&acc), sizeof(Account))) {
        acc.showAccount();
        cout << "---------------------\n";
    }

    inFile.close();
}

// Main menu
int main() {
    int choice, accNo;

    do {
        cout << "\n\n=== BANK MANAGEMENT SYSTEM ===";
        cout << "\n1. Create Account";
        cout << "\n2. Display Account";
        cout << "\n3. Deposit Money";
        cout << "\n4. Withdraw Money";
        cout << "\n5. Display All Accounts";
        cout << "\n6. Exit";
        cout << "\nEnter your choice: ";
        cin >> choice;

        switch (choice) {
        case 1:
            writeAccount();
            break;

        case 2:
            cout << "\nEnter Account Number: ";
            cin >> accNo;
            displayAccount(accNo);
            break;

        case 3:
            cout << "\nEnter Account Number: ";
            cin >> accNo;
            depositWithdraw(accNo, 1);
            break;

        case 4:
            cout << "\nEnter Account Number: ";
            cin >> accNo;
            depositWithdraw(accNo, 2);
            break;

        case 5:
            displayAll();
            break;

        case 6:
            cout << "\nThank you!\n";
            break;

        default:
            cout << "\nInvalid Choice!\n";
        }

    } while (choice != 6);

    return 0;
}
