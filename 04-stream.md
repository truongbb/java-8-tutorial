# JAVA 8 - STREAM

## 1. WHAT
- `Stream` là 1 API của java 8 nằm trong package `java.util.stream`, gồm những API xử lý tuần tự các element cho `collection`
- Có 2 loại stream là tuần tự (`stream`) và song song (`parallelStream`)

## 2. WHY
- Mục đích của `stream` là sử dụng để thao tác với các `collection` của java và thực hiện các tác vụ mà các câu truy vấn cơ sở dữ liệu đảm nhiệm. Ví dụ tính tổng, tìm số `min`, `max`, lọc danh sách theo tên, tìm kiếm, gom nhóm (`group by`) ... với câu lệnh ngắn nhưng mang lại hiệu quả cao.
- Giảm thiểu code, tăng tính sử dụng, dễ đọc, dễ hiểu, dễ dùng.
- Sử dụng cơ chế `pipeline`, đa luồng của CPU nên tốc độ xử lý nhanh.

## 3. WHEN	

`Stream` được sử dụng khi thao tác với `Collection` như `list`, `map`, `set`, ...

## 4. HOW

### 4.0 `filter` AND `map`

Trước khi đi vào cách sử dụng stream, chúng ta hãy tìm hiểu một chút về `filter` và `map` nhé.


Chúng ta có class `Person`:

```java
public class Person {

	private String name;
	private int age;

	// constructors, getters, setters, toString()
}
```

Và một List instance của class Person:

```java
List<Person> people = Arrays.asList(
	new Person("Nguyễn Văn A", 20), 
	new Person("Bùi Bá Trường", 21), 
	new Person("Đỗ Quốc Dũng", 20), 
	new Person("Lê Nhân Đông", 19),
	new Person("Nguyễn Thị Loan", 17)
	);
```

#### 4.0.1 `filter`

Truyền vào 1 instance của functional interface [`Predicate`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html) làm điều kiện để lọc và trả về 1 giá trị `boolean` --> sử dụng để lọc 1 collection với 1 điều kiện nào đó.

Interface `Predicate` chỉ chứa một hàm duy nhất đó chính là `test(T t)`, chúng ta cần cài đè hàm này và viết nội dung thực thi cho hàm này để nó thực hiện lọc.

Tiện thể ôn lại bài [`lambda expression`](./02-lambda-expression.md) luôn nhé! Nhiệm vụ đầu tiên là lọc những người trên 20 tuổi trong list `people`.

##### 4.0.1.1 Lọc những người trên 20 tuổi trong list `people`

- Java trước 8 sẽ làm như thế này:

```java
List<Person> youngPeople = new ArrayList<>();
for (int i = 0; i < people.size(); i++) {
	if (people.get(i).getAge() >= 20)
		youngPeople.add(people.get(i));
}
for (Person person : youngPeople) {
	System.out.println(person);
}
```
- Còn java 8 sẽ làm thế này, đây là cách sử dụng `anonymous class` nhé:

```java
people.stream().filter(new Predicate() {
	@Override
	public boolean test(Person person) {
		return person.getAge() >= 20;
	}
}}).forEach(System.out::println);
```
Sử dụng lambda sẽ ngắn gọn hơn rất nhiều:

```java
people.stream().filter(person -> person.getAge() >= 20).forEach(System.out::println);
```

##### 4.0.1.2 Lọc những người có chữ `a` hoặc `A` trong tên

- Trước java 8:

```java
List<String> personNames = new ArrayList<>();
for (Person person : people) {
	if (person.getName().toLowerCase().contains("a")) {
		personNames.add(person.getName());
	}
}
for (String string : personNames) {
	System.out.println(string);
}
```

- Từ java 8 trở đi:

