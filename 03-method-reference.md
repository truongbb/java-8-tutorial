# JAVA 8 - METHOD REFERENCE

## 1. WHAT

Chúng ta sử dụng [`lambda expression`](./02-lambda-expression.md) để tạo `anonymous class`, nhưng trong 1 vài ngữ cảnh, `method` trong `anonymous class` không thực hiện thêm bất cứ hành động nào mà chỉ sử dụng code có sẵn của hàm đã được viết khi ấy, [`lambda expression`](./02-lambda-expression.md) chỉ việc gọi, và như vậy, code sẽ cảm giác thừa, những lúc như thế, chúng ta sử dụng `method reference`.

## 2. WHY

`Method reference` được sinh ra để giảm thiểu code, dễ đọc, dễ hiểu và dễ sử dụng khi cần (trường hợp nêu trên).

## 3. WHEN

Dùng khi `lambda` sử dụng chỉ để gọi hàm mà không thực hiện thêm hành động nào cả.

## 4. HOW

Có 4 kiểu:

- Tham chiếu đến một `static method`: `<Tên_class>::<tên_hàm_Static_tham_chiếu_tới>`

- Tham chiếu tới một `constructor`: `<Tên_class>::new`

- Tham chiếu tới một `instance method` của một đối tượng tùy ý của 1 kiểu cụ thể:  `<Tên_class>::<tên_instance_method_tham_chiếu_tới>`

- Tham chiếu tới một `instance method` của một đối tượng tùy ý cụ thể:  `<Tên_object>::<tên_instance_method_tham_chiếu_tới>`


## 5. EXAMPLE


### 5.1 Example 1

Ta có list và 2 `method static` sau:

```java
public class Ex01 {
	List<Integer> list = Arrays.asList(1, 2, 8, 3, 4, 9, 0, 14, 5);

	public static void printOddNumber(int num) {
		if (num % 2 == 0) {
			System.out.println(num);
		}
	}

	static <T, R> List<R> convertToInteger(Function<T, R> function, List<T> source) {
		List<R> destiny = new ArrayList<>();
		for (T item : source) {
			R value = function.apply(item);
			destiny.add(value);
		}
		return destiny;
	}
}
```

Và khi sử dụng 2 hàm trên với `lambda expression`:

```java
list.forEach(t -> Ex01.printOddNumber(t));
```

Lúc này, `method reference` sẽ giải quyết ngắn gọn hơn rất nhiều:

```java
list.forEach(Ex01::printOddNumber);
```

Và tương tự với cách xử lý khi ta dùng hàm `convertToInteger`

```java
List<Integer> convertToInteger = convertToInteger(Integer::new, list);
convertToInteger.forEach(ystem.out::println);
```

### 5.2 Example 2

Xét `class Student` sau:

```java
public class Student {
	private final String name;
	private final int age;

	public Student(String name, int age) {
		this.name = name;
		this.age = age;

	}

	public String getName() {
		return name;
	}

	public int getAge() {
		return age;
	}

	public void printName() {
		System.out.println(this.name);
	}
}
```

Khởi tạo 1 list trước đã nhé:

```java
List<Student> people = new ArrayList<>();
people.add(new Student("Albert", 18));
people.add(new Student("Ben", 15));
people.add(new Student("Charlote", 16));
people.add(new Student("Dean", 16));
people.add(new Student("Elaine", 17));
```

Sau đó đơn giản là in ra list của chúng ta để xem nội dung bên trong, nhưng sử dụng hàm `printName` của `class Student` nhé:

```java
people.forEach(Student::printName);
```

Và sử dụng `System.out.println()` nào:

```java
people.forEach(System.out::println);
```