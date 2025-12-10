#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/* ----------------------- CONSTANTS ----------------------- */
#define STUDENT_FILE "students.txt"
#define CREDENTIAL_FILE "credentials.txt"
#define MAX 200  // Maximum students stored in array

/* --------------------- STUDENT STRUCT -------------------- */
struct Student {
    int roll;
    char name[50];
    float marks;
};

/* ------------------ GLOBAL ARRAY + COUNT ------------------ */
struct Student students[MAX];  
int count = 0;                  

/* ---------------- FUNCTION DECLARATIONS ------------------ */
void loadArray();
void saveArray();
int loginSystem();
void addStudent();
void displayStudents();
void searchStudent();
void deleteStudent();
void updateStudent();


/* --------------------- LOAD ALL STUDENTS FROM FILE INTO ARRAY -----------------*/

void loadArray() {
    FILE *file = fopen(STUDENT_FILE, "r");
    struct Student s;

    count = 0;   // reset array

    if (file == NULL) return;

    /* Read all records into student array */
    while (fscanf(file, "%d %s %f", &s.roll, s.name, &s.marks) != EOF) {
        if (count < MAX) {
            students[count] = s;
            count++;
        }
    }

    fclose(file);
}

/* -------------------SAVE ALL ARRAY DATA BACK INTO THE FILE ------------------- */

void saveArray() {
    FILE *file = fopen(STUDENT_FILE, "w");

    for (int i = 0; i < count; i++)
        fprintf(file, "%d %s %.2f\n",
                students[i].roll, students[i].name, students[i].marks);

    fclose(file);
}


/*----------------------LOGIN SYSTEM--------------------------- */

int loginSystem() {
    FILE *file = fopen(CREDENTIAL_FILE, "r");


    if (file == NULL) {
        file = fopen(CREDENTIAL_FILE, "w");
        fprintf(file, "admin admin123\n");
        fclose(file);

        file = fopen(CREDENTIAL_FILE, "r");
    }

    char savedUser[50], savedPass[50];
    fscanf(file, "%s %s", savedUser, savedPass);
    fclose(file);

    char user[50], pass[50];

    printf("\n===== LOGIN =====\n");
    printf("Username: ");
    scanf("%s", user);
    printf("Password: ");
    scanf("%s", pass);

    if (strcmp(user, savedUser) == 0 && strcmp(pass, savedPass) == 0)
        return 1;

    return 0;
}

/*------------------- ADD STUDENT USING ARRAY---------------- */

void addStudent() {
    if (count >= MAX) {
        printf("Array is Full! Cannot add more students.\n");
        return;
    }

    struct Student s;

    printf("Enter Roll No: ");
    scanf("%d", &s.roll);

    printf("Enter Name: ");
    getchar();
    fgets(s.name, sizeof(s.name), stdin);
    s.name[strcspn(s.name, "\n")] = 0;

    printf("Enter Marks: ");
    scanf("%f", &s.marks);

    /* Add to array */
    students[count] = s;
    count++;

    /* Save to file */
    saveArray();

    printf("Student Added Successfully!\n");
}

/* ----------DISPLAY ALL STUDENTS (ARRAY TRAVERSAL)-------- */

void displayStudents() {
    loadArray();

    if (count == 0) {
        printf("No Records Found.\n");
        return;
    }

    printf("\n----- STUDENT LIST -----\n");
    for (int i = 0; i < count; i++) {
        printf("Roll: %d | Name: %s | Marks: %.2f\n",
               students[i].roll, students[i].name, students[i].marks);
    }
}

/* --------------SEARCH STUDENT USING ROLL NUMBER-------------- */

void searchStudent() {
    loadArray();

    int roll, found = 0;
    printf("Enter Roll No to Search: ");
    scanf("%d", &roll);

    for (int i = 0; i < count; i++) {
        if (students[i].roll == roll) {
            printf("Roll: %d | Name: %s | Marks: %.2f\n",
                   students[i].roll, students[i].name, students[i].marks);
            found = 1;
            break;
        }
    }

    if (!found)
        printf("Student Not Found!\n");
}


/* -----------DELETE STUDENT ? SHIFT ARRAY ELEMENTS-----------*/

void deleteStudent() {
    loadArray();

    int roll, found = 0;
    printf("Enter Roll No to Delete: ");
    scanf("%d", &roll);

    for (int i = 0; i < count; i++) {
        if (students[i].roll == roll) {
            found = 1;

            /* Shift all elements left */
            for (int j = i; j < count - 1; j++)
                students[j] = students[j + 1];

            count--;
            saveArray();
            break;
        }
    }

    if (found)
        printf("Student Deleted Successfully!\n");
    else
        printf("Student Not Found!\n");
}


/* -------------------UPDATE STUDENT DETAILS------------------- */

void updateStudent() {
    loadArray();

    int roll, found = 0;
    printf("Enter Roll No to Update: ");
    scanf("%d", &roll);

    for (int i = 0; i < count; i++) {
        if (students[i].roll == roll) {
            found = 1;

            printf("Enter New Name: ");
            getchar();
            fgets(students[i].name, 50, stdin);
            students[i].name[strcspn(students[i].name, "\n")] = 0;

            printf("Enter New Marks: ");
            scanf("%f", &students[i].marks);

            saveArray();
            break;
        }
    }

    if (found)
        printf("Record Updated Successfully!\n");
    else
        printf("Student Not Found!\n");
}


/*---------------------- MAIN ---------------------------- */

int main() {
    if (!loginSystem()) {
        printf("Login Failed. Exiting...\n");
        return 0;
    }

    int choice;

    do {
        printf("\n===== ARRAY-BASED STUDENT MANAGEMENT SYSTEM =====\n");
        printf("1. Add Student\n");
        printf("2. Display Students\n");
        printf("3. Search Student\n");
        printf("4. Delete Student\n");
        printf("5. Update Student\n");
        printf("6. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: addStudent(); break;
            case 2: displayStudents(); break;
            case 3: searchStudent(); break;
            case 4: deleteStudent(); break;
            case 5: updateStudent(); break;
            case 6: printf("Exiting...\n"); break;
            default: printf("Invalid Choice! Try Again.\n");
        }
    } while (choice != 6);

    return 0;
} 
