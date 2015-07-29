---
layout: post
title: Closure in Clojure
tags: clojure closure
---

Closure는 function과 context라고 할 수 있다.
context는 함수가 생성되는 시점에서 함수에게 유효한 어떤 상태들이다.

Closure 정의는 이러한데...

우선 코드 샘플.

비교함수 여러개를 합친 함수를 리턴하는 함수. (함수를 ..하는 함수를 ..하는 함수 ㅠㅠ)

```
(defn make-composed-comparison [& comparisons]
  (fn [p1 p2]
    (let [results (for [comparison comparisons] (comparison p1 p2))
          first-non-zero-result
            (some (fn [result] (if (not (= 0 result)) result nil)) results)]
      (if (nil? first-non-zero-result)
        0
        first-non-zero-result))))
```

* note: some함수는 predicate이 true인 첫번째 값을 리턴

```
;; 비교함수 2개를 만들자
(defn first-name-comparison [p1, p2] (compare (:first-name p1) (:first-name p2)))
(defn last-name-comparison [p1 p2] (compare (:last-name p1) (:last-name p2)))

;; 비교함수를 합쳤음.
(def first-and-last-name-comparison
  (make-composed-comparison first-name-comparison last-name-comparison))

;;
(def p1 {:first-name "John" :middle-name "" :last-name "Adams"})
(def p2 {:first-name "John" :middle-name "Quincy" :last-name "Adams"})

;; 이제 실행해보자
(first-and-last-name-comparison p1 p2)
; => 0
```

그런데 위 예제에서는 무엇이 Closure인가?
first-and-last-name-comparison이다. 이 함수는 만들어질 때, 두개의 비교함수가 외부에서 주어졌다.
이것이 context이다. 결국 function + context 가 되었다.
