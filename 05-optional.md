# JAVA 8 - OPTIONAL 

- [`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) trong java 8 là một container, nó bao bọc các kiểu dữ liệu khác.

- Mục đích của `Optional` là tránh `NullPointerException` và tối ưu hóa code khi sử dụng các hàm tiện tích của nó.

Ở bài này chúng ta sẽ đi vào ví dụ và giải thích cụ thể luôn.

Chúng ta có class `Student`:

```java
public class Student {

	private int id;
	private String fullName;
	private java.util.Date birthday;

	// getters, setters, constructors

}
```

Khi ta muốn lấy ra 1 list tên của sinh viên thỏa mãn điều kiện chứa 1 cụm kí tự nào đó, ta sử dụng hàm:

```java
public static List<String> findStudentNameList(String key, List<Student> students) {
	return students.stream()
					.map(Student::getFullName)
					.filter(t -> t.contains(key))
					.collect(Collectors.toList());
}
```

Nhưng khi sử dụng `Optional`, nó sẽ bao bên ngoài `List<String>`:

```java
public static Optional<List<String>> findStudentNameListUsingOptional(String key, List<Student> students) {
	return Optional.ofNullable(
				students.stream()
						.map(Student::getFullName)
						.filter(t -> t.contains(key))
						.collect(Collectors.toList())
			);
}
```

`ofNullable()` là 1 hàm static của `Optional`, nó trả về 1 `Optional` khi data trong nó không `null` và nó sẽ trả về 1 `empty Optional` khi data trong nó bị `null`, ngoài ra ta có thể dùng `Optional.of()` nó cũng trả về 1 optional nhưng không bao được trường hợp `null`.


Khi ta cần lấy duy nhất 1 tên đầu tiên có kí tự mong muốn:

```java
public static String findTheFirstMatch(String key, List<Student> students) {
	List<String> list = students.stream()
								.map(Student::getFullName)
								.filter(t -> t.contains(key))
								.collect(Collectors.toList());
	if (list != null && !list.isEmpty()) {
		return list.get(0);
	}
	return null;
}
```

Còn khi sử dụng `Optional`:

```java
public static Optional<String> findTheFirstMatchUsingOptional(String key, List<Student> students) {
	return students.stream().map(Student::getFullName).filter(t -> t.contains(key)).findFirst();
}
```

Bước xây dựng hàm đã xong, bây giờ chúng ta hãy sử dụng các hàm vừa viết để xem sự khác biệt nhé.

Trước tiên chúng ta có 1 list sinh viên:

```java
List<Student> students = Arrays.asList(
				new Student(1, "Nguyễn Văn A", new java.util.Date()),
				new Student(2, "Nguyễn Văn A", new java.util.Date()),
				new Student(3, "Phạm Thị Thu Thảo", new java.util.Date()),
				new Student(4, "Nguyễn Khắc Hưng", new java.util.Date()),
				new Student(5, "Vũ Thị Huyền", new java.util.Date()),
				new Student(6, "Lê Đức Trung", new java.util.Date()),
				new Student(7, "Lý Hoài Nam", new java.util.Date())
			);
```

Sử dụng hàm tìm list tên theo cách thông thường:

```java
findStudentNameList("a", students).forEach(System.out::println);
```

Sử dụng hàm tìm list tên theo `Optional` --> kết quả cho ra tương tự

```java
findStudentNameListUsingOptional("a", students)
	.map(Collection::stream)
	.orElseGet(Stream::empty)
	.filter(Objects::nonNull)
	.forEach(System.out::println);
```

Đoạn `map(Collection::stream).orElseGet(Stream::empty)` là đoạn convert từ `Optional<List<String>>` sang `List<String>` --> Tại sao phải chuyển?
Vì ta cần thao tác khác với `List` này, không thể để dưới dạng `Optional` được, `Optional` chỉ là dạng sơ  khởi và tránh gặp `null` mà thôi, khi cho ra kết quả cuối cùng ta vẫn phải đưa về dạng chính.

Một ví dụ khác dễ hình dung hơn và đơn giảm hơn như sau: dùng hàm tìm tên đầu tiên trùng khớp với kí tự cần tìm một cách bình thường

```java
System.out.println(findTheFirstMatch("n", students));
```

Giả sử chúng ta muốn tìm chữ `z` thì sẽ không có kết quả, và trong trường hợp không có kết quả như vậy, ta muốn in ra một thông báo rằng không có kết quả, thì chúng ta sẽ phải code thêm một chút:

```java
String findTheFirstMatch = findTheFirstMatch("z", students);
if (findTheFirstMatch != null) {
	System.out.println(findTheFirstMatch);
} else {
	System.out.println("Không tìm thấy");
}
```

Nhưng nếu ta sử dụng Optional thì mọi chuyện sẽ dễ dàng hơn nhiều:

```java
findTheFirstMatchUsingOptional("z", students).map(t -> {
	System.out.println(t);
	return t;
}).orElseGet(() -> {
	System.out.println("Không tìm thấy");
	return null;
});
```

Nói chung, `Optional` là giải pháp hay và hiệu quả khi tối ưu code, tránh `null` và không phải kiểm tra `null`, ngoài ra còn nhiều hàm hay và thường được sử dụng khác của `Optional`, xem tại [document chính chủ của Oracle](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)
