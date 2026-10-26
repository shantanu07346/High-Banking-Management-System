# High-Banking-Management-System
The Basic Calculator Program is designed to perform four fundamental arithmetic operations: Addition, Subtraction, Multiplication, and Division. It allows users to enter two numbers and select the desired operation to obtain the result.

#include <stdio.h>
#include <string.h>
#define MAX 100
#define HISTORY 100

struct Account {
    int accountNo;
    char name[50];
    char pin[10];
    double balance;
    char transactions[HISTORY][100];
    int transactionCount;
};

struct Account accounts[MAX];
int totalAccounts = 0;
int nextAccountNo = 1001;

int findAccount(int accountNo) {
    for (int i = 0; i < totalAccounts; i++) {
        if (accounts[i].accountNo == accountNo)
            return i;
    }
    return -1;
}

int login(int index) {
    char pin[10];

    printf("Enter your PIN: ");
    scanf("%9s", pin);

    if (strcmp(accounts[index].pin, pin) == 0)
        return 1;

    printf("Incorrect PIN!\n");
    return 0;
}

void addTransaction(int index, const char *message) {
    if (accounts[index].transactionCount < HISTORY) {
        strcpy(accounts[index].transactions[
            accounts[index].transactionCount++], message);
    }
}

void createAccount() {
    if (totalAccounts >= MAX) {
        printf("Bank account limit reached!\n");
        return;
    }

    struct Account *a = &accounts[totalAccounts];

    a->accountNo = nextAccountNo++;

    printf("Enter account holder name: ");
    scanf(" %49[^\n]", a->name);

    printf("Set a 4-digit PIN: ");
    scanf("%9s", a->pin);

    if (strlen(a->pin) != 4 ||
        a->pin[0] < '0' || a->pin[0] > '9' ||
        a->pin[1] < '0' || a->pin[1] > '9' ||
        a->pin[2] < '0' || a->pin[2] > '9' ||
        a->pin[3] < '0' || a->pin[3] > '9') {
        printf("Invalid PIN! Use exactly 4 digits.\n");
        return;
    }

    a->balance = 0;
    a->transactionCount = 0;

    printf("Initial deposit amount: Rs. ");
    if (scanf("%lf", &a->balance) != 1 || a->balance < 0) {
        printf("Invalid amount!\n");
        a->balance = 0;
        return;
    }

    char msg[100];
    snprintf(msg, sizeof(msg),
             "Account opened with Rs. %.2f", a->balance);
    addTransaction(totalAccounts, msg);

    printf("\nAccount created successfully!\n");
    printf("Account Number: %d\n", a->accountNo);

    totalAccounts++;
}

void deposit() {
    int number;
    double amount;

    printf("Enter account number: ");
    scanf("%d", &number);

    int i = findAccount(number);
    if (i == -1 || !login(i)) {
        printf("Account not found or login failed.\n");
        return;
    }

    printf("Enter deposit amount: Rs. ");
    if (scanf("%lf", &amount) != 1 ||
        amount <= 0 || amount > 1000000000.0) {
        printf("Invalid amount!\n");
        return;
    }

    accounts[i].balance += amount;

    char msg[100];
    snprintf(msg, sizeof(msg), "Deposited Rs. %.2f", amount);
    addTransaction(i, msg);

    printf("Deposit successful!\n");
    printf("Balance: Rs. %.2f\n", accounts[i].balance);
}

void withdraw() {
    int number;
    double amount;

    printf("Enter account number: ");
    scanf("%d", &number);

    int i = findAccount(number);
    if (i == -1 || !login(i)) {
        printf("Account not found or login failed.\n");
        return;
    }

    printf("Enter withdrawal amount: Rs. ");
    if (scanf("%lf", &amount) != 1 ||
        amount <= 0 || amount > 1000000000.0) {
        printf("Invalid amount!\n");
        return;
    }

    if (amount > accounts[i].balance) {
        printf("Insufficient balance!\n");
        return;
    }

    accounts[i].balance -= amount;

    char msg[100];
    snprintf(msg, sizeof(msg), "Withdrawn Rs. %.2f", amount);
    addTransaction(i, msg);

    printf("Withdrawal successful!\n");
    printf("Balance: Rs. %.2f\n", accounts[i].balance);
}

void checkBalance() {
    int number;

    printf("Enter account number: ");
    scanf("%d", &number);

    int i = findAccount(number);
    if (i == -1 || !login(i)) {
        printf("Account not found or login failed.\n");
        return;
    }

    printf("\nAccount Holder: %s\n", accounts[i].name);
    printf("Account Number: %d\n", accounts[i].accountNo);
    printf("Available Balance: Rs. %.2f\n",
           accounts[i].balance);
}

void transferMoney() {
    int from, to;
    double amount;

    printf("Your account number: ");
    scanf("%d", &from);

    int i = findAccount(from);
    if (i == -1 || !login(i)) {
        printf("Account not found or login failed.\n");
        return;
    }

    printf("Receiver account number: ");
    scanf("%d", &to);

    int j = findAccount(to);

    if (j == -1 || i == j) {
        printf("Invalid receiver account!\n");
        return;
    }

    printf("Transfer amount: Rs. ");
    if (scanf("%lf", &amount) != 1 ||
        amount <= 0 || amount > 1000000000.0) {
        printf("Invalid amount!\n");
        return;
    }

    if (amount > accounts[i].balance) {
        printf("Insufficient balance!\n");
        return;
    }

    accounts[i].balance -= amount;
    accounts[j].balance += amount;

    char msg[100];

    snprintf(msg, sizeof(msg),
             "Transferred Rs. %.2f to account %d",
             amount, to);
    addTransaction(i, msg);

    snprintf(msg, sizeof(msg),
             "Received Rs. %.2f from account %d",
             amount, from);
    addTransaction(j, msg);

    printf("Money transferred successfully!\n");
    printf("Remaining balance: Rs. %.2f\n",
           accounts[i].balance);
}

void transactionHistory() {
    int number;

    printf("Enter account number: ");
    scanf("%d", &number);

    int i = findAccount(number);
    if (i == -1 || !login(i)) {
        printf("Account not found or login failed.\n");
        return;
    }

    printf("\n--- Transaction History ---\n");

    if (accounts[i].transactionCount == 0) {
        printf("No transactions found.\n");
        return;
    }

    for (int j = 0; j < accounts[i].transactionCount; j++) {
        printf("%d. %s\n", j + 1,
               accounts[i].transactions[j]);
    }
}

int main() {
    int choice;

    do {
        printf("\n================================\n");
        printf("   ADVANCED BANKING SYSTEM\n");
        printf("================================\n");
        printf("1. Create Account\n");
        printf("2. Deposit Money\n");
        printf("3. Withdraw Money\n");
        printf("4. Check Balance\n");
        printf("5. Transfer Money\n");
        printf("6. Transaction History\n");
        printf("7. Exit\n");
        printf("--------------------------------\n");
        printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1) {
            printf("Invalid input!\n");
            return 1;
        }

        switch (choice) {
            case 1: createAccount(); break;
            case 2: deposit(); break;
            case 3: withdraw(); break;
            case 4: checkBalance(); break;
            case 5: transferMoney(); break;
            case 6: transactionHistory(); break;
            case 7: printf("Thank you for using our bank!\n"); break;
            default: printf("Invalid choice! Try again.\n");
        }

    } while (choice != 7);

    return 0;
}