```java
people.stream()
	.map(Person::getName) // map này á, ở ngay bên dưới nhé!
	.filter(person -> person.toLowerCase().contains("a"))
	.forEach(System.out::println);
```
#### 4.0.2 `map`
Truyền vào 1 instance của functional interface [`Function<T,R>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) và trả về một instance khác (có thể là chính instance cũ nhưng đã được biến đổi các thuộc tính bên trong) --> sử dụng để ánh xạ `collection` với 1 điều kiện nào đó thành một `collection` khác, ví dụ muốn lấy list tên từ 1 list người.

Interface `Function<T,R>` chỉ chứa một hàm duy nhất đó là hàm `R apply(T t)`. Chúng ta có thể thấy rằng nó nhận vào một tham số kiểu `T` và trả ra một tham số kiểu `R` khác với `T`.

Vì vậy đoạn `people.stream().map(Person::getName)` sẽ được diễn giải như sau:

- Sử dụng `anonymous class`:

```java
people.stream()
	.map(new Function<Person, String> {
		@Override
		public String apply(Person person) {
			return person.getName();
		}
	})
	.filter(person -> person.toLowerCase().contains("a"))
	.forEach(System.out::println);
```

- Sử dụng `lambda expression`:

```java
people.stream()
	.map(person -> person.getName())
	.filter(person -> person.toLowerCase().contains("a"))
	.forEach(System.out::println);
```

- Sử dụng `method reference`:

```java
people.stream()
	.map(Person::getName)
	.filter(person -> person.toLowerCase().contains("a"))
	.forEach(System.out::println);
```

### 4.1 Create a stream

- Tạo stream từ một collection, chúng ta chỉ cần sử dụng hàm `stream()` là được:

```java
Stream<Integer> intStream = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 0).stream();

// xài thử stream này nhé
intStream.filter(t -> t > 5).forEach(System.out::println);

// ouput sẽ là: 
6
7
8
9
```

- Sử dụng hàm `of()` của class `Stream`:

```java
Stream<Integer> intStream2 = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 0);
intStream2.filter(t -> t > 5).forEach(System.out::println);
```

Java hỗ trợ Stream theo kiểu dữ liệu, vì vậy chúng ta có thể sử dụng chính xác kiểu dữ liệu muốn tạo:

```java
IntStream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 0).filter(t -> t < 5).forEach(System.out::println);
// tương tự với LongStream, DoubleStream, ....
```

- Sử dụng `mapToInt`, `mapToLong`, `mapToDouble` --> tạo ra một `IntStream`, `LongStream`, `DoubleStream`:

```java
people.stream()
	.mapToInt(Person::getAge)
	.filter(person -> person <= 20)
	.forEachOrdered(System.out::println);
```

- Ngoài `map()` còn có `flatMap()` làm nhiệm vụ map tất cả các `stream` của các kiểu dữ liệu khác nhau lại thành 1 `stream`, cụ thể:

```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
List<Integer> list2 = Arrays.asList(2, 4, 6);
List<Integer> list3 = Arrays.asList(3, 5, 7);

List<List<Integer>> list = Arrays.asList(list1, list2, list3);
System.out.println(list);

