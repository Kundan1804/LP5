#include <iostream>
#include <omp.h>

using namespace std;

void sequentialBubbleSort(int *, int);
void parallelBubbleSort(int *, int);
void swap(int &, int &);
void mergesort(int[], int, int);
void merge(int[], int, int, int, int);

void sequentialBubbleSort(int *a, int n) {
    int swapped;
    for (int i = 0; i < n; i++) {
        swapped = 0;
        for (int j = 0; j < n - 1; j++) {
            if (a[j] > a[j + 1]) {
                swap(a[j], a[j + 1]);
                swapped = 1;
            }
        }

        if (!swapped)
            break;
    }
}

void parallelBubbleSort(int *a, int n) {
    int swapped;
    for (int i = 0; i < n; i++) {
        swapped = 0;
        int first = i % 2;
#pragma omp parallel for shared(a, first)
        for (int j = first; j < n - 1; j++) {
            if (a[j] > a[j + 1]) {
                swap(a[j], a[j + 1]);
                swapped = 1;
            }
        }

        if (!swapped)
            break;
    }
}

void swap(int &a, int &b) {
    int test;
    test = a;
    a = b;
    b = test;
}

void mergesort(int a[], int i, int j) {
    int mid;
    if (i < j) {
        mid = (i + j) / 2;

#pragma omp parallel sections
        {
#pragma omp section
            {
                mergesort(a, i, mid);
            }

#pragma omp section
            {
                mergesort(a, mid + 1, j);
            }
        }

        merge(a, i, mid, mid + 1, j);
    }
}

void merge(int a[], int i1, int j1, int i2, int j2) {
    int temp[1000];
    int i, j, k;
    i = i1;
    j = i2;
    k = 0;

    while (i <= j1 && j <= j2) {
        if (a[i] < a[j]) {
            temp[k++] = a[i++];
        } else {
            temp[k++] = a[j++];
        }
    }

    while (i <= j1) {
        temp[k++] = a[i++];
    }

    while (j <= j2) {
        temp[k++] = a[j++];
    }

    for (i = i1, j = 0; i <= j2; i++, j++) {
        a[i] = temp[j];
    }
}

int main() {
    int *a, n;
    double start_time, end_time;

    cout << "\n enter total no of elements=>";
    cin >> n;
    a = new int[n];
    cout << "\n enter elements=>";
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // Sequential Bubble Sort
    start_time = omp_get_wtime();
    sequentialBubbleSort(a, n);
    end_time = omp_get_wtime();
    cout << "\nSorted array using Sequential Bubble Sort:\n";
    for (int i = 0; i < n; i++) {
        cout << a[i] << " ";
    }
    cout << "\nTime taken by Sequential Bubble Sort: " << end_time - start_time << " seconds" << endl;

    // Parallel Bubble Sort
    start_time = omp_get_wtime();
    parallelBubbleSort(a, n);
    end_time = omp_get_wtime();
    cout << "\nSorted array using Parallel Bubble Sort:\n";
    for (int i = 0; i < n; i++) {
        cout << a[i] << " ";
    }
    cout << "\nTime taken by Parallel Bubble Sort: " << end_time - start_time << " seconds" << endl;

    // Mergesort
    cout << "\n enter total no of elements for Mergesort=>";
    cin >> n;
    a = new int[n];
    cout << "\n enter elements for Mergesort=>";
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // Sequential Mergesort
    start_time = omp_get_wtime();
    mergesort(a, 0, n - 1);
    end_time = omp_get_wtime();
    cout << "\nSorted array using Sequential Mergesort:\n";
    for (int i = 0; i < n; i++) {
        cout << a[i] << " ";
    }
    cout << "\nTime taken by Sequential Mergesort: " << end_time - start_time << " seconds" << endl;

    // Parallel Mergesort
    start_time = omp_get_wtime();
#pragma omp parallel
    {
#pragma omp single
        {
            mergesort(a, 0, n - 1);
        }
    }
    end_time = omp_get_wtime();
    cout << "\nSorted array using Parallel Mergesort:\n";
    for (int i = 0; i < n; i++) {
        cout << a[i] << " ";
    }
    cout << "\nTime taken by Parallel Mergesort: " << end_time - start_time << " seconds" << endl;

    delete[] a; // Don't forget to free the allocated memory

    return 0;
}