---
title: Java BigDecimal 및 BigInteger
author: dejavuhyo
date: 2021-11-17 09:00:00 +0900
categories: [Language, Java]
tags: [java-bigdecimal-biginteger, java-bigdecimal, java-biginteger, bigdecimal-biginteger]
---

## 1. BigDecimal
BigDecimal은 변경할 수 없는 임의 정밀도 부호 있는 십진수를 나타낸다. 두 부분으로 구성된다.

* **스케일되지 않은 값:** 임의의 정밀도 정수

* **스케일:** 소수점 오른쪽의 자릿수를 나타내는 32비트 정수

예를 들어, BigDecimal 3.14는 스케일 되지 않은 값이 314이고 스케일이 2이다.

고정밀 산술을 위해 BigDecimal 을 사용한다. 규모를 제어하고 동작을 반올림해야 하는 계산에도 사용한다. 그러한 예는 금융 거래와 관련된 계산이다.

String, character array, int, long, 그리고 BigInteger에서 BigDecimal 객체를 만들 수 있다.

```java
@Test
public void whenBigDecimalCreated_thenValueMatches() {
    BigDecimal bdFromString = new BigDecimal("0.1");
    BigDecimal bdFromCharArray = new BigDecimal(new char[] {'3','.','1','6','1','5'});
    BigDecimal bdlFromInt = new BigDecimal(42);
    BigDecimal bdFromLong = new BigDecimal(123412345678901L);
    BigInteger bigInteger = BigInteger.probablePrime(100, new Random());
    BigDecimal bdFromBigInteger = new BigDecimal(bigInteger);
        
    assertEquals("0.1",bdFromString.toString());
    assertEquals("3.1615",bdFromCharArray.toString());
    assertEquals("42",bdlFromInt.toString());
    assertEquals("123412345678901",bdFromLong.toString());
    assertEquals(bigInteger.toString(),bdFromBigInteger.toString());
}
```

Double에서 BigDecimal을 만들 수도 있다.

```java
@Test
public void whenBigDecimalCreatedFromDouble_thenValueMayNotMatch() {
    BigDecimal bdFromDouble = new BigDecimal(0.1d);
    assertNotEquals("0.1", bdFromDouble.toString());
}
```

그러나 이 경우 결과는 예상과 다르다(즉, 0.1). 이유는 아래와 같다.

* double 생성자는 정확한 표현 작업을 한다.

* 0.1은 double에 정확한 표현이 없다.

따라서 double 생성자 대신 String 문자열 생성자를 사용해야 합니다 .

또한 valueOf 정적 메서드를 사용하여 double과 long을 BigDecimal로 변환할 수 있다.

```java
@Test
public void whenBigDecimalCreatedUsingValueOf_thenValueMatches() {
    BigDecimal bdFromLong1 = BigDecimal.valueOf(123412345678901L);
    BigDecimal bdFromLong2 = BigDecimal.valueOf(123412345678901L, 2);
    BigDecimal bdFromDouble = BigDecimal.valueOf(0.1d);

    assertEquals("123412345678901", bdFromLong1.toString());
    assertEquals("1234123456789.01", bdFromLong2.toString());
    assertEquals("0.1", bdFromDouble.toString());
}
```

이 메서드는 BigDecimal로 변환하기 전에 double을 String 표현으로 변환한다. 또한 개체 인스턴스를 재사용할 수 있다.

따라서 생성자보다 valueOf 메서드를 사용해야 한다.

## 2. BigDecimal 연산
다른 Number 클래스(Integer, Long, Double 등)와 마찬가지로 BigDecimal은 산술 및 비교 연산을 위한 연산을 제공한다. 또한 스케일 조작, 반올림 및 형식 변환을 위한 작업도 제공한다.

산술(`+`, `-`, `/`, `*`) 또는 논리(`>`, `<` 등) 연산자를 오버로드하지 않는다. 대신 더하기, 빼기, 곱하기, 나누기 및 compareTo와 같은 해당 메서드를 사용한다.

BigDecimal에는 정밀도, 스케일 및 부호와 같은 다양한 속성을 추출하는 메소드가 있다.

```java
@Test
public void whenGettingAttributes_thenExpectedResult() {
    BigDecimal bd = new BigDecimal("-12345.6789");
        
    assertEquals(9, bd.precision());
    assertEquals(4, bd.scale());
    assertEquals(-1, bd.signum());
}
```

