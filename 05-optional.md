# JAVA 8 - OPTIONAL 

Optional được manh nha ý tưởng từ [một câu trả lời trên Stackoverflow](https://stackoverflow.com/questions/26327957/should-java-8-getters-return-optional-type/26328555#26328555) của chính Brian Goetz, người làm việc trong mảng ngôn ngữ Java của Oracle. Được tung ra từ phiên bản 1.8 của java, tới phiên bản 9, 10, 11 được cải thiện, tới nay Optional là một công cụ cực kì mạnh mẽ của java để tránh được exception không mong muốn và khiến code của các Java dev trở nên sạch đẹp hơn.

[`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) trong java 8 là một container, nó bao bọc các kiểu dữ liệu khác. Mục đích của `Optional` là tránh `NullPointerException` và tối ưu hóa code khi sử dụng các hàm tiện tích của nó. Khi kết hợp với các Java 8 API như `stream`, `map`, `filter`, ... sẽ rất mạnh mẽ và giúp code sạch sẽ hơn.

Ngày trước, khi chưa có hoặc chưa sử dụng `Optional`, code của chúng ta sẽ như sau:

```java
// Life before Optional
private void getIsoCode(User user) {
    if (user != null) {
        Address address = user.getAddress();
        if (address != null) {
            Country country = address.getCountry();
            if (country != null) {
                String isocode = country.getIsocode();
                if (isocode != null) {
                    isocode = isocode.toUpperCase();
                }
            }
        }
    }
}
```

Dựa vào [document của Oracle](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html), chúng ta sẽ lần lượt sử dụng các phương thức của `Optional` để áp dụng vào thực tế.

## 1. Tạo một `Optional` object

Có rất nhiều cách để tạo ra 1 instance của `Optional`, nhưng trong bài này chúng ta chưa vội bàn đến những cách tạo gián tiếp (sử dụng một vài method của Stream), nên chúng ta sẽ có 3 cách để tạo `Optional` object: sử dụng 3 static method của class `Optional`.

- Sử dụng `Optional.of(T)`:

Ở bài này chúng ta sẽ hoạt động với một class `Student`:
```java
public class Student {
    private int id;
    private String fullName;
    private java.util.Date birthday;
    // getters, setters, constructors
}
```

Muốn tạo một `Optional` instance, rất đơn giản: 

```java
Student student = new Student();
Optional<Student> studentOptional = Optional.of(student); // done
System.out.println(student);
System.out.println(studentOptional);

// console output
Student{id=0, fullName='null', birthday=null}
Optional[Student{id=0, fullName='null', birthday=null}]
```

Nhưng khi truyền `null` để tạo thì chúng ta sẽ nhận lại `NullPointerException` khi sử dụng chúng:

```java
Optional<Student> optional = Optional.of(null); // will throw NullPointerException
```

Để tránh được vấn đề đối số truyền vào của chúng ta có thể `null` thì chúng ta nên sử dụng cách thứ 2 khi muốn khởi tạo một `Optional` instance.

- Sử dụng `Optional.ofNullable(T)`:

```java
Optional<Student> optional25 = Optional.ofNullable(null); // --> it's ok

Student s = null;
Optional<Student> optional25 = Optional.ofNullable(s); // --> it's ok too
```

- Sử dụng `Optional.empty()`: Khi bạn chủ động muốn khởi tạo 1 `Optinoal` instace với giá trị bên trong nó là null mà không muốn truyền `null` hoặc object rỗng, java hỗ trợ sử dụng phương thức `Optional.empty()`:

```java
Optional emptyOptional = Optional.empty();
```

## 2. Kiểm tra giá trị với `isPresent()` và `isEmpty()`

Sự vi diệu và tránh `NullPointerException` như đã đề cập bên trên, đó chính là việc check giá trị mà một `Optional` instance bao bọc liệu có khả dụng không. Ví dụ đơn giản:

```java
Student student = new Student();

Optional<Student> studentOptional1 = Optional.of(student);
System.out.println(studentOptional1.isEmpty()); // false
System.out.println(studentOptional1.isPresent()); // true

Optional<Student> studentOptional2 = Optional.ofNullable(null);
System.out.println(studentOptional2.isEmpty()); // true
System.out.println(studentOptional2.isPresent()); // false
```

Chú ý rằng phương thức `isEmpty()` chỉ có từ java 11 trở lên, còn `isPresent()` có thể được sử dụng ngay ở java 8.

Cách sử dụng thì rất là đơn giản, giống như việc chúng ta check `null` vậy, mỗi khi sử dụng tới instance đó ta phải sử dụng 1 trong 2 hàm này để check.

Ngày trước thì:
```java
Student student = getStudentInfo();

if(student != null ){
    // do some action with studentOptional1
}
```

Bây giờ thì:
```java
Optional<Student> studentOptional1 = getStudentInfoOptional();

if(studentOptional1.isPresent()){
    // do some action with studentOptional1
}
```

Tới đây sẽ có người hỏi rằng: "Ô thế check như kia thì khác quái nào là phải check null rồi không, việc gì phải viết thế, tôi vẫn check null như trước tôi vẫn làm!!!". Đồng ý! Nếu như học tới đây thì chưa thấy khác biệt gì cả, và nếu như các bạn quên check `Optional` intance như việc các bạn quên check `null` thì các bạn vẫn oẳng như thường mà thôi. Nhưng trò hay vẫn còn phía sau mà. Let's go!


## 3. Thực hiện các hành động với `ifPresent()`

Câu chuyện còn dang dở lúc nãy đang nói về việc quên check null hoặc `isEmpty()` (cũng như `isPresent()`) thì sẽ bắn ra exception khi chúng ta sử dụng:

```java
Student student = getStudentInfo();

System.out.println(student); // NullPointerException will be thrown when student object is NULL
```

hoặc:

```java
Optional<Student> studentOptional1 = getStudentInfoOptional();

System.out.println(studentOptional1.get().getFullName()); // NullPointerException will be thrown when student instance inside studentOptional1 object is NULL
```

Quên mất, khi muốn thao tác với object bên trong `Optional` bao bọc thì ta buộc phải lấy nó ra để thao tác bằng phương thức `get()` nhé!

Đó, giả sử ta quên việc kiểm tra thì chúng ta vẫn nhận được Exception như thường lệ. Nhưng các ông thánh viết ra java đã lường trước được việc này và ném thằng vào mặt những ông dev nào code mà hay quên check một method `ifPresent(Consumer<? super T> action)`. 


Khi sử dụng phương thức này, ta cần truyền vào trong một `Consumer` instance để viết các nội dung mà ta muốn thực thi. Hàm này làm 2 việc, check `Optional` instance đó có non-null hay không, nếu có thì sẽ thực hiện nội dung mà ta truyền vào. 

Vậy nên hãy <em><b>quên đi</b></em> những công đoạn `if-else` check `null` nhàm chán mà ta vẫn làm và hay quên, sử dụng `ifPresent` và code của bạn sẽ sạch hơn, nói không với `NullPointerException`!

Thế thì đoạn code bên trên:

```java
Optional<Student> studentOptional1 = getStudentInfoOptional();

System.out.println(studentOptional1.get().getFullName()); // NullPointerException will be thrown when student instance inside studentOptional1 object is NULL
```

sẽ được viết như này:

```java
Optional<Student> studentOptional1 = getStudentInfoOptional();

studentOptional1.ifPresent(student -> System.out.println(student.getFullName)); // ok
```

Và tất nhiên, đây là một phương thức không trả về gì cả (kiểu `void`). Vậy nên, cách sử dụng đã quá rõ ràng, đó là khi bạn chỉ muốn thực hiện các thao tác với một `Optional` đã present và trong trường hợp nó không `present` thì không làm gì cả.

## 4. Giá trị mặc định, xử lý "các trường hợp else"

Trong trường hợp chúng ta muốn trả về giá trị mặc định hoặc thực hiện một loạt các hành động khi rơi vào trường hợp mặc định, `Optinal` cung cáp cho chúng ta 2 phương thức `orElse()` và `orElseGet()`.

### 4.1  Trả về giá trị mặc định với `orElse(T value)`:

Trước hết, ta có hàm `getDefaultName` trnog class `Demo` như sau:
```java
private String getDefaultName() {
        return "default name";
    }
```

Ta có đoạn code sau: 
```java
Student student = ...;
String studentFullName = Optional.ofNullable(student.getFullName()).orElse(getDefaultName());
```

Phương thức `orElse` nhận vào một giá trị nào đó hoặc đối tượng.

Nếu như trong dấu 3 chấm ta khởi tạo:

```java
Student student = new Student(1, "James", new Date());
```
thì giá trị của `studentFullName` sẽ là `"James"`.

Nhưng khi ta để `student` là một object rỗng:
```java
Student student = new Student();
```
thì giá trị của `studentFullName` sẽ là `"default name"`. Đây chính là giá trị mặc định sau khi chạy vào `orElse(T)`


### 4.2  Trả về giá trị mặc định với `orElseGet(Supplier<? extends T> supplier)`:


Khác với `orElse` về cú pháp, `orElseGet` nhận vào một `Supplier` instance, chứng tỏ rằng chúng ta có thể thực hiện một loạt các hành động với `orElseGet` rồi trả về một giá trị mặc định.

```java
Student student = new Student(1, "James", new Date());
String studentFullName = Optional.ofNullable(student.getFullName()).orElseGet(Demo::getDefaultName); // James

student = new Student();
String studentFullName1 = Optional.ofNullable(student.getFullName()).orElseGet(Demo::getDefaultName); // default name
```

### 4.3 Sự khác biệt giữa `orElse` và `orElseGet`

Tất nhiên, sinh ra 2 thứ khác nhau phải có mục đích và cách sử dụng khác nhau. Về cách sử dụng thì đã quá rõ ràng, một hàm truyền vào giá trị, một hàm truyền vào `Supplier` instance để thực hiện một loạt các hành động mong muốn.

Nhưng giả sử, trong trường hợp cả 2 hàm cùng gọi tới 1 hàm khác để lấy về giá trị mặc định thì liệu có khác biệt?

Chúng ta chỉ cần thêm 1 dòng vào hàm `getDefaultName` thì sẽ rõ:
```java
private String getDefaultName() {
    System.out.println("Call getDefaultName");
    return "default name";
}
```

Và kết quả sẽ như sau:

```java
Student student =new Student(1, "James", new Date());

System.out.println(Optional.ofNullable(student.getFullName()).orElse(getDefaultName()));
System.out.println("---------------------------");
System.out.println(Optional.ofNullable(student.getFullName()).orElseGet(Main::getDefaultName));

// console output
Call getDefaultName
James
---------------------------
James
```

Vậy là đã rõ! `orElse` luôn luôn gọi tới hàm `getDefaultName` còn `orElseGet` chỉ gọi tới khi `Optional` instance không `present` mà thôi!


## 5. Bắn ra exception với `orElseThrow()`

Tương tự như `orElse()` hoặc `orElseGet()`, phương thức `orElseThrow(Supplier<? extends X> exceptionSupplier)` sẽ thực hiện ném ra một exception mà ta mong muốn. Trong trường hợp này là người code chủ động ném ra exception.

Ví dụ đơn giản với việc viết hàm tính tổng hai số sử dụng `Optional`:

```java
private Integer totalOf(Integer a, Integer b) {
    return Optional.ofNullable(a).map(firstArg ->
            Optional.ofNullable(b).map(secondArg -> firstArg + secondArg).orElseThrow(IllegalArgumentException::new)
    ).orElseThrow(IllegalArgumentException::new);
}
```

Hàm này thực hiện nhận vào 2 object `Integer`, thực hiện tính tổng của chúng nếu hợp lệ, ngược lại, sẽ bắn ra `IllegalArgumentException` nếu tham số truyền vào không hợp lệ (cụ thể là bị null :v).

Nếu sử dụng java 10, java cung cấp cho chúng ta phương thức `orElseThrow()` mặc định mà không cần truyền một `Supplier` instance. Và mặc định phương thức này ném ra `NoSuchElelementException`.

```java
private Integer totalOf(Integer a, Integer b) {
    return Optional.ofNullable(a).map(firstArg ->
            Optional.ofNullable(b).map(secondArg -> firstArg + secondArg).orElseThrow()
    ).orElseThrow();
}
```


## 6. `map()` và `filter()` trong `Optional`

Rất giống với khi sử dụng `map()` và `filter()` của `Stream`. Nếu không nhớ có thể tham khảo tại [các bài trước](./04-stream.md), hoặc [document chính thức của Oracle](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#map-java.util.function.Function-).


Và cuối cùng, sau khi đã biết được sơ bộ các phương thức cũng như cách dùng cơ bản của `Optional` thì đoạn code đầu bài:

```java
// Life before Optional
private void getIsoCode(User user) {
    if (user != null) {
        Address address = user.getAddress();
        if (address != null) {
            Country country = address.getCountry();
            if (country != null) {
                String isocode = country.getIsocode();
                if (isocode != null) {
                    isocode = isocode.toUpperCase();
                }
            }
        }
    }
}
```

có thể được viết lại sử dụng `Optional` như sau:

```java
// Life after Optional
private void getIsoCode(User user) {
    String isoCode = Optional.ofNullable(user).map(u ->
        Optional.ofNullable(u.getAddress()).map(addr ->
            Optional.ofNullable(address.getCountry()).map(country ->
                Optional.ofNullable(country.getIsocode()).map(isoCode -> Optional.ofNullable(isoCode).orElseThrow()).orElseThrow()
            ).orElseThrow()
        ).orElseThrow()
    ).orElseThrow();
}
```

Hoặc như đoạn code register account như sau:

```java
/**
  * POST  /register : register the user.
  *
  * @param managedUserVM the managed user View Model
  * @return the ResponseEntity with status 201 (Created) if the user is registered or 400 (Bad Request) if the login or email is already in use
*/
@Timed
@PostMapping(path = "/register", produces = {MediaType.APPLICATION_JSON_VALUE, MediaType.TEXT_PLAIN_VALUE})
public ResponseEntity registerAccount(@Valid @RequestBody ManagedUserVM managedUserVM) {

    HttpHeaders textPlainHeaders = new HttpHeaders();
    textPlainHeaders.setContentType(MediaType.TEXT_PLAIN);

    return userRepository.findOneByLogin(managedUserVM.getLogin().toLowerCase())
            .map(user -> new ResponseEntity<>("uername already in use", textPlainHeaders, HttpStatus.BAD_REQUEST))
            .orElseGet(() -> userRepository.findOneByEmail(managedUserVM.getEmail())
                    .map(user -> new ResponseEntity<>("email address already in use", textPlainHeaders, HttpStatus.BAD_REQUEST))
                    .orElseGet(() -> {
                        User user = userService
                                .createUser(managedUserVM.getLogin(), managedUserVM.getPassword(),
                                        managedUserVM.getFirstName(), managedUserVM.getLastName(),
                                        managedUserVM.getEmail().toLowerCase(), managedUserVM.getImageUrl(),
                                        managedUserVM.getLangKey());

                        mailService.sendActivationEmail(user);
                        return new ResponseEntity<>(HttpStatus.CREATED);
                    })
            );
}
```

## 7. Ví dụ

Chúng ta sẽ thực hiện một vài ví dụ với class `Student` được đề cập ở đầu bài.
<br/>

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
        return Optional.ofNullable(students).map(studentList ->
                students.stream()
                        .map(Student::getFullName)
                        .filter(t -> t.contains(key))
                        .collect(Collectors.toList())
        );
    }
```

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
	return Optional.ofNullable(students).map(studentList ->
			students.stream().map(Student::getFullName).filter(fullName -> fullName.contains(key)).findFirst().orElse(null)
	);
}
```

Bước xây dựng hàm đã xong, bây giờ chúng ta hãy sử dụng các hàm vừa viết để xem sự khác biệt nhé.
<br/>

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
Đoạn `map(Collection::stream).orElseGet(Stream::empty)` là đoạn convert từ `Optional<List<String>>` sang `List<String>`. Vì ta cần thao tác khác với `List` này, không thể để dưới dạng `Optional` được, `Optional` chỉ là dạng sơ  khởi và tránh gặp `null` mà thôi, khi cho ra kết quả cuối cùng ta vẫn phải đưa về dạng chính.

<br/>

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
findTheFirstMatchUsingOptional("z", students).ifPresentOrElse(System.out::println, () -> System.out.println("Không tìm thấy"));
```
