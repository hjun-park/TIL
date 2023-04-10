> ### 출처 : [모던 자바 인 액션](http://www.yes24.com/Product/Goods/77125987)

# CompletableFuture

## 1. CompletableFuture란?

- 비동기적으로 실행되는 작업 결과를 표현하는 클래스, `Future` 인터페이스를 확장한 클래스다.
- `Future`와 비슷하나, `Future`는 결과 가져오는 방법 제공하지 않는다.

### 1-1. CompletableFuture 선언 예제

```java
public class Main {
    // 0) 비동기적으로 가격을 계산하는 메서드
    public Future<Double> getPriceAsync(String product) {
        // 1) 연산 결과를 저장할 `CompletableFuture` 객체 생성
        CompletableFuture<Double> futurePrice = new CompletableFuture<>();

        // 2) 새로운 스레드에서 비동기적으로 연산 수행
        new Thread(() -> {
            try {
                double price = calculatePrice(product);

                // 3) 연산이 끝나면 `CompletableFuture` 객체에 결과를 저장
                futurePrice.complete(price);
            } catch (Exception ex) {
                futurePrice.completeExceptionally(ex);
            }
        }).start();

        // 4) 계산 결과가 나올 때까지 기다리지 않고 `Future` 반환
        return futurePrice;
    }
}
```

<br />

### 1-2. CompletableFuture 사용 예제

```java
public class Main {
    public static void main(String[] args) {

        Store store = new Store();
        long start = System.nanoTime();

        // 1) 비동기적으로 가격 계산 요청
        Future<Double> futurePrice = store.getPriceAsync("product");

        long invocationTime = ((System.nanoTime() - start) / 1_000_000);
        System.out.println("Invocation returned after " + invocationTime + " msecs");

        // 2) 가격 계산하는 동안 다른 작업 수행
        doSomethingElse();

        // 3) 다른 상점 검색 등 다른 작업 수행
        try {
            // `get 사용` 가격정보가 있다면 Future 통해서 가격정보 읽고,
            // 없다면 가격정보가 나올 때까지 기다린다.
            double price = futurePrice.get();
            System.out.printf("Price is %.2f%n", price);
        } catch (Exception ex) {
            throw new RuntimeException(ex);
        }

        long retrievalTime = ((System.nanoTime() - start) / 1_000_000);
        System.out.println("Price returned after " + retrievalTime + " msecs");
    }
}
```

- 결과는 다음과 같다.

```markdown
Invocation returned after 1 msecs
Price is 123.45
Price returned after 1001 msecs
```

1) 가격 계산 끝나기도 전에 `getPriceAsync` 메서드 반환

<br />

#### 🔵 만약 `getPriceAsync` 메서드 연산에 에러가 발생한다면 ?

- 에러가 발생해도 `getPriceAsync` 메서드는 연산하는 해당 스레드에만 영향 미침
- 결국 클라이언트는 `get` 메서드 반환까지 기다린다.
- 따라서 에러를 전파시켜 `Future` 종료하게끔 한다.

```java
    public class Main {
    public static void main(String[] args) {
        try {
            futurePrice.complete(price);
        } catch (Exception ex) {
            // 문제 발생 시 에러를 포함시켜 Future 종료
            futurePrice.completeExceptionally(ex);
        }
    }
}
```

<br />

### 1-3. `supplyAsync` :: CompletableFuture 더 간단하게

- 위 예제는 `supplyAsync`로 간편히 만들 수 있다.
- `supplier`를 인수로 받아서(return만 있는) `CompletableFuture` 반환

```java
public class Main {
    public Future<Double> getPriceAsync(String product) {
        return CompletableFuture.supplyAsync(() -> calculatePrice(product));
    }
}
```

<br />

## 2. 비블록으로 CompletableFuture 구현
- 좀 더 간결하게 `CompletableFucture` 구현하는 코드 작성

### 2-1. 상황
- 4개의 상점 리스트가 존재하며, 제품명 입력 시 상점별로 제품에 대한 가격을 `List`로 반환해야 한다.
```java
public class Main {
    public static void main(String[] args) {
        List<Shop> shops = Array.asList(new Shop("Coupang"),
                                        new Shop("Gmarket"),
                                        new Shop("11st"),
                                        new Shop("Auction"));
    }
}
```
- 구현해야 할 메서드는 `public List<String> findPrices(String product);`

### 2-2. [Version 1] 순차적으로 요청해서 갖고오기 
```java
public class Main {
    public static void main(String[] args) {
        public List<String> findPrices(String product) {
            return shops.stream()
                    .map(shop -> String.format("%s price is %.2f", shop.getName(), shop.getPrice(product)))
                    .collect(toList());
        }
    }
}
```
- 이 방식은 순차적으로 진행되기 때문에 상점이 많아지면 시간이 오래 걸린다.

<br />

### 2-3. [Version 2] 병렬 스트림 사용
```java
public class Main {
    public static void main(String[] args) {
        public List<String> findPrices(String product) {
            return shops.parallelStream()
                    .map(shop -> String.format("%s price is %.2f", shop.getName(), shop.getPrice(product)))
                    .collect(toList());
        }
    }
}
```
- 병렬적으로 처리해서 시간이 단축되었다.
- 하지만 동기 방식이기 때문에 병렬로 `findPrices`를 실행하는 동안 다른 것을 하지 못한다.


<br />

### 2-4. [Version 3] `CompletableFuture` 사용
```java
public class Main {
    public static void main(String[] args) {
        public List<String> findPrices(String product) {
            
            // 1) 첫 번째 stream 파이프라인
            List<CompletableFuture<String>> priceFutures = shops.stream()
                    .map(shop -> CompletableFuture.supplyAsync(() -> shop.getName() + " price is " + shop.getPrice(product)))
                    .collect(toList());

            // 2) 두 번째 stream 파이프라인
            return priceFutures.stream()
                    .map(CompletableFuture::join)
                    .collect(toList());
        }
    }
}
```
1) 파이프라인을 나눈 이유
   - 바로 반환하면 `List<CompletableFuture<String>>>` 반환됨
   - 하지만 우리 요구사항은 `List<String>`이다.
   - 따라서, `CompletableFuture`를 `join`해서 다 만들어질 때까지 대기 후에 `List<String>`으로 만들어준다.
2) 이 방식은 단순히 병렬 스트림을 이용하는 것보다 느리다.
   - 하지만 스레드 4개, 상점은 5개가 있는 상황이라면 `CompletableFuture`가 더 빠르다.
   - 이유는 `CompletableFuture`는 스레드 풀을 사용하기 때문이다.
   - `CompletableFuture`는 병렬 스트림과 다르게 다양한 `Executor` 지정 가능
#### TODO: 이해 안 가는 점 : stream lazy 특성 때문에 순차 계산이 일어나고 이를 회피하는 방법을 소개했음 (p510)
#### 그림이 잘 이해 안 됨
#### 내 생각, 순차 : 요청하고 기존 요청에 대한 결과가 나올 때까지 join하고, 그 때서야 그 다음으로 요청 전달?
#### 병렬 : 비동기로 진행되는데, 두 map 연산을 나눴기 때문에, 첫 번째 map은 Future 만들면 곧바로 반환하기 때문에 기다리지않고 바로바로 진행
#### , 두 번째는 join 써서 비로소 모두 다 받아지면 그 때서야 List<String> 반환 ?  
3) 병렬 스트림일지라도 `getPrice`는 순차적으로 실행된다.
   - lazy 특성은 하나의 파이프라인으로 연산 처리 시 모든 가격 정보 요청이 동시, 순차적으로 이루어지는 결과를 말함

#### 515page




