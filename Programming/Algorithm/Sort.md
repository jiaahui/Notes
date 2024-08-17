# Sort

## Quick Sort

```c++
void quick_sort(int arr[], int low, int high) {
    if (low >= high) return;
    // partition
    int pivot = arr[low + high >> 1];
    int i = low - 1, j = high + 1;
    while (i < j) {
        do i++; while (arr[i] < pivot);
        do j--; while (arr[j] > pivot);
        if (i < j) swap(arr[i], arr[j]);
    }
    // recursive
    quick_sort(arr, low, j);
    quick_sort(arr, j + 1, high);
}
```

## Merge Sort

```c++
int temp[100010];

void merge_sort(int arr[], int low, int high) {
    if (low >= high) return;
    int mid = (low + high) >> 1;
    merge_sort(arr, low, mid);
    merge_sort(arr, mid + 1, high);
    // merge
    int k = 0, i = low, j = mid + 1;
    while (i <= mid && j <= high) {
        if (arr[i] <= arr[j]) {
            temp[k++] = arr[i++];
        } else {
            temp[k++] = arr[j++];
        }
    }
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= high) temp[k++] = arr[j++];
    // copy back
    for (int p = low; p <= high; p++) {
        arr[p] = temp[p - low];
    }
}
```
