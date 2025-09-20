#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define MAX 100

typedef struct {
    int roll;
    char name[50];
    float marks;
} Student;

Student students[MAX];
int count = 0;

void insertStudent();
void deleteStudent();
void updateStudent();
void displayStudents();
int linearSearch(int roll);
int binarySearch(int roll);
void bubbleSortByRoll();
void selectionSortByMarks();
void insertionSortByMarks();
void quickSortByRoll(int low, int high);
int partition(int low, int high);
void mergeSortByMarks(int l, int r);
void merge(int l, int m, int r);
void filterByMarks(float threshold);
void searchMenu();
void sortMenu();

int main() {
    int choice;

    do {
        printf("\n--- Student Record Manager ---\n");
        printf("1. Insert Student\n");
        printf("2. Delete Student\n");
        printf("3. Update Student\n");
        printf("4. Display All Students\n");
        printf("5. Search Student\n");
        printf("6. Sort Students\n");
        printf("7. Filter by Marks\n");
        printf("0. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: insertStudent(); break;
            case 2: deleteStudent(); break;
            case 3: updateStudent(); break;
            case 4: displayStudents(); break;
            case 5: searchMenu(); break;
            case 6: sortMenu(); break;
            case 7: {
                float threshold;
                printf("Enter marks threshold: ");
                scanf("%f", &threshold);
                filterByMarks(threshold);
                break;
            }
            case 0: printf("Exiting...\n"); break;
            default: printf("Invalid choice! Try again.\n");
        }
    } while (choice != 0);

    return 0;
}

void insertStudent() {
    if (count >= MAX) {
        printf("Student list full!\n");
        return;
    }
    Student s;
    printf("Enter Roll No: ");
    scanf("%d", &s.roll);
    getchar();
    printf("Enter Name: ");
    fgets(s.name, sizeof(s.name), stdin);
    s.name[strcspn(s.name, "\n")] = 0;
    printf("Enter Marks: ");
    scanf("%f", &s.marks);

    students[count++] = s;
    printf("Student inserted successfully.\n");
}

void deleteStudent() {
    int roll;
    printf("Enter Roll No to delete: ");
    scanf("%d", &roll);

    int pos = linearSearch(roll);
    if (pos == -1) {
        printf("Student not found.\n");
        return;
    }

    for (int i = pos; i < count - 1; i++) {
        students[i] = students[i + 1];
    }
    count--;
    printf("Student deleted successfully.\n");
}

void updateStudent() {
    int roll;
    printf("Enter Roll No to update: ");
    scanf("%d", &roll);

    int pos = linearSearch(roll);
    if (pos == -1) {
        printf("Student not found.\n");
        return;
    }

    printf("Updating details for Roll No %d\n", roll);
    getchar();
    printf("Enter new Name: ");
    fgets(students[pos].name, sizeof(students[pos].name), stdin);
    students[pos].name[strcspn(students[pos].name, "\n")] = 0;
    printf("Enter new Marks: ");
    scanf("%f", &students[pos].marks);

    printf("Student updated successfully.\n");
}

void displayStudents() {
    if (count == 0) {
        printf("No students to display.\n");
        return;
    }
    printf("\n%-10s %-20s %-10s\n", "Roll No", "Name", "Marks");
    printf("-------------------------------------------\n");
    for (int i = 0; i < count; i++) {
        printf("%-10d %-20s %-10.2f\n", students[i].roll, students[i].name, students[i].marks);
    }
}

int linearSearch(int roll) {
    for (int i = 0; i < count; i++) {
        if (students[i].roll == roll)
            return i;
    }
    return -1;
}

int binarySearch(int roll) {
    int low = 0, high = count - 1;
    while (low <= high) {
        int mid = (low + high) / 2;
        if (students[mid].roll == roll)
            return mid;
        else if (students[mid].roll < roll)
            low = mid + 1;
        else
            high = mid - 1;
    }
    return -1;
}

void bubbleSortByRoll() {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (students[j].roll > students[j + 1].roll) {
                Student temp = students[j];
                students[j] = students[j + 1];
                students[j + 1] = temp;
            }
        }
    }
    printf("Students sorted by roll number (Bubble Sort).\n");
}

void selectionSortByMarks() {
    for (int i = 0; i < count - 1; i++) {
        int max_idx = i;
        for (int j = i + 1; j < count; j++) {
            if (students[j].marks > students[max_idx].marks) {
                max_idx = j;
            }
        }
        if (max_idx != i) {
            Student temp = students[i];
            students[i] = students[max_idx];
            students[max_idx] = temp;
        }
    }
    printf("Students sorted by marks (Selection Sort).\n");
}

