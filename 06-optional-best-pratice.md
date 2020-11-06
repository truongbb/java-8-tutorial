# JAVA 8 - OPTIONAL BEST PRACTICE


## 1 Đừng bao giờ gán `null` vào một `Optional` instance trả về

Chúng ta đã biết rằng có một số cách tạo `Optional`, nhưng với trường hợp muốn return lại một `Optional` không có giá trị bên trong, ta nên sử dụng `Optional.empty()` chứ không nên dùng `Optional.of(null)` hoặc `Optional.ofNullable(null)`.

## 2 Phải đảm bảo rằng bạn kiểm tra `Optional` instance trước khi sử dụng nó

Vâng, tất nhiên rồi, để tránh exception, chúng ta nên check `Optional` mà chúng ta muốn sử dụng liệu rằng có `present` hay không, rồi mới sử dụng nó.

```java
// AVOID
Optional<Cart> cart = ... ; // this is prone to be empty

// if "cart"is empty then this code will throw a java.util.NoSuchElementException
Cart myCart = cart.get();

// ----------------------------------------------

// PREFER
Optional<Cart> cart = ... ; // this is prone to be empty
if (cart.isPresent()) {
    Cart myCart = cart.get();
    // ... do something with "myCart"
} else {
    // do something that doesn't call cart.get()
}
```

## 3 Sử dụng `orElse`, `orElseGet` hoặc `orElseThrow` để trả về giá trị mặc định, ngừng `if-else` dài dòng

```java
// AVOID
public static final String USER_STATUS = "UNKNOWN";

public String findUserStatus(long id) {
    Optional<String> status = ...; // prone to return an empty Optional
    if (status.isPresent()) {
        return status.get();
    } else {
        return USER_STATUS;
    }
}

// --------------------------------------------

// PREFER
public static final String USER_STATUS = "UNKNOWN";

public String findUserStatus(long id) {
    Optional<String> status = ...; // prone to return an empty Optional
    return status.orElse(USER_STATUS);
}
```

Tương tự với `orElseGet`, nhưng `orElseGet` được sử dụng khi bạn không trả về giá trị mặc định một cách trực tiếp, mà cần thực hiện một loạt các hành động khác để lấy về giá trị mặc định đó.

```java
// AVOID
public String computeStatus() {
    // some code used to compute status
}

public String findUserStatus(long id) {
    Optional<String> status = ...; // prone to return an empty Optional
    if (status.isPresent()) {
        return status.get();
    } else {
        return computeStatus();
    }
}

// -----------------------------------------

// AVOID
public String computeStatus() {
    // some code used to compute status
}

public String findUserStatus(long id) {
    Optional<String> status = ...; // prone to return an empty Optional
    // computeStatus() is called even if "status" is not empty
    return status.orElse(computeStatus());
}

// -----------------------------------------

// PREFER
public String computeStatus() {
    // some code used to compute status
}

public String findUserStatus(long id) {
    Optional<String> status = ...; // prone to return an empty Optional
    // computeStatus() is called only if "status" is empty
    return status.orElseGet(this::computeStatus);
}
```

Và điều này cũng tương tự với method `orElseThrow()` (java 10) và `orElseThrow(Supplier<? extends X> exceptionSupplier)`



## 4. Nếu cần trả về null trong một "Optional chain" thì hãy sử dụng `orElse(null)`


Mục này đã được demo tại bài trước, nếu bạn còn nhớ:

```java
public static Optional<String> findTheFirstMatchUsingOptional(String key, List<Student> students) {
	return Optional.ofNullable(students).map(studentList ->
			students.stream().map(Student::getFullName).filter(fullName -> fullName.contains(key)).findFirst().orElse(null)
	);
}
```

## 5. Nếu chỉ sử dụng `Optional` instance khi nó `present` mà không muốn xử lý khi nó "không `present`" thì hãy sử dụng `Optional.ifPresent()`