compareTo 메소드를 사용하여 두 BigDecimal의 값을 비교한다.

```java
@Test
public void whenComparingBigDecimals_thenExpectedResult() {
    BigDecimal bd1 = new BigDecimal("1.0");
    BigDecimal bd2 = new BigDecimal("1.00");
    BigDecimal bd3 = new BigDecimal("2.0");

    assertTrue(bd1.compareTo(bd3) < 0);
    assertTrue(bd3.compareTo(bd1) > 0);
    assertTrue(bd1.compareTo(bd2) == 0);
    assertTrue(bd1.compareTo(bd3) <= 0);
    assertTrue(bd1.compareTo(bd2) >= 0);
    assertTrue(bd1.compareTo(bd3) != 0);
}
```

이 방법은 비교하는 동안 스케일을 무시한다.

다른 한편으로는, equals 메소드는 두 BigDecimal 개체가 값과 크기가 같은 경우에만 같은 것으로 간주한다. 따라서 BigDecimals 1.0과 1.00은 이 방법으로 비교할 때 같지 않다.

```java
@Test
public void whenEqualsCalled_thenSizeAndScaleMatched() {
    BigDecimal bd1 = new BigDecimal("1.0");
    BigDecimal bd2 = new BigDecimal("1.00");
        
    assertFalse(bd1.equals(bd2));
}
```

해당 메소드를 호출하여 산술 연산을 수행한다.

```java
@Test
public void whenPerformingArithmetic_thenExpectedResult() {
    BigDecimal bd1 = new BigDecimal("4.0");
    BigDecimal bd2 = new BigDecimal("2.0");

    BigDecimal sum = bd1.add(bd2);
    BigDecimal difference = bd1.subtract(bd2);
    BigDecimal quotient = bd1.divide(bd2);
    BigDecimal product = bd1.multiply(bd2);

    assertTrue(sum.compareTo(new BigDecimal("6.0")) == 0);
    assertTrue(difference.compareTo(new BigDecimal("2.0")) == 0);
    assertTrue(quotient.compareTo(new BigDecimal("2.0")) == 0);
    assertTrue(product.compareTo(new BigDecimal("8.0")) == 0);
}
```

이후 의 BigDecimal은 불변이며, 이러한 작업은 기존 개체를 수정하지 않는다. 오히려 새 객체를 반환한다.

## 3. 반올림과 BigDecimal
숫자를 반올림하여 더 짧고 단순하며 의미 있는 표현을 가진 다른 숫자로 바꾼다. 예를 들어 분수 센트가 없기 때문에 $24.784917을 $24.78로 반올림한다.

사용하는 정밀도 및 반올림 모드는 계산에 따라 다르다. 예를 들어, 미국 연방 세금 환급은 HALF_UP을 사용하여 전체 달러 금액으로 반올림하도록 지정한다.

반올림 동작을 제어하는 두 가지 클래스인 RoundingMode 및 MathContext가 있다.

RoundingMode는 여덟개 반올림 모드를 제공한다.

* **CEILING:** 양의 무한대로 반올림

* **FLOOR:** 음의 무한대로 반올림

* **UP:** 0에서 반올림

* **DOWN:** 0으로 반올림

* **HALF_UP:** 두 이웃이 등거리가 아닌 경우 "가장 가까운 이웃"으로 반올림한다. 이 경우 반올림한다.

* **HALF_DOWN:** 두 이웃이 등거리가 아닌 경우 "가장 가까운 이웃"으로 반올림한다. 이 경우 내림한다.

* **HALF_EVEN:** 두 이웃이 등거리가 아닌 경우 "가장 가까운 이웃"으로 반올림한다. 이 경우에는 짝수 이웃으로 반올림한다.

* **UNNECESSARY:** 반올림이 필요 하지 않으며 정확한 결과가 가능하지 않으면 ArithmeticException이 발생한다.

HALF_EVEN 반올림 모드는 반올림 작업으로 인한 편향을 최소화한다. 자주 사용된다. 은행가의 반올림 이라고도 한다.

MathContex는 정밀도와 반올림 모드를 모두 캡슐화한다. 미리 정의된 MathContext는 거의 없다.

* **DECIMAL32:** 7자리 정밀도 및 HALF_EVEN의 반올림 모드

* **DECIMAL64:** 16자리 정밀도 및 HALF_EVEN의 반올림 모드

