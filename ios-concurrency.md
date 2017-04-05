## Synopsis

RxSwift notes

## Examples

```Swift
import RxSwift

// Variables hold a value which you can change over time
let names = Variable(["Jen"])

// How to reach to changes?
names.asObservable()
  .filter { value in

  }
  // observable only acts after you put in subscribe
  .subscribe(onNext: { value in
    print(value)
  })

names.value = ["Jen", "John"]
names.value = ["Alice", "Wendy"]

delay(seconds: 3) {
  names.value = ["Wendy"]
}

delay(seconds: 2, completion: {
  names.value = ["Admin"]
})
```