```java
// AVOID
Optional<String> status = ... ;
if (status.isPresent()) {
    System.out.println("Status: " + status.get());
}

// ---------------------------------------------------

// PREFER
Optional<String> status ... ;
status.ifPresent(System.out::println);
```



## 6. Sử dụng `Optional.ifPresentOrElse()` khi muốn xử lý như một khối `if-else` hoàn chỉnh

Được giới thiệu từ java 9, `Optional.ifPresentOrElse()` là một phương thức cải thiện của `Optional.ifPresent()` bên trên. Nó nhận 2 tham số đầu vào:

```java
/**
  * If a value is present, performs the given action with the value,
  * otherwise performs the given empty-based action.
  *
  * @param action the action to be performed, if a value is present
  * @param emptyAction the empty-based action to be performed, if no value is
  *        present
  * @throws NullPointerException if a value is present and the given action
  *         is {@code null}, or no value is present and the given empty-based
  *         action is {@code null}.
  * @since 9
*/
public void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction) {
    if (value != null) {
        action.accept(value);
    } else {
        emptyAction.run();
    }
}
```

Bài trước cũng có trường hợp sử dụng `Optional.ifPresentOrElse()` rồi, nếu bạn còn nhớ:
```java
findTheFirstMatchUsingOptional("z", students).ifPresentOrElse(System.out::println, () -> System.out.println("Không tìm thấy"));
```

## 7. Khi cần trả về một Optional mặc định trong một "trường hợp else" nào đó, hãy sử dụng `Optional.or()`

Tất nhiên chúng ta sẽ không làm thế này:

```java
// AVOID
public Optional<String> fetchStatus () {
    Optional<String> status = ...;
    Optional<String> defaultStatus = Optional.of("PENDING");
    if (status.isPresent()) {
        return status;
    } else {
        return defaultStatus;
    }
}
```

Cũng không nên sử dụng như này nhé:

```java
// AVOID
public Optional<String> fetchStatus() {
    Optional<String> status = ... ;
    return status.orElseGet(() -> Optional.<String>of("PENDING"));
}
```

Tại vì bản chất của hàm `orElseGet` là chấp nhận một `Supplier` instance để thực hiện xử lý:

```java
public T orElseGet(Supplier<? extends T> supplier) {
        return value != null ? value : supplier.get();
    }
```

Còn `or()` nhận vào hẳn một `Optional`, vậy nên khi muốn trả về `Optional` mặc định thì nên sử dụng `or` chứ không phải là `orElse` hay `orElseGet`:
```java
// PREFER
public Optional<String> fetchStatus () {
    Optional<String> status = ...;
    Optional<String> defaultStatus = Optional.of("PENDING");

    return status.or(() -> defaultStatus);
    // or, without defining "defaultStatus"
    return status.or(() -> Optional.of("PENDING"));
}
```

## 8 Nên sử dụng Optional.orElse/orElseXXX thay thực hiện combo check `isPresent` và `get`

Cái này quá rõ ràng rồi, sử dụng `orElse` hoặc `orElseXXX` làm cho code liền mạch, tạo ra một `Optional chain` và tránh quên như quên việc check `present` của một `Optional` instance

## 9 Đừng tạo `Optional` instance bừa bãi khi chỉ sử dụng để lấy ra giá trị mặc định hay không

Đừng dùng dao mổ trâu để giết gà:

```java
// AVOID
public String fetchStatus () {
    String status = ...;
    return Optional.ofNullable(status).orElse("PENDING");
}

// -----------------------------------------

// PREFER
public String fetchStatus () {
    String status = ...;
    return status == null ? "PENDING" : status;
}
```

## 10. Không nên sử dụng `constructor` và `getter` có tham số là `Optional`

## 11. Không nên sử dụng các thuộc tính của class là `Optional`




## Preference

[1] [26 Reasons Why Using Optional Correctly Is Not Optional](https://dzone.com/articles/using-optional-correctly-is-not-optional)