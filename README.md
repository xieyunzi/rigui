# Ri Gui （日晷, Sundial）

[![Build
Status](https://travis-ci.org/sunng87/rigui.png?branch=master)](https://travis-ci.org/sunng87/rigui)
[![Clojars](https://img.shields.io/clojars/v/rigui.svg)](https://clojars.org/rigui)
[![GitHub license](https://img.shields.io/github/license/sunng87/rigui.svg)](https://github.com/sunng87/rigui/blob/master/LICENSE)

Hierarchical Timing Wheels for Clojure and ClojureScript.

![rigui](https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Beijing_sundial.jpg/318px-Beijing_sundial.jpg)

## Base Usage

Start a timer:

```clojure
(require '[rigui.core :refer [start later! every! stop cancel!]])

;; starting a timer with arguments:
;; tick size: 1ms
;; bucket per wheel: 8
;; handler function: some-handler
;; thread pool for running tasks: some-executor (for JVM only)
(def timer (start 1 8 some-handler some-executor))
```

Schedule some task/value for later:

```clojure
;; schedule some value :a with delay 1000ms
;; timer's handler function will be called with :a in 1000ms

(def task (later! timer :a 1000))
```

Note that values to scheduled within a tick will be delivered to
handler function at once, and executed on current thread.

The handler function will be called with task value as arguments when
the task expires. The run a custom task, you can start the timer as:

```clojure
(def timer (start 1 8 (fn [f] (f)) some-executor))

(later! timer #(println :a) 1000)
```

The schedule function returns a `promise`-like object, which maintains
the execution result of handler function and task value. `deref`,
blocking `deref` and `realized?` are supported.

Cancel a task:

```clojure
(cancel! timer task)
```

Schedule some task/value for a fixed interval. The value will be
delivered to handler function in fixed interval.

```clojure
(every! timer :a
  1000 ;; initial delay
  500 ;; interval
  )
```

Stop the timer:

```clojure
;; calling stop on timer returns all the pending tasks
(stop timer)
```

Once a timer is stopped, it no longer accepts new task.

## Delayed Channel

This library also provides an experimental core.async channel that pop
the value after some delay.

```clojure
(require '[rigui.async :refer [delayed-chan delayed-value]])

(def c (delayed-chan))

;; the value will be available in 1000 milliseconds
(>!! c (delayed-value :a 1000))

;; blocked until 1000 milliseconds later
(<!! c)
```

## Under the hood

I will describe the implementation and its trade-off here, soon.

## License

Copyright © 2015-2016 Ning Sun

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
