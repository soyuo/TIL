# (A)synchronous
> [!NOTE]
> ### (비)동기
> 어떤 작업 혹은 연관된 작업을 처리하는 목적의 차이
>
> 동기는 행위와 목적 동시 성취,
>
> 비동기는 행위와 목적이 다를 수 있고, 동시 성취가 아님

## 데이터 처리 모델
 - 데이터를 받는 방식
   - 동기식 처리 모델
   - 비동기식 처리 모델

### 동기 (synchronous)
 - 데이터의 요청과 결과가 한 자리에서 동시에 일어나야 함

요청을 하면 시간이 오래 소요되어도 그 위치에서 반환이 있어야 함

다른 말로 하자면 결괏값이 올 때까지 이후 작업을 하지 않고 기다리는 것

**장점**
 - 설계가 간단하고 직관적

**단점**
 - 결과가 올 때까지 아무것도 못 하고 대기

### 비동기 (Asynchronous)
 - 데이터의 요청과 결과가 동시에 일어나지 않음

요청한 결과가 지금 오지 않는다는 것

다른 말로 하자면 결괏값을 기다리지 않고 이후 작업을 하는 것

**장점**
 - 결과를 기다리지 않고 올 때까지 다른 작업 수행

**단점**
 - 결과값을 기다리지 않기에 설계가 복잡함

## blocking & non-blocking
 - (A)synchronous 와 연결되는 개념
   - 블록(blocking)
   - 논블록(non-blocking)
 - 다만, 완전히 같은 개념은 아님

### blocking
 - 동기의 개념에서 만들어진 상태
 - 1번 요청을 했을 때, 이후 요청은 기다려야 하는 상태

### non-blocking
 - 비동기 개념에서 만들어진 상태
 - 1번 요청을 하고도, 이후 요청은 제약 없이 자유롭게 사용할 수 있는 상황

## realization in Kotlin
### synchronous
```kotlin
import java.net.URI
import java.net.http.HttpClient
import java.net.http.HttpRequest
import java.net.http.HttpResponse

fun main() {
    val client = HttpClient.newHttpClient()
    val url = "https://example.com"
    val totalStart = System.nanoTime()

    repeat(3) { i ->
        val req = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build()

        val start = System.nanoTime()
        client.send(req, HttpResponse.BodyHandlers.discarding())
        val elapsedMs = (System.nanoTime() - start) / 1_000_000
        println("#${i + 1} ${elapsedMs} ms")
    }

    val totalMs = (System.nanoTime() - totalStart) / 1_000_000
    println("${totalMs} ms")
}
```
```cmd
#1 460 ms
#2 136 ms
#3 134 ms
734 ms
```
### Asynchronous
```kotlin
import java.net.URI
import java.net.http.HttpClient
import java.net.http.HttpRequest
import java.net.http.HttpResponse
import java.util.concurrent.CompletableFuture

fun main() {
    val client = HttpClient.newHttpClient()
    val url = "https://example.com"
    val totalStart = System.nanoTime()

    val futures = (1..3).map { idx ->
        val req = HttpRequest.newBuilder()
            .uri(URI.create(url))
            .GET()
            .build()

        client.sendAsync(req, HttpResponse.BodyHandlers.discarding())
            .thenAccept {
                val sinceStartMs = (System.nanoTime() - totalStart) / 1_000_000
                println("#${idx}(From) ${sinceStartMs} ms")
            }
    }

    CompletableFuture.allOf(*futures.toTypedArray()).join()
    val totalMs = (System.nanoTime() - totalStart) / 1_000_000
    println("${totalMs} ms")
}
```
```cmd
#3(From) 404 ms
#1(From) 404 ms
#2(From) 404 ms
405 ms
```
