# 算法

## 排序算法

### （1）冒泡排序

```java
public class BubbleSort {
	@SuppressWarnings("all")
	public static void main(String[] args) {
		int[] arr = {10, 12, 4, 9, 3, 13, 5, 1, 22, 17};
		int bigger = arr[0];
		for(int i = 0; i < arr.length; i++) {
            //j < arr.length - i - 1是关键
			for(int j = 0; j < arr.length - i - 1; j++) {
                //如果前一个数比后一个更大，就交换两者
				if(arr[j] > arr[j + 1]) {
					bigger = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = bigger;
				}
			}
		}
		
		for(int num : arr) {
			System.out.println(num);
		}
	}
}
```

