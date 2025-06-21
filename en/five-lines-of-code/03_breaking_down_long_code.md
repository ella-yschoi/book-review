# Five Lines of Code - 3. Breaking Down Long Code

> Topic: Identifying and decomposing methods with five-line limit, matching abstraction levels, and separating if statements.

<br/>

## 🔖 3.1 Why Five Lines? - Intent

### Book Content

Four methods with 5 lines of code each are much faster and easier to understand than one method with 20 lines. This is because the intent of the code can be conveyed through the name of each method. Basically, naming a method is equivalent to putting a comment at least every 5 lines. Also, giving appropriate names to small methods helps in naming large functions.

### My Thoughts

I remember during the Woowacourse pre-course mission, there was a requirement limiting method length. At that time, I just thought that if a method was long, it must not be very readable, but reading this sentence, I now understand. Looking back, well-readable code seemed to show the beginning, development, turn, and conclusion of the code like reading English sentences, thanks to friendly and intuitive variable names. The more I code, the more I invest considerable thought and time in naming variables, and now I think I can believe it's worth it.

<br/>

## 🔖 3.5 Separating Functions That Do Too Much

### Book Content

If there is an if statement, that if statement should be the first item in the function. Also, it should be unique in the sense that nothing should be done after it.

```typescript
function reportPrimes(n: number) {
  for (let i = 2; i < n; i++) if (isPrime(i)) console.log(`${i} is prime`); // (1) iterate through numbers and (2) check if number is prime
}
```

```typescript
function reportPrimes(n: number) {
  for (let i = 2; i < n; i++) reportIfPrime(i); // (1) iterate through numbers
}

function reportIfPrime(n: number) {
  if (isPrime(i)) console.log(`${i} is prime`); // (2) check if number is prime
}
```

This rule means that since an if statement is one task, when separating it, the following else if is viewed as an atomic unit that cannot be separated from the if statement. This means that when an if statement forms context with else if, the smallest unit that can be done through method extraction includes the if statement and the following else if.

### My Thoughts

Looking at the example above, I reflected on how much I made multiple functions do multiple things when solving algorithm problems or implementing features... Is it really good to break it down like this..? I habitually gave multiple roles including if statements and for loops to one function, but I should consciously break them down from now on. (Of course, it's difficult to break down from the beginning)
