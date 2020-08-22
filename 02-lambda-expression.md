# JAVA 8 - LAMBDA EXPRESSION

## 1. WHAT
- `Lambda expression` là một tính năng mới và cực kì hot của java 8, cải tiến của cách viết `anonymous class`, nó được sử dụng với [`functional interface`](./01-functional-interface.md) tạo nên sự mạnh mẽ của java 8.

- Cách viết rút gọn của `lambda expression` là [`method reference`](./03-method-reference.md).

## 2. WHY

- Sử dụng `lambda expression` để cho code ngắn gonj, dễ hiểu, trong sáng hơn.

- Đặt trường hợp nếu interface chỉ có 1 `abstract method` thì việc viết bắng `anonymous class` rất dài dòng, khó kiểm soát, sử dụng `lambda expression` cho code ngắn hơn nhưng hiệu năng tương tự.

## 3. WHEN

- Dùng bất cứ khi nào bạn muốn và được phép, cụ thể:
	+ Sử dụng khi cài đè 1 hàm của một `functional interface`
	+ Sử dụng khi làm việc với `Collections` và `Stream` (lọc, ánh xạ list, ...)
- Được sử dụng chủ yếu khi làm việc với `Collections` và `Stream`.

## 4. HOW
- Cú pháp rất đơn giản nhưng nếu chưa hiểu bản chất thì sẽ rất trừu tượng 
`(tham_số_1, tham_số_2, ...., tham_số_n) -> {thân_hàm_cần_viết}`

## 5. EXAMPLES

Ta có `functional interface` như sau:

```java
@FunctionalInterface
public interface IMessage {
	void showMessage(String message);

	default void defaultMethod() {
		System.out.println("This is a default method");
	}
}
```

Và khi sử dụng `anonymous class` như trước:

```java
IMessage message = new IMessage() {
	@Override
	public void showMessage(String message) {
		System.out.println(message);
	}
};
message.showMessage("This is a message when using anonymous class.");
```

Còn khi sử dụng `lambda expression` thì trông sẽ như thế này:

```java
IMessage message2 = (String tempMessage) -> {
	System.out.println(tempMessage);
};
message2.showMessage("This is a message when using lambda expression.");
```

Giải thích môt chút về cú pháp của `lambda expression`:

1. `(String tempMessage)`: tức là truyền vào hàm duy nhất của `functional interface IMessage` tham số kiểu `String` có tên là `tempMessage`.

2. `->`: dấu này là bắt buộc do cú pháp.

3. `{ System.out.println(tempMessage); }` : nội dung của hàm mình cài đè.

Cả 3 ý trên gộp lại đúng với cấu trúc: `(tham số truyền vào) -> {thân hàm}`

Có một số cách viết khác ngắn gọn hơn nhiều như sau:

1. Không cần kiểu tham số truyền vào, mặc định JVM sẽ xác định tùy vào từng ngữ cảnh:

```java
IMessage message3 = (mess) -> {
	System.out.println(mess);
};
message3.showMessage("mess3");
```

2. Không cần dấu ngoặc đon ở danh sách tham số truyền vào khi chỉ có 1 tham số

```java
IMessage message4 = temp -> {
	System.out.println(temp);
};
message4.showMessage("mess4");
```

3. Không cần dấu ngoặc nhọn ở thân hàm khi thân hàm chỉ có 1 câu lệnh, đây là cách viết ngắn gọn và dễ đọc nhất

```java
IMessage message5 = temp2 -> System.out.println(temp2);
message5.showMessage("mess5");
```

Lưu ý rằng khi mà cài đè hàm của `interface` không có tham số truyền vào ta chỉ viết dấu ngoặc tròn mà thôi

```java
new Thread(() -> System.out.println("ABC")).start();
```

Bây giờ, xét tới ví dụ tính toán với `interface ICalculator`:

```java
@FunctionalInterface
public interface ICalculator<T> {
	T cacultate(T a, T b);
}
```

Áp dụng `interface` này để tính tổng:

```java
ICalculator<Integer> add = (a, b) -> a + b;
System.out.println(add.cacultate(5, 10));
```

Tính cả hiệu nữa nhé:

```java
ICalculator<Float> sub = (a, b) -> a - b;
System.out.println(sub.cacultate(Float.valueOf(20.5f), Float.valueOf(7)));
```

Tích cũng không bỏ qua:

```java
ICalculator<Integer> multiply = (a, b) -> a * b;
System.out.println(multiply.cacultate(34, 5));
```

Cuối cùng là thương:

```java
ICalculator<Integer> divide = (a, b) -> {
	if (b == 0) {
		return 0;
	}
	return a / b;
};
System.out.println(divide.cacultate(34, 8));
```

#### NOTE

Một số ví dụ khác phong phú để hiểu hơn về `lambda expression` nhé:

```java
n -> n % 2 != 0;  // --> truyền vào n và trả về true nếu n là lẻ

(char c) -> c == 'y'; // --> truyền vào kí tự c và trả về true nếu c là 'y'

(x, y) -> x + y;  // -> truyền vào x và y, trả về tổng 2 số đó

(int a, int b) -> a * a + b * b;  

() -> 42  // --> không truyền vào gì và trả về 42

() -> { return 3.14 };   // --> không truyền vào gì và trả về 3.14

(String s) -> { System.out.println(s); }; 

() -> { System.out.println("Hello World!"); };

```