void insertionSortByMarks() {
    for (int i = 1; i < count; i++) {
        Student key = students[i];
        int j = i - 1;
        while (j >= 0 && students[j].marks < key.marks) {
            students[j + 1] = students[j];
            j--;
        }
        students[j + 1] = key;
    }
    printf("Students sorted by marks (Insertion Sort).\n");
}

void quickSortByRoll(int low, int high) {
    if (low < high) {
        int pi = partition(low, high);
        quickSortByRoll(low, pi - 1);
        quickSortByRoll(pi + 1, high);
    }
}

int partition(int low, int high) {
    int pivot = students[high].roll;
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (students[j].roll < pivot) {
            i++;
            Student temp = students[i];
            students[i] = students[j];
            students[j] = temp;
        }
    }
    Student temp = students[i + 1];
    students[i + 1] = students[high];
    students[high] = temp;
    return i + 1;
}

void mergeSortByMarks(int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSortByMarks(l, m);
        mergeSortByMarks(m + 1, r);
        merge(l, m, r);
    }
}

void merge(int l, int m, int r) {
    int n1 = m - l + 1;
    int n2 = r - m;

    Student L[n1], R[n2];

    for (int i = 0; i < n1; i++) L[i] = students[l + i];
    for (int j = 0; j < n2; j++) R[j] = students[m + 1 + j];

    int i = 0, j = 0, k = l;

    while (i < n1 && j < n2) {
        if (L[i].marks >= R[j].marks) {
            students[k++] = L[i++];
        } else {
            students[k++] = R[j++];
        }
    }
    while (i < n1) students[k++] = L[i++];
    while (j < n2) students[k++] = R[j++];
}

void filterByMarks(float threshold) {
    printf("\nStudents with marks above %.2f:\n", threshold);
    printf("%-10s %-20s %-10s\n", "Roll No", "Name", "Marks");
    printf("-------------------------------------------\n");
    int found = 0;
    for (int i = 0; i < count; i++) {
        if (students[i].marks > threshold) {
            printf("%-10d %-20s %-10.2f\n", students[i].roll, students[i].name, students[i].marks);
            found = 1;
        }
    }
    if (!found) {
        printf("No students found with marks above %.2f\n", threshold);
    }
}

void searchMenu() {
    if (count == 0) {
        printf("No students to search.\n");
        return;
    }

    int choice, roll;
    printf("\n--- Search Menu ---\n");
    printf("1. Linear Search\n");
    printf("2. Binary Search (Requires sorted by roll)\n");
    printf("3. Interpolation Search (Requires sorted by roll)\n");
    printf("Enter choice: ");
    scanf("%d", &choice);

    printf("Enter Roll No to search: ");
    scanf("%d", &roll);

    int pos = -1;
    if (choice == 1) {
        pos = linearSearch(roll);
    } else if (choice == 2) {
        bubbleSortByRoll();
        pos = binarySearch(roll);
    } else if (choice == 3) {
        bubbleSortByRoll();
        int low = 0, high = count - 1;
        while (low <= high && roll >= students[low].roll && roll <= students[high].roll) {
            if (low == high) {
                if (students[low].roll == roll) pos = low;
                break;
            }
            int posi = low + ((double)(high - low) / (students[high].roll - students[low].roll)) * (roll - students[low].roll);

            if (students[posi].roll == roll) {
                pos = posi;
                break;
            } else if (students[posi].roll < roll) {
                low = posi + 1;
            } else {
                high = posi - 1;
            }
        }
    } else {
        printf("Invalid choice.\n");
        return;
    }

    if (pos != -1) {
        printf("Student found:\n");
        printf("Roll No: %d\nName: %s\nMarks: %.2f\n", students[pos].roll, students[pos].name, students[pos].marks);
    } else {
        printf("Student not found.\n");
    }
}

void sortMenu() {
    if (count == 0) {
        printf("No students to sort.\n");
        return;
    }

    int choice;
    printf("\n--- Sort Menu ---\n");
    printf("1. Bubble Sort by Roll No\n");
    printf("2. Selection Sort by Marks (descending)\n");
    printf("3. Insertion Sort by Marks (descending)\n");
    printf("4. Quick Sort by Roll No\n");
    printf("5. Merge Sort by Marks (descending)\n");
    printf("Enter choice: ");
    scanf("%d", &choice);

    switch(choice) {
        case 1: bubbleSortByRoll(); break;
        case 2: selectionSortByMarks(); break;
        case 3: insertionSortByMarks(); break;
        case 4: quickSortByRoll(0, count - 1);
                printf("Students sorted by roll number (Quick Sort).\n");
                break;
        case 5: mergeSortByMarks(0, count - 1);
                printf("Students sorted by marks (Merge Sort).\n");
                break;
        default: printf("Invalid choice.\n");
    }
}
