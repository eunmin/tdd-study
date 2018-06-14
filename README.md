# 테스트 주도 개발

## 저자의 글

## 들어가는 글

## 1부 화폐 예제

| 종목  | 주    | 가격 | 합계   |
| ---- | ---- | --- | ----- |
| IBM  | 1000 |  25 | 25000 |
| GE   |  400 | 100 | 40000 |
|      |      | 합계 | 65000 |


| 종목  | 주    | 가격    | 합계      |
| ---- | ---- | ------ | -------- |
| IBM  | 1000 |  25USD | 25000USD |
| GE   |  400 | 150CHF | 60000CHF |
|      |      | 합계    | 65000USD |

```
1. 재빨리 테스트를 하나 추가한다.
2. 모든 테스트를 실행하고 새로 추가한 것이 실패하는지 확인한다.
3. 코드를 조금 바꾼다.
4. 모든 테스트를 실행하고 전부 성공하는지 확인한다.
5. 리팩토링을 통해 중복을 제거한다.
```

### 다중 통화를 지원하는 Money 객체

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(진행중) $5 x 2 = $10
```

```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  five.times(2);
  assertEquals(10, five.amount);
}
```

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(진행중) $5 x 2 = $10
amount를 private으로 만들기
Dollar 부작용(side effect)?
Money 반올림?
```

```java
class Dollar {}
```

```java
class Dollar {
  Dollar(int amount) {}
}
```

```java
class Dollar {
  Dollar(int amount) {}
  void times(int multiplier) {}
}
```

```java
class Dollar {
  int amount;
  Dollar(int amount) {}
  void times(int multiplier) {}
}
```

- 테스트 실행하고 실패 확인

```java
class Dollar {
  int amount = 10;
  Dollar(int amount) {}
  void times(int multiplier) {}
}
```

- 테스트 성공 후 중복 제거

```java
class Dollar {
  int amount = 5 * 2;
  Dollar(int amount) {}
  void times(int multiplier) {}
}
```

- 테스트 실행

```java
class Dollar {
  int amount;
  Dollar(int amount) {}
  void times(int multiplier) {
    amount = 5 * 2;
  }
}
```

- 테스트 실행

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    amount = 5 * 2;
  }
}
```

- 테스트 실행

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    amount = amount * 2;
  }
}
```

- 테스트 실행

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    amount = amount * multiplier;
  }
}
```

- 테스트 실행

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
Dollar 부작용(side effect)?
Money 반올림?
```

### 타락한 객체

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(진행중) Dollar 부작용(side effect)?
Money 반올림?
```

```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  five.times(2);
  assertEquals(10, five.amount);
  five.times(3);
  assertEquals(15, five.amount);
}
```

```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  Dollar product = five.times(2);
  assertEquals(10, product.amount);
  product = five.times(3);
  assertEquals(15, product.amount);
}
```

- 테스트 후 컴파일 오류

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    amount = amount * multiplier;
    return null;
  }
}
```

- 테스트 실행

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    return new Dollar(amount * multiplier);
  }
}
```

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(완료) Dollar 부작용(side effect)?
Money 반올림?
```

### 모두를 위한 평등

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(완료) Dollar 부작용(side effect)?
Money 반올림?
equals()
```

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(완료) Dollar 부작용(side effect)?
Money 반올림?
(진행중) equals()
hashCode()
```

```java
public void testEquality() {
  assertTrue(new Dollar(5).equals(new Dollar(5)));
}
```

- 테스트 후 컴파일 실패

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    return new Dollar(amount * multiplier);
  }
  public boolean equals(Object object) {
    return true;
  }
}
```

- 테스트 성공

```java
public void testEquality() {
  assertTrue(new Dollar(5).equals(new Dollar(5)));
  assertFalse(new Dollar(5).equals(new Dollar(6)));}
```

- 테스트 실패

```java
class Dollar {
  int amount;
  Dollar(int amount) {
    this.amount = amount;
  }
  void times(int multiplier) {
    return new Dollar(amount * multiplier);
  }
  public boolean equals(Object object) {
    Dollar dollar = (Dollar) object;
    return amount == dollar.amount;
  }
}
```

- 테스트 실행 성공

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(완료) Dollar 부작용(side effect)?
Money 반올림?
(완료) equals()
hashCode()
```

- 할일

```
$5 + 10CHF = $10(환율이 2:1일 경우)
(완료) $5 x 2 = $10
amount를 private으로 만들기
(완료) Dollar 부작용(side effect)?
Money 반올림?
(완료) equals()
hashCode()
Equal null
Equal object
```