* **DECIMAL128:** 34자리 정밀도 및 HALF_EVEN의 반올림 모드

* **UNLIMITED:** 무제한 정밀 산술

이 클래스를 사용하여 지정된 정밀도 및 반올림 동작을 사용하여 BigDecimal 숫자를 반올림할 수 있다.

```java
@Test
public void whenRoundingDecimal_thenExpectedResult() {
    BigDecimal bd = new BigDecimal("2.5");
    // Round to 1 digit using HALF_EVEN
    BigDecimal rounded = bd
        .round(new MathContext(1, RoundingMode.HALF_EVEN));

    assertEquals("2", rounded.toString());
}
```

수량과 단가가 주어진 품목에 대해 지불해야 할 총 금액을 계산하는 메소드를 작성해 본다. 할인율과 판매세율도 적용해 보겠다. setScale 메서드를 사용하여 최종 결과를 센트로 반올림한다.

```java
public static BigDecimal calculateTotalAmount(BigDecimal quantity,
    BigDecimal unitPrice, BigDecimal discountRate, BigDecimal taxRate) { 
    BigDecimal amount = quantity.multiply(unitPrice);
    BigDecimal discount = amount.multiply(discountRate);
    BigDecimal discountedAmount = amount.subtract(discount);
    BigDecimal tax = discountedAmount.multiply(taxRate);
    BigDecimal total = discountedAmount.add(tax);

    // round to 2 decimal places using HALF_EVEN
    BigDecimal roundedTotal = total.setScale(2, RoundingMode.HALF_EVEN);
        
    return roundedTotal;
}
```

이 메서드에 대한 단위 테스트를 작성해 본다.

```java
@Test
public void givenPurchaseTxn_whenCalculatingTotalAmount_thenExpectedResult() {
    BigDecimal quantity = new BigDecimal("4.5");
    BigDecimal unitPrice = new BigDecimal("2.69");
    BigDecimal discountRate = new BigDecimal("0.10");
    BigDecimal taxRate = new BigDecimal("0.0725");

    BigDecimal amountToBePaid = BigDecimalDemo
      .calculateTotalAmount(quantity, unitPrice, discountRate, taxRate);

    assertEquals("11.68", amountToBePaid.toString());
}
```

## 4. BigInteger
BigInteger는 변경할 수 없는 임의 정밀도 정수를 나타낸다. 기본 정수 유형과 유사하지만 임의의 큰 값을 허용한다.

관련된 정수가 long 유형의 한계보다 클 때 사용된다. 예를 들어, 50의 계승은 30414093201713378043612608166064768844377641568960512000000000000이다. 이 값은 int 또는 long 데이터 유형이 처리하기에 너무 크다. BigInteger 변수에만 저장할 수 있다.

보안 및 암호화 응용 프로그램에서 널리 사용된다.

바이트 배열 또는 String에서 BigInteger를 만들 수 있다.

```java
@Test
public void whenBigIntegerCreatedFromConstructor_thenExpectedResult() {
    BigInteger biFromString = new BigInteger("1234567890987654321");
    BigInteger biFromByteArray = new BigInteger(
       new byte[] { 64, 64, 64, 64, 64, 64 });
    BigInteger biFromSignMagnitude = new BigInteger(-1,
       new byte[] { 64, 64, 64, 64, 64, 64 });

    assertEquals("1234567890987654321", biFromString.toString());
    assertEquals("70644700037184", biFromByteArray.toString());
    assertEquals("-70644700037184", biFromSignMagnitude.toString());
}
```

또한 정적 메서드 valueOf를 사용하여 long을 BigInteger로 변환할 수 있다.

```java
@Test
public void whenLongConvertedToBigInteger_thenValueMatches() {
    BigInteger bi =  BigInteger.valueOf(2305843009213693951L);
        
    assertEquals("2305843009213693951", bi.toString());
}
```

## 5. BigInteger에 대한 연산
int 및 long과 마찬가지로 BigInteger는 모든 산술 및 논리 연산을 구현한다. 그러나 연산자에 과부하를 주지 않습니다.

또한 Math 클래스의 해당 메소드(abs, min, max, pow, signum)를 구현 한다.

compareTo 메서드를 사용하여 두 BigIntegers의 값을 비교한다.

