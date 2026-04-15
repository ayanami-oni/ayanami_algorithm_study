# 基础排序算法

排序算法是计算机科学中最基础也最重要的算法之一。本文档详细介绍几种常见的排序算法，包括其原理、实现和时间复杂度分析。

---

## 目录

1. [冒泡排序（Bubble Sort）](#1-冒泡排序bubble-sort)
2. [选择排序（Selection Sort）](#2-选择排序selection-sort)
3. [插入排序（Insertion Sort）](#3-插入排序insertion-sort)
4. [归并排序（Merge Sort）](#4-归并排序merge-sort)
5. [快速排序（Quick Sort）](#5-快速排序quick-sort)
6. [堆排序（Heap Sort）](#6-堆排序heap-sort)
7. [算法复杂度对比](#7-算法复杂度对比)

---

## 1. 冒泡排序（Bubble Sort）

### 原理

冒泡排序是一种简单的交换排序算法。它重复地遍历要排序的数组，比较相邻的两个元素，如果顺序错误就交换它们。通过多次遍历，最大（或最小）的元素会逐渐"冒泡"到数组的末端。

### 实现

```javascript
function bubbleSort(arr) {
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    let swapped = false;
    for (let j = 0; j < n - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    // 如果没有交换，说明已经排序完成
    if (!swapped) break;
  }
  return arr;
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n) |
| 最坏 | O(n²) |
| 平均 | O(n²) |
| 空间 | O(1) |

### 特点

- **稳定排序**：相等元素的相对位置不会改变
- **原地排序**：不需要额外空间
- **简单直观**：适合教学和小规模数据

---

## 2. 选择排序（Selection Sort）

### 原理

选择排序的工作原理是：从未排序部分中选择最小（或最大）的元素，将其放到已排序部分的末尾。重复这个过程，直到所有元素都排序完成。

### 实现

```javascript
function selectionSort(arr) {
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    let minIdx = i;
    for (let j = i + 1; j < n; j++) {
      if (arr[j] < arr[minIdx]) {
        minIdx = j;
      }
    }
    if (minIdx !== i) {
      [arr[i], arr[minIdx]] = [arr[minIdx], arr[i]];
    }
  }
  return arr;
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n²) |
| 最坏 | O(n²) |
| 平均 | O(n²) |
| 空间 | O(1) |

### 特点

- **不稳定排序**：相等元素的相对位置可能改变
- **原地排序**
- **交换次数最少**：最多只需 n-1 次交换

---

## 3. 插入排序（Insertion Sort）

### 原理

插入排序类似于整理扑克牌的过程。将数组分为已排序区和未排序区，初始时已排序区只有第一个元素。然后逐个将未排序区的元素插入到已排序区的正确位置。

### 实现

```javascript
function insertionSort(arr) {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    const key = arr[i];
    let j = i - 1;
    while (j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = key;
  }
  return arr;
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n) |
| 最坏 | O(n²) |
| 平均 | O(n²) |
| 空间 | O(1) |

### 特点

- **稳定排序**
- **原地排序**
- **对部分有序数组效率高**
- **适合小规模数据**

---

## 4. 归并排序（Merge Sort）

### 原理

归并排序采用分治策略。将数组递归地分成两半，分别排序后再合并。核心是合并两个已排序的数组。

### 实现

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;
  
  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));
  
  return merge(left, right);
}

function merge(left, right) {
  const result = [];
  let i = 0, j = 0;
  
  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }
  
  return result.concat(left.slice(i)).concat(right.slice(j));
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n log n) |
| 最坏 | O(n log n) |
| 平均 | O(n log n) |
| 空间 | O(n) |

### 特点

- **稳定排序**
- **时间复杂度稳定**
- **需要额外空间**
- **适合大规模数据**

---

## 5. 快速排序（Quick Sort）

### 原理

快速排序同样采用分治策略。选择一个"基准"元素（pivot），将数组分成两部分：小于基准的元素和大于基准的元素。然后递归地对两部分进行快速排序。

### 实现

```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
  if (low < high) {
    const pivotIndex = partition(arr, low, high);
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
  }
  return arr;
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low - 1;
  
  for (let j = low; j < high; j++) {
    if (arr[j] < pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }
  
  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
  return i + 1;
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n log n) |
| 最坏 | O(n²) |
| 平均 | O(n log n) |
| 空间 | O(log n) |

### 特点

- **不稳定排序**
- **原地排序**
- **实际应用中速度最快**
- **最坏情况可通过随机化避免**

---

## 6. 堆排序（Heap Sort）

### 原理

堆排序利用二叉堆（通常是完全二叉堆）的性质。先将数组构建成最大堆，然后反复取出堆顶最大元素并调整堆结构。

### 实现

```javascript
function heapSort(arr) {
  const n = arr.length;
  
  // 构建最大堆
  for (let i = Math.floor(n / 2) - 1; i >= 0; i--) {
    heapify(arr, n, i);
  }
  
  // 逐个取出堆顶元素
  for (let i = n - 1; i > 0; i--) {
    [arr[0], arr[i]] = [arr[i], arr[0]];
    heapify(arr, i, 0);
  }
  return arr;
}

function heapify(arr, n, i) {
  let largest = i;
  const left = 2 * i + 1;
  const right = 2 * i + 2;
  
  if (left < n && arr[left] > arr[largest]) {
    largest = left;
  }
  if (right < n && arr[right] > arr[largest]) {
    largest = right;
  }
  
  if (largest !== i) {
    [arr[i], arr[largest]] = [arr[largest], arr[i]];
    heapify(arr, n, largest);
  }
}
```

### 复杂度分析

| 情况 | 时间复杂度 |
|------|-----------|
| 最好 | O(n log n) |
| 最坏 | O(n log n) |
| 平均 | O(n log n) |
| 空间 | O(1) |

### 特点

- **不稳定排序**
- **原地排序**
- **最坏情况也是 O(n log n)**
- **适合需要稳定最坏情况保障的场景**

---

## 7. 算法复杂度对比

### 时间复杂度一览

| 算法 | 最好 | 平均 | 最坏 | 空间 | 稳定 |
|------|------|------|------|------|------|
| 冒泡排序 | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| 选择排序 | O(n²) | O(n²) | O(n²) | O(1) | ❌ |
| 插入排序 | O(n) | O(n²) | O(n²) | O(1) | ✅ |
| 归并排序 | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ |
| 快速排序 | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ |
| 堆排序 | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ |

### 选择建议

- **小规模数据（n < 50）**：插入排序、冒泡排序
- **大规模数据**：快速排序、归并排序、堆排序
- **需要稳定排序**：归并排序、插入排序、冒泡排序
- **需要稳定最坏情况**：堆排序、归并排序
- **追求综合性能**：快速排序（实际应用广泛）

---

## 总结

排序算法是算法学习的基石。理解每种算法的原理、特点和适用场景，能够帮助我们在实际开发中做出更好的选择。不同场景下，最优的排序算法可能不同，掌握多种排序算法是每个程序员必备的技能。
