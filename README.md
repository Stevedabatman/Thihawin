#include <iostream>
#include <iomanip>
#include <string>
using namespace std;

const int MAX_EMPLOYEES = 100;

class Employee {
private:
    string id;
    string name;
    string type;

public:
    Employee() {}

    void inputDetails() {
        cout << "Enter Employee ID: ";
        cin >> id;
        cout << "Enter Employee Name: ";
        cin.ignore();
        getline(cin, name);
        cout << "Enter Employee Type (Full-time/Part-time): ";
        getline(cin, type);
    }

    string getId() {
        return id;
    }

    string getName() {
        return name;
    }

    string getType() {
        return type;
    }

    void displayDetails() {
        cout << left << setw(15) << id << setw(25) << name << setw(15) << type;
    }
};

class Salary : public Employee {
private:
    double baseSalary;
    double hourlyRate;
    int leaveDays;
    int overtimeHours;
    int workedHours;

public:
    Salary() : baseSalary(0), hourlyRate(0), leaveDays(0), overtimeHours(0), workedHours(0) {}

    void selectPosition() {
        int position;
        cout << "Select Position:\n";
        cout << "1. Barista ($1920)\n";
        cout << "2. Cashier ($1790)\n";
        cout << "3. Kitchen Staff (Part-time) ($7.7/hour)\n";
        cout << "4. Cleaner (Part-time) ($5.8/hour)\n";
        cout << "5. Waiter/Waitress (Part-time) ($7/hour)\n";
        cout << "6. Supervisor ($2400)\n";
        cout << "Enter the number corresponding to the position: ";
        cin >> position;

        switch (position) {
            case 1:
                baseSalary = 1920;
                hourlyRate = 8;
                cout << "Position: Barista\n";
                break;
            case 2:
                baseSalary = 1790;
                hourlyRate = 7.5;
                cout << "Position: Cashier\n";
                break;
            case 3:
                hourlyRate = 7.7;
                cout << "Position: Kitchen Staff (Part-time)\n";
                break;
            case 4:
                hourlyRate = 5.8;
                cout << "Position: Cleaner (Part-time)\n";
                break;
            case 5:
                hourlyRate = 7;
                cout << "Position: Waiter/Waitress (Part-time)\n";
                break;
            case 6:
                baseSalary = 2400;
                hourlyRate = 10;
                cout << "Position: Supervisor\n";
                break;
            default:
                cout << "Invalid choice, setting default as Barista.\n";
                baseSalary = 1920;
                hourlyRate = 8;
        }
    }

    void inputWorkDetails() {
        if (getType() == "Part-time") {
            cout << "Enter hours worked: ";
            cin >> workedHours;
        } else {
            cout << "Enter Overtime Hours: ";
            cin >> overtimeHours;
            cout << "Enter Leave Days: ";
            cin >> leaveDays;
        }
    }

    double calculatePartTimeSalary() {
        return workedHours * hourlyRate;
    }

    double calculateLeaveBonus() {
        if (leaveDays > 2) {
            return 0;
        }
        switch (leaveDays) {
            case 0:
                return 500;
            case 1:
                return 200;
            case 2:
                return 100;
            default:
                return 0;
        }
    }

    double calculateFullTimeNetSalary() {
        double dailyRate = baseSalary / 30;
        double overtimePay = overtimeHours * (hourlyRate * 2);
        double leaveDeduction = 0;

        if (leaveDays > 4) {
            leaveDeduction = (baseSalary * 0.01);
        }

        double grossSalary = baseSalary + overtimePay + calculateLeaveBonus() - leaveDeduction;
        double tax = grossSalary * 0.02;
        double totalDeductions = tax + 10;

        double netSalary = grossSalary - totalDeductions;
        return netSalary;
    }
void displaySalaryDetails() {
        if (getType() == "Part-time") {
            cout << setw(15) << hourlyRate << setw(15) << workedHours << setw(15) << calculatePartTimeSalary() << endl;
        } else {
            cout << setw(15) << baseSalary << setw(15) << overtimeHours << setw(15) << leaveDays << setw(15) << calculateLeaveBonus() << setw(15) << calculateFullTimeNetSalary() << endl;
        }
    }
};

