---
tags: Задачи
---
## Дано
2 массива отсортированных по возрастанию. Объединить 2 массива в один, отсортированный по возрастанию
## Примеры
**Example 1:**

**Input:** nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
**Output:** [1,2,2,3,5,6]
**Explanation:** The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.

**Example 2:**

**Input:** nums1 = [1], m = 1, nums2 = [], n = 0
**Output:** [1]
**Explanation:** The arrays we are merging are [1] and [].
The result of the merge is [1].

## Решение
```java
class Solution {

    public void merge(int[] nums1, int m, int[] nums2, int n) {

        if (m == 0) { // Если первый массив пуст

            for (int i = 0; i < n; i++) { // Перепиши первый массив элементами 
            //второго массива

            nums1[i] = nums2[i];

            }

        }

        int[] result = new int[m + n]; //Результирующий массив

        int i = m - 1;

        int j = n - 1;

        int k = m + n - 1;

        while (k >= 0) {

            if (i == -1) {

                result[k] = nums2[j];

                k--;

                j--;

            } else if (j == -1) {

                result[k] = nums1[i];

                k--;

                i--;

            } else {

                if (nums1[i] < nums2[j]) {

                    result[k] = nums2[j];

                    j--;

                    k--;

                } else {

                    result[k] = nums1[i];

                    i--;

                    k--;

                }

            }

        }

        for (i = 0; i < n + m; i++) {

            nums1[i] = result[i];

        }

    }

}
```
