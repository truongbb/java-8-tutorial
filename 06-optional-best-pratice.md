# JAVA 8 - OPTIONAL BEST PRACTICE


## 1 Đừng bao giờ gán `null` vào một `Optional` instance

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
if (cart.isPresent()) {
    Cart myCart = cart.get();
    // ... do something with "myCart"
} else {
    // do something that doesn't call cart.get()
}
```

## 3 Sử dụng `orElse` để trả về giá trị mặc định, ngừng `if-else` dài dòng

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
















## Preference

[1] [26 Reasons Why Using Optional Correctly Is Not Optional](https://dzone.com/articles/using-optional-correctly-is-not-optional)