Salary employees[MAX_EMPLOYEES];
int employeeCount = 0;

// Quick Sort Functions (Sort by Name)
int partition(Salary arr[], int low, int high) {
    Salary pivot = arr[high];
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (arr[j].getName() <= pivot.getName()) {
            i++;
            swap(arr[i], arr[j]);
        }
    }

    swap(arr[i + 1], arr[high]);
    return i + 1;
}

void quickSort(Salary arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

void showMenu() {
    cout << "----- Employee Management System -----" << endl;
    cout << "1. Insert Employee Data" << endl;
    cout << "2. Update Employee Data" << endl;
    cout << "3. View Employee Data" << endl;
    cout << "4. View Total Salary of All Employees" << endl;
    cout << "5. Exit" << endl;
    cout << "--------------------------------------" << endl;
    cout << "Choose an option: ";
}

void viewTotalSalary() {
    double totalFullTimeSalary = 0;
    double totalPartTimeSalary = 0;

    for (int i = 0; i < employeeCount; i++) {
        if (employees[i].getType() == "Part-time") {
            totalPartTimeSalary += employees[i].calculatePartTimeSalary();
        } else {
            totalFullTimeSalary += employees[i].calculateFullTimeNetSalary();
        }
    }

    cout << "\nTotal Salary for Full-Time Employees: $" << totalFullTimeSalary << endl;
    cout << "Total Salary for Part-Time Employees: $" << totalPartTimeSalary << endl;
}

int main() {
    int option;

    do {
        showMenu();
        cin >> option;

        switch (option) {
            case 1:
                if (employeeCount < MAX_EMPLOYEES) {
                    employees[employeeCount].inputDetails();
                    employees[employeeCount].selectPosition();
                    employees[employeeCount].inputWorkDetails();
                    employeeCount++;
                    cout << "Employee data inserted successfully!" << endl;
                } else {
                    cout << "Employee database is full!" << endl;
                }
                break;

            case 3:
                if (employeeCount == 0) {
                    cout << "No employee data available!" << endl;
                } else {
                    // Sort the employees by name before displaying
                    quickSort(employees, 0, employeeCount - 1);

                    // Display Full-time Employee Data
                    cout << "\n----- Full-Time Employee Data -----" << endl;
                    cout << left << setw(15) << "ID" << setw(25) << "Name" << setw(15) << "Type" << setw(15) << "Base Salary" << setw(15) << "Overtime Hrs" << setw(15) << "Leave Days" << setw(15) << "Leave Bonus" << setw(15) << "Net Salary" << endl;
                    cout << string(120, '-') << endl;

                    for (int i = 0; i < employeeCount; i++) {
                        if (employees[i].getType() == "Full-time") {
                            employees[i].displayDetails();
                            employees[i].displaySalaryDetails();
                        }
                    }

                    // Display Part-time Employee Data
                    cout << "\n----- Part-Time Employee Data -----" << endl;
                    cout << left << setw(15) << "ID" << setw(25) << "Name" << setw(15) << "Type" << setw(15) << "Hourly Rate" << setw(15) << "Worked Hours" << setw(15) << "Total Salary" << endl;
                    cout << string(120, '-') << endl;
                    for (int i = 0; i < employeeCount; i++) {
                        if (employees[i].getType() == "Part-time") {
                            employees[i].displayDetails();
                            employees[i].displaySalaryDetails();
                        }
                    }
                    cout << "--------------------------" << endl;
                }
                break;

            case 4:
                viewTotalSalary();
                break;

            case 5:
                cout << "Exiting program..." << endl;
                break;

            default:
                cout << "Invalid choice, please try again." << endl;
                break;
        }
    } while (option != 5);

    return 0;
};
