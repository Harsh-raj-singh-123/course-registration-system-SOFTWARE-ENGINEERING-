#include <iostream>
#include <vector>
#include <string>
using namespace std;

// -----------------------------------------------
// CLASS DEFINITIONS
// -----------------------------------------------

class Course {
public:
    int id;
    string code, name;
    int seats;

    Course(int i, string c, string n, int s) {
        id = i;
        code = c;
        name = n;
        seats = s;
    }
};

class Student {
public:
    int id;
    string name;
    vector<int> registered;

    Student(int i, string n) {
        id = i;
        name = n;
    }

    void viewRegistered(const vector<Course>& courses) {
    cout << "\n--- Registered Courses ---\n";
    if (registered.empty()) {
        cout << "No courses registered.\n";
        return;
    }

    // old-style loop for registered[]
    for (int i = 0; i < registered.size(); i++) {
        int cid = registered[i];

        // old-style loop for courses vector
        for (int j = 0; j < courses.size(); j++) {
            if (courses[j].id == cid) {
                cout << courses[j].code << " - " << courses[j].name << endl;
            }
        }
    }
}

};

// -----------------------------------------------
// DATABASE-LIKE STORAGE
// -----------------------------------------------

vector<Course> courses;
vector<Student> students;

// -----------------------------------------------
// FUNCTIONS
// -----------------------------------------------

void adminMenu();
void studentMenu(Student &s);

void addCourse() {
    int id, seats;
    string code, name;

    cout << "Enter Course ID: ";
    cin >> id;
    cout << "Enter Course Code: ";
    cin >> code;
    cout << "Enter Course Name: ";
    cin.ignore();
    getline(cin, name);
    cout << "Enter Seats: ";
    cin >> seats;

    courses.push_back(Course(id, code, name, seats));
    cout << "Course added successfully!\n";
}

void deleteCourse() {
    int cid;
    cout << "Enter Course ID to delete: ";
    cin >> cid;

    for (int i = 0; i < courses.size(); i++) {
        if (courses[i].id == cid) {
            courses.erase(courses.begin() + i);
            cout << "Course deleted!\n";
            return;
        }
    }
    cout << "Course not found!\n";
}

void viewCourses() {
    cout << "\n--- Available Courses ---\n";

    for (int i = 0; i < courses.size(); i++) {
        cout << courses[i].id << " | "
             << courses[i].code << " | "
             << courses[i].name << " | Seats: "
             << courses[i].seats << endl;
    }
}


void registerCourse(Student &s) {
    int cid;
    cout << "Enter Course ID to register: ";
    cin >> cid;

    // Loop through courses
    for (int i = 0; i < courses.size(); i++) {
        if (courses[i].id == cid) {

            // seat check
            if (courses[i].seats == 0) {
                cout << "No seats available!\n";
                return;
            }

            // duplicate check
            for (int j = 0; j < s.registered.size(); j++) {
                if (s.registered[j] == cid) {
                    cout << "Already registered!\n";
                    return;
                }
            }

            // register course
            s.registered.push_back(cid);
            courses[i].seats--;
            cout << "Course registered successfully!\n";
            return;
        }
    }

    cout << "Course not found!\n";
}


void dropCourse(Student &s) {
    int cid;
    cout << "Enter Course ID to drop: ";
    cin >> cid;

    // search in student's registered list
    for (int i = 0; i < s.registered.size(); i++) {
        if (s.registered[i] == cid) {

            // remove course id
            s.registered.erase(s.registered.begin() + i);

            // update seat count
            for (int j = 0; j < courses.size(); j++) {
                if (courses[j].id == cid) {
                    courses[j].seats++;
                }
            }

            cout << "Course dropped!\n";
            return;
        }
    }

    cout << "You are not registered for this course.\n";
}


// -----------------------------------------------
// MENUS
// -----------------------------------------------

void adminMenu() {
    int choice;
    while (true) {
        cout << "\n--- ADMIN MENU ---\n";
        cout << "1. Add Course\n";
        cout << "2. Delete Course\n";
        cout << "3. View Courses\n";
        cout << "4. Back\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1: addCourse(); break;
            case 2: deleteCourse(); break;
            case 3: viewCourses(); break;
            case 4: return;
            default: cout << "Invalid option!\n";
        }
    }
}

void studentMenu(Student &s) {
    int choice;
    while (true) {
        cout << "\n--- STUDENT MENU (" << s.name << ") ---\n";
        cout << "1. View Available Courses\n";
        cout << "2. Register Course\n";
        cout << "3. Drop Course\n";
        cout << "4. View Registered Courses\n";
        cout << "5. Logout\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1: viewCourses(); break;
            case 2: registerCourse(s); break;
            case 3: dropCourse(s); break;
            case 4: s.viewRegistered(courses); break;
            case 5: return;
            default: cout << "Invalid choice!\n";
        }
    }
}

// -----------------------------------------------
// MAIN MENU
// -----------------------------------------------

int main() {
    // Pre-adding one student for demo
    students.push_back(Student(1, "Harsh"));

    int choice;

    while (true) {
        cout << "\n=== COURSE REGISTRATION SYSTEM ===\n";
        cout << "1. Admin Login\n";
        cout << "2. Student Login\n";
        cout << "3. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                adminMenu();
                break;

            case 2: {
                int sid;
                cout << "Enter Student ID: ";
                cin >> sid;

                bool found = false;

                // Old-style loop instead of: for (auto &s : students)
                for (int i = 0; i < students.size(); i++) {
                    if (students[i].id == sid) {
                        studentMenu(students[i]);
                        found = true;
                        break;
                    }
                }

                if (!found)
                    cout << "Student not found!\n";

                break;
            }

            case 3:
                cout << "Exiting system...\n";
                return 0;

            default:
                cout << "Invalid option!\n";
        }
    }

    return 0;
}

