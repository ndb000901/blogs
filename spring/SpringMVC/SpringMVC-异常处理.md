# SpringMVC-异常处理器

## **1. 使用 `@ExceptionHandler`（局部异常处理）**
如果某个 Controller 需要处理自己的异常，可以在该类中定义 `@ExceptionHandler` 方法。

**示例：**
```java
@Controller
@RequestMapping("/api")
public class DemoController {

    @GetMapping("/test")
    public String test() {
        throw new IllegalArgumentException("参数不合法！");
    }

    @ExceptionHandler(IllegalArgumentException.class)
    @ResponseBody
    public ResponseEntity<String> handleIllegalArgumentException(IllegalArgumentException ex) {
        return ResponseEntity.badRequest().body("错误：" + ex.getMessage());
    }
}
```
**说明：**
- `@ExceptionHandler(IllegalArgumentException.class)` 表示捕获 `IllegalArgumentException` 异常。
- `ResponseEntity<String>` 作为返回值，返回 HTTP 状态码 **400 Bad Request**。

**局限性：**
- 只能处理当前 Controller 内部的异常，不能全局适用。

---

## **2. 使用 `@ControllerAdvice` 进行全局异常处理**
如果想在全局范围内处理异常，可以使用 `@ControllerAdvice` + `@ExceptionHandler`。

**示例：**
```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    @ResponseBody
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity.badRequest().body("参数错误：" + ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("服务器内部错误：" + ex.getMessage());
    }
}
```
**说明：**
- `@ControllerAdvice` 标注类作为**全局异常处理器**，对所有 `@Controller` 生效。
- `@ExceptionHandler(Exception.class)` 兜底，捕获所有未处理的异常。

---

## **3. 实现 `HandlerExceptionResolver` 进行自定义异常解析**
`HandlerExceptionResolver` 允许我们自定义异常解析逻辑。

**示例：**
```java
@Component
public class CustomExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        ModelAndView mav = new ModelAndView();
        mav.addObject("message", "发生错误：" + ex.getMessage());
        mav.setViewName("error"); // 跳转到 error.html 页面
        return mav;
    }
}
```
**说明：**
- `resolveException()` 方法允许返回 `ModelAndView`，可以跳转到自定义错误页面。
- 适用于**传统 Spring MVC 视图应用**（JSP、Thymeleaf）。

---

## **4. 使用 `@ResponseStatus` 直接指定 HTTP 状态码**
如果不需要复杂的异常处理逻辑，只需要改变 HTTP 状态码，可以使用 `@ResponseStatus`。

**示例：**
```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class CustomBadRequestException extends RuntimeException {
    public CustomBadRequestException(String message) {
        super(message);
    }
}
```
**在 Controller 里抛出异常：**
```java
@GetMapping("/error")
public void error() {
    throw new CustomBadRequestException("请求参数错误");
}
```


---

## **5. `@RestControllerAdvice`（Spring Boot 推荐方式）**
在 Spring Boot 项目中，可以使用 `@RestControllerAdvice` 代替 `@ControllerAdvice`，它等效于 `@ControllerAdvice + @ResponseBody`，返回 JSON 响应。

**示例：**
```java
@RestControllerAdvice
public class GlobalRestExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgument(IllegalArgumentException ex) {
        return ResponseEntity.badRequest().body("参数错误：" + ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("服务器错误：" + ex.getMessage());
    }
}
```
**适用于：**
- 纯 REST API 项目，所有异常都返回 JSON 格式。

---