```java
@Test
public void givenBigIntegers_whentCompared_thenExpectedResult() {
    BigInteger i = new BigInteger("123456789012345678901234567890");
    BigInteger j = new BigInteger("123456789012345678901234567891");
    BigInteger k = new BigInteger("123456789012345678901234567892");

    assertTrue(i.compareTo(i) == 0);
    assertTrue(j.compareTo(i) > 0);
    assertTrue(j.compareTo(k) < 0);
}
```

해당 메소드를 호출하여 산술 연산을 수행한다.

```java
@Test
public void givenBigIntegers_whenPerformingArithmetic_thenExpectedResult() {
    BigInteger i = new BigInteger("4");
    BigInteger j = new BigInteger("2");

    BigInteger sum = i.add(j);
    BigInteger difference = i.subtract(j);
    BigInteger quotient = i.divide(j);
    BigInteger product = i.multiply(j);

    assertEquals(new BigInteger("6"), sum);
    assertEquals(new BigInteger("2"), difference);
    assertEquals(new BigInteger("2"), quotient);
    assertEquals(new BigInteger("8"), product);
}
```

BigInteger는 불변하므로 이러한 작업은 기존 개체를 수정하지 않는다. int, long과 달리 이러한 연산은 오버플로되지 않는다.

BigInteger에는 int 및 long과 유사한 비트 연산이 있다. 그러나 연산자 대신 메서드를 사용해야 한다.

```java
@Test
public void givenBigIntegers_whenPerformingBitOperations_thenExpectedResult() {
    BigInteger i = new BigInteger("17");
    BigInteger j = new BigInteger("7");

    BigInteger and = i.and(j);
    BigInteger or = i.or(j);
    BigInteger not = j.not();
    BigInteger xor = i.xor(j);
    BigInteger andNot = i.andNot(j);
    BigInteger shiftLeft = i.shiftLeft(1);
    BigInteger shiftRight = i.shiftRight(1);

    assertEquals(new BigInteger("1"), and);
    assertEquals(new BigInteger("23"), or);
    assertEquals(new BigInteger("-8"), not);
    assertEquals(new BigInteger("22"), xor);
    assertEquals(new BigInteger("16"), andNot);
    assertEquals(new BigInteger("34"), shiftLeft);
    assertEquals(new BigInteger("8"), shiftRight);
}
```

추가 비트 조작 방법이 있다.

```java
@Test
public void givenBigIntegers_whenPerformingBitManipulations_thenExpectedResult() {
    BigInteger i = new BigInteger("1018");

    int bitCount = i.bitCount();
    int bitLength = i.bitLength();
    int getLowestSetBit = i.getLowestSetBit();
    boolean testBit3 = i.testBit(3);
    BigInteger setBit12 = i.setBit(12);
    BigInteger flipBit0 = i.flipBit(0);
    BigInteger clearBit3 = i.clearBit(3);

    assertEquals(8, bitCount);
    assertEquals(10, bitLength);
    assertEquals(1, getLowestSetBit);
    assertEquals(true, testBit3);
    assertEquals(new BigInteger("5114"), setBit12);
    assertEquals(new BigInteger("1019"), flipBit0);
    assertEquals(new BigInteger("1010"), clearBit3);
}
```

BigInteger는 GCD 계산 및 모듈식 산술을 위한 방법을 제공한다.

```java
@Test
public void givenBigIntegers_whenModularCalculation_thenExpectedResult() {
    BigInteger i = new BigInteger("31");
    BigInteger j = new BigInteger("24");
    BigInteger k = new BigInteger("16");

    BigInteger gcd = j.gcd(k);
    BigInteger multiplyAndmod = j.multiply(k).mod(i);
    BigInteger modInverse = j.modInverse(i);
    BigInteger modPow = j.modPow(k, i);

    assertEquals(new BigInteger("8"), gcd);
    assertEquals(new BigInteger("12"), multiplyAndmod);
    assertEquals(new BigInteger("22"), modInverse);
    assertEquals(new BigInteger("7"), modPow);
}
```

또한 소수 생성 및 소수 테스트와 관련된 방법이 있다.

```java
@Test
public void givenBigIntegers_whenPrimeOperations_thenExpectedResult() {
    BigInteger i = BigInteger.probablePrime(100, new Random());
        
    boolean isProbablePrime = i.isProbablePrime(1000);
    assertEquals(true, isProbablePrime);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-bigdecimal-biginteger](https://www.baeldung.com/java-bigdecimal-biginteger)
