# 二分查找算法

## 算法概述

二分查找（Binary Search）是一种在**有序数组**中查找目标元素的搜索算法。其核心思想是将目标值与数组中间元素进行比较，如果相等则查找成功；如果目标值大于中间元素，则在数组右半部分继续查找；如果目标值小于中间元素，则在数组左半部分继续查找。通过不断缩小搜索范围，直到找到目标元素或确定目标不存在。

## 算法步骤

1. 初始化左指针 `left = 0`，右指针 `right = n - 1`
2. 当 `left <= right` 时，重复执行：
   - 计算中间位置 `mid = Math.floor((left + right) / 2)`
   - 如果 `arr[mid] === target`，返回 `mid`（找到目标）
   - 如果 `arr[mid] < target`，则 `left = mid + 1`（在右半部分查找）
   - 如果 `arr[mid] > target`，则 `right = mid - 1`（在左半部分查找）
3. 如果循环结束仍未找到，返回 `-1`（目标不存在）

## 代码实现

### TypeScript 实现

```typescript
function binarySearch(arr: number[], target: number): number {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    
    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return -1;
}
```

### 递归实现

```typescript
function binarySearchRecursive(
  arr: number[], 
  target: number, 
  left: number = 0, 
  right: number = arr.length - 1
): number {
  // 基本情况：搜索空间为空
  if (left > right) {
    return -1;
  }

  const mid = Math.floor((left + right) / 2);

  if (arr[mid] === target) {
    return mid;
  } else if (arr[mid] < target) {
    return binarySearchRecursive(arr, target, mid + 1, right);
  } else {
    return binarySearchRecursive(arr, target, left, mid - 1);
  }
}
```

## 复杂度分析

| 指标 | 复杂度 | 说明 |
|------|--------|------|
| 时间复杂度 | O(log n) | 每次搜索将范围缩小一半 |
| 空间复杂度 | O(1) | 迭代版本仅使用常数额外空间 |
| 空间复杂度（递归） | O(log n) | 递归版本需要递归栈空间 |

## 使用场景

- **有序数组查找**：当数据已排序时，二分查找是最高效的搜索方式
- **查找第一个/最后一个位置**：稍微修改算法可找到目标值首次或最后出现的位置
- **查找边界值**：可用于查找满足某个条件的最小/最大值
- **旋转排序数组**：经过修改后可处理部分旋转的有序数组

## 示例

```typescript
const sortedArray = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19];

console.log(binarySearch(sortedArray, 7));   // 输出: 3
console.log(binarySearch(sortedArray, 6));   // 输出: -1
console.log(binarySearch(sortedArray, 1));   // 输出: 0
console.log(binarySearch(sortedArray, 19)); // 输出: 9
```

## 变体：查找左侧边界

```typescript
function lowerBound(arr: number[], target: number): number {
  let left = 0;
  let right = arr.length; // 注意：right 初始化为 length

  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }

  return left;
}
```

## 注意事项

1. **数据必须有序**：二分查找仅适用于已排序的数据
2. **整数溢出**：当使用 `(left + right) / 2` 时，大数组可能导致整数溢出，建议使用 `left + (right - left) / 2`
3. **边界条件**：仔细处理 `left <= right` 或 `left < right` 的判断条件
4. **精确性**：根据需求选择合适的变体（精确查找、左侧边界、右侧边界）
