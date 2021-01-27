# Return an object of functions

```js
function compare(a) {
  return {
    greaterThan(b) {
      return a > b 
    }
    lesserThan(b) {
      return a < b
    }
    equalTo(b) {
      return a == b
    }
  }
}

compare(4).greaterThan(3) // true
compare(4).lesserThan(3) // false
compare(4).equalTo(3) // false

```