list.stream().flatMap(t -> t.stream()).forEach(System.out::print);
```

### 4.2 Inside `stream`

- Bản chất `stream` thực hiện lệnh như một `pipeline`.

- Ngoài ra `stream` là 1 loại `lazy execution`, tức là khi chưa có toán tử cuối (toán tử thường trong `lambda`) thì `stream` không thực hiện lệnh nào cả. Như đoạn code dưới đây, dòng lệnh thực hiện in ra màn hình sẽ không được thực thi.

```java
Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 0).stream().filter(t -> { // --> không in ra gì cả vì mới filter thôi,
	System.out.println(t); // chưa có một công việc cuối cùng như convert sang list, forEach để làm gì đó
	return true;
});
```
Còn đoạn code này sẽ thực hiện in ra, thậm chí sẽ in ra 2 lần:

```java
Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 0).stream().filter(t -> {
	System.out.println(t);
	return true;
}).collect(Collectors.toList()).forEach(System.out::println);
```

### 4.3 Reuse `stream`

`stream` không thể sử dụng lại, nếu cố tình sử dụng một `stream` đã dùng rồi thì sẽ gặp `exception`, ví dụ vây giờ cố tình dùng `intStream` ở phía bên trên như thế này thì sẽ bị lỗi:

```java
intStream.filter(t -> t % 2 == 0).forEach(System.out::println);
```
muốn tái sử dụng `stream`, ta phải dùng [`Supplier`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html)


### 4.4 PARALLEL STREAM

`Stream` có thể được thực hiện một cách song song để tăng hiệu năng hệ thống khi ta thực hiện iterator qua một `collection` lớn.

Stream song song sử dụng `ForkJoinPool.commonPool()` để làm việc này, `thread pool` có thể lên đến 5 `thread`, tất nhiên điều này còn phụ thuộc vào CPU, JVM setting hay thậm chí là framework config. Ví dụ sau sẽ giúp ta hiểu rõ hơn về stream song song.

```java
Arrays.asList("a1", "a2", "a3", "a4", "a5", "a6").parallelStream().filter(s -> {
	System.out.format("filter: %s [%s]\n", s, Thread.currentThread().getName());
	return true;
}).map(s -> {
	System.out.format("map: %s [%s]\n", s, Thread.currentThread().getName());
	return s.toUpperCase();
}).forEach(s -> System.out.format("forEach: %s [%s]\n", s, Thread.currentThread().getName()));
```

## 5. COLLECTIONS' ULTILITIES

- Gom nhóm kết quả sau khi `filter` hoặc `map` thành một `collection` mới:

```java
List<String> nameList = people.stream().map(Person::getName).collect(Collectors.toList());
Collections.sort(nameList, (t1, t2) -> t1.compareTo(t2));
nameList.forEach(System.out::println);
```

[`Collectors`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html) hỗ trợ rất nhiều cách gom nhóm kết quả khác nhau như `toList`, `toMap`, `toSet`, `toCollection`, ... 

- *MỘT SỐ CÁCH SẮP XẾP TRONG JAVA 8*

	- Sử dụng hàm `sorted()` của `stream`:

	```java
	people.stream().map(Person::getName).sorted().forEach(System.out::println);
	```

	Hàm `sorted()` có thể truyền vào 1 instance của class [`Comperator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html) để thực hiện so sánh theo ý muốn:

	```java
	people.stream()
		.map(Person::getName)
		.sorted((t1, t2) -> t1.compareTo(t2))
		.forEach(System.out::println);
	```

	- Sử dụng `forEarchOrdered` để sắp xếp:

	```java
	people.stream().map(Person::getName).forEachOrdered(System.out::println);
	```

	- Sử dụng method `sort` của `List`:

	```java
	people.sort((o1, o2) -> o1.getName().compareTo(o2.getName()));
	people.forEach(System.out::println);
	```

	- Sử dụng `Collections.sort()` giống với sử dụng hàm `sorted()` của `stream`:

	```java
	Collections.sort(people); //sort thông thường, chiều tăng dần

	Collections.sort(people, Collections.reverseOrder()); // tham số thứ 2 là một instance của  Comparator, ví dụ sắp xếp đảo ngược thứ tự.

	Collections.sort(people, (o1, o2) -> o1.getName().compareTo(o2.getName())); // custom instance comparator để sắp xếp theo tên người
	```

- Ngoài ra một số hàm tiện ích của stream như [`min`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#min-java.util.Comparator-), [`max`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#max-java.util.Comparator-), [`count`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#count--), [`distinct`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#distinct--), [`limit`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#limit-long-), ... cũng rất dễ sử dụng.

- Để nối 2 stream ta dùng [`concat`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#concat-java.util.stream.Stream-java.util.stream.Stream-).

- Các hàm tìm kiếm tiện dụng có thể áp dụng dễ dàng như [`findAny`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#findAny--), [`anyMatch`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#anyMatch-java.util.function.Predicate-), [`findFirst`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#findFirst--), [`allMactch`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#allMatch-java.util.function.Predicate-), ...

- Một số hàm gom nhóm tính toán khá khó sử dụng khi chưa xài qua như `groupingBy`, `summarizingInt`, `averagingInt`:

```java
// tìm nhóm người gom theo tuổi: vd 20 tuổi có người A, B; 19 tuổi có C, 21 tuổi có D, E --> sử dụng groupingBy như GROUP BY trong sql
Map<Integer, List<Person>> groupingPeople = people.stream().collect(Collectors.groupingBy(Person::getAge));

// tính độ tuổi trung bình của tất cả moi người có trong list
Double averageAge = people.stream().collect(Collectors.averagingInt(Person::getAge));

// tính tổng tuổi của tất cả mọi người trong list, tìm tuổi min, tuổi max, ...
IntSummaryStatistics totalAge = people.stream().collect(Collectors.summarizingInt(Person::getAge));
System.out.println(totalAge.getAverage());// trung bình
System.out.println(totalAge.getMax());// tuổi max
System.out.println(totalAge.getMin());// tuổi min
System.out.println(totalAge.getSum()); // tổng tuổi
System.out.println(totalAge.getCount()); // số lượng số hạng trong list
```

xem thêm tại http://winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples/