# 排序

## 冒泡排序
每一次比较，如果大的数据在前，将相邻的数据做交换。

顶层每次遍历，都会将大的数据排到最后。（大的气泡首先排上来）
```java
public static void main(String[] args) {
    int[] array = {4,2,1,5,3};
    for(int i = 0;i<array.length-1;i++){
        for(int j = 0;j<array.length-1-i;j++){
            if(array[j]>array[j+1]){
                int temp = array[j];
                array[j] = array[j+1];
                array[j+1] = temp; 
            }
        }
    }		
    for (int i = 0;i<array.length;i++) {
        System.out.println(array[i]);
    }		
}
```

## 直接插入排序
从第2个数开始，倒序循环比较，如果当前要插入的数小于排序好比较的数，就将数组后移。直到比较到第一位仍然小于，就插入到第一位。
```java
public static void main(String[] args) {
    int[] array = { 4, 2, 1, 5, 3 };
    int temp;
    for (int i = 1; i < array.length; i++) {

        // 待排元素小于有序序列的最后一个元素时，向前插入
        if (array[i] < array[i - 1]) {
            temp = array[i];
            for (int j = i; j >= 0; j--) {
                if (j > 0 && array[j - 1] > temp) {
                    array[j] = array[j - 1];
                } else {
                    array[j] = temp;
                    break;
                }
            }
        }
    }

    System.out.println(Arrays.toString(array));
}
```
