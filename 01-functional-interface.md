# JAVA 8 - FUNCTIONAL INTERFACE


## 1. WHAT?
- Trước java 8, `functional inteface` được gọi là `Single Abstract Method Inteface` (`SAM interface`),
tức là interface mà chỉ có 1 abstract method duy nhất (không tính default method).
- Sang java 8, các interface như thế được gọi là `functional interface`.

## 2. WHY?
- Tại sao phải có `funtional interface`?

    - Thực ra `functional inteface` sinh ra để nhằm implement một trong 5 nguyên lý của [SOLID](https://toidicodedao.com/2015/03/24/solid-la-gi-ap-dung-cac-nguyen-ly-solid-de-tro-thanh-lap-trinh-vien-code-cung/), đó chính là nguyên lý đầu tiên: `Single responsibility principle (S)` là `mỗi một class/interface nên chỉ chứa 1 nhiệm vụ duy nhất`.

    - Để sử dụng [`lambda expession`](./02-lambda-expression.md), [`method reference`](./03-method-reference.md).

    - Để tiện dụng hơn khi trước kia phải dùng kĩ thuật `anonymous class`.

## 3. WHEN

-	Sử dụng khi mà inteface của ta dùng cho mục đích đơn (thực hiện 1 chức năng) hoặc dùng để áp dụng `lambda expression`.
-	Trong thư viện của java có rất nhiều `functional interface` được để trong package `java.util.funtion`, chi tiết xem tại [đây](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html).

## 4. HOW

-	Basic functional interface
-	Funtional interface annotation: `@FunctionalInterface`
-	Sample (using lambda, method reference)

## 5. EXAMPLES

Sau đây là một ví dụ về `fucntional interface`, vì bên trong nó chỉ chứa một `abstract method` duy nhất:
```java
public interface Ex01 { // functional interface

	void showMessage();

}
```
Interface bên dưới đây *KHÔNG* là một `fucntional interface`, vì bên trong nó chứa 2 `abstract method`.


```java
public interface Ex02 { // NOT functional interface

	String getMessage();

	void showMessage();
	
}
```

Nhưng khi có 1 `abstract method` và 1 `default method` thì interface đó vẫn được coi là `functional interface`:
```java
public interface Ex03 {// functional interface

	void showMessage(String mess);

	default String concatMessage(String mess1, String mess2) {
		return mess1 + mess2;
	};

	default String getMessage(String mess1) {
		return mess1;
	};

}
```

Thậm chí, khi bạn cài đè phương thức của class `Object` thì interface đó vẫn là functional interface với 1 abstract method bên trong nó:
```java
public interface Ex04 {// functional interface

	void showMessage(String mess);

	@Override
	boolean equals(Object other);

}
```

Còn nếu như chỉ có `override method` mà không có `abstract medthod` thì cũng không là `fucntional interface` nhé, vì điều kiện cần của `functional interface` là phải có duy nhất 1 `abstract method` và method `equals` kia là của class `Object` chứ không phải của interface của chúng ta:
```java
public interface Ex06_Error01 {// NOT functional interface

	@Override
	boolean equals(Object other);

}
```

Interface của chúng ta chứa cả `override method` và `default method` mà không chứa một `abstract method` nào thì sao? Nó vẫn không là `functional interface` đâu:
```java
public interface Ex07_Error02 {// NOT functional interface

	@Override
	boolean equals(Object other);

	default String getMess(String message) {
		return message;
	}

}
```

Nếu như sử dụng annotation `@FunctionalInterface`, annotation này sẽ xác định đây là 1 `functional interface` và khi biên dịch nó sẽ phát hiện ra xem nếu trong `interface` này không thỏa mãn các điều  kiện của `functional interface` nó sẽ báo lỗi:
```java
@FunctionalInterface
public interface Ex05_Annotation {// functional interface

	void showMessage(String mess);

	default String getMess(String mess) {
		return mess;
	}

	@Override
	boolean equals(Object other);

}
```

Còn cách sử dụng `functional interface` thì sao? Vẫn như những gì đã được biết, chúng ta sẽ tạo một `implement` của `interface` (bằng cách tạo hoàn toàn một class mới hoặc sử dụng kĩ thuật `anonymous class`) và khởi tạo 1 `instance` từ nó thôi, rất đơn giản:
```java
Ex03 ex03 = new Ex03() {
    @Override
    public void showMessage(String mess) {
        System.out.println(mess);
    }
};
ex03.showMessage("124567575");
```

Nhưng nếu kết hợp với sử dụng `lambda expression` thì mọi chuyện sẽ rất kì diệu:
```java
Ex03 sampleChuan = (mess) -> {
    System.out.println(mess);
};
```
hoặc
```java
Ex03 sample = mess -> System.out.println(mess);
```

Và sử dụng chúng như bình thường:
```java
sample.showMessage("abccccccccccccccc");
```

Phần `lambda expression` này sẽ được đề cập sau.