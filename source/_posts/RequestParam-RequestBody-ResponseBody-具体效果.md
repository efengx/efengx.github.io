---
title: '@RequestParam @RequestBody @ResponseBody 具体效果'
date: 2019-03-14 15:30:18
tags:
---

REST API是最基本的接口调用规范，使用频率是非常之高。
注解是非常有效的技术，可以极大提高开发效率并且可以用简单的方式解决各种复杂的问题。
这里主要讲一下在REST API中经常需要使用的基本各种注解
<!-- more -->

## @RequestParam 正确使用姿势

```java
@GetMapping("/search/findById")
public String findById(
    @RequestParam("id") Long id
# 如果 id 参数有可能不存在，请使用以下注解。否则程序将抛出异常
    @RequestParam(value = "id", required = false)
) {
    return "Hello world" + id;
}

# 对应的请求url
curl http://127.0.0.1:8080/users/search/findById?id=1
```

## @PathVariable 正确使用姿势

```java
@GetMapping("/search/findById/{id}")
public String findById(@PathVariable("id") Long id) {
    return "Hello world" + id;
}

# 对应的请求url
curl http://127.0.0.1:8080/users/search/findById/1
```

## @RequestBody 正确使用姿势

```java
@PostMapping("/search/findById")
public String findById(@RequestBody User user) {
    return "Hello world" + user.getId();
}

# 对应的请求url
curl -H "Content-Type:application/json" \
     -X POST \
     -d '{"id":1}' \
     http://127.0.0.1:8080/users/search/findById
```
