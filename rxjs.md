# Rxjs cheat sheet

## Operators

### Combination

**[combineLatest](https://rxjs.dev/api/operators/combineLatest)**
> (deprecated, see combineLatestWith)

Emit last value of all observables once they've all emit once.

```ts
import { combineLatest, Subject } from "rxjs";

const obs1 = new Subject();
const obs2 = new Subject();

combineLatest([obs1, obs2]).subscribe(console.log);

obs1.next('test1');
obs1.next('test1 1');
obs2.next('test2'); // Log : ["test1 1", "test2"]
```

**[combineLatestWith](https://rxjs.dev/api/operators/combineLatestWith)**

```ts
import { Subject } from 'rxjs';
import { combineLatestWith } from 'rxjs/operators';

const obs1 = new Subject();
const obs2 = new Subject();

obs1.pipe(combineLatestWith(obs2)).subscribe(console.log);

obs1.next('test1');
obs1.next('test1 1');
obs2.next('test2'); // Log : ["test1 1", "test2"]
```

**[zip](https://rxjs.dev/api/operators/zip)**
>(deprecated, see zipWith)

Emit values by emit index of all observables once they've all emit once

```ts
import { zip, Subject } from 'rxjs';

const obs1 = new Subject();
const obs2 = new Subject();

zip(obs1, obs2).subscribe(console.log);

obs1.next('test1');
obs1.next('test1 1');
obs2.next('test2'); // Log : ["test1", "test2"]
obs2.next('test2 2'); // Log : ["test1 1", "test2 2"]
```

**[zipWith](https://rxjs.dev/api/operators/zipWith)**

```ts
import { zipWith } from 'rxjs/operators';

const obs1 = new Subject();
const obs2 = new Subject();

obs1.pipe(
  zipWith(obs2)
).subscribe(console.log);

obs1.next('test1');
obs1.next('test1 1');
obs2.next('test2'); // Log : ["test1", "test2"]
obs2.next('test2 2'); // Log : ["test1 1", "test2 2"]
```

**[merge](https://rxjs.dev/api/operators/merge)**

Emit once one of the observable emit. No possibility to know wich one has emitted.

```ts
import { merge, Subject } from 'rxjs';

const obs1 = new Subject();
const obs2 = new Subject();

merge(obs1, obs2).subscribe(console.log);

obs1.next('test1'); // Log : test1
obs2.next('test2'); // Log : test2
```

**[race](https://rxjs.dev/api/operators/race)**

Emit values of the first observable that has emitted. No possibility to know wich one has emitted.

```ts
import { race, Subject } from 'rxjs';

const obs1 = new Subject();
const obs2 = new Subject();

race(obs1, obs2).subscribe(console.log);

obs1.next('test1'); // Log : test1
obs2.next('test2');
obs1.next('test1 1'); // Log : test1 1
```

**[forkJoin](https://rxjs.dev/api/index/function/forkJoin)**

Emit all values once all observable completed

```ts
import { forkJoin, Subject } from 'rxjs';

const obs1 = new Subject();
const obs2 = new Subject();

forkJoin([obs1, obs2]).subscribe(console.log)

obs1.next('test1');
obs1.next('test1 1');
obs2.next('test2');
obs1.complete();
obs2.complete(); // Log : ["test1 1", "test2"]
```

**[from](https://rxjs.dev/api/index/function/from)**

Emit elements of an array

```ts
import { from } from 'rxjs'; 

from([1,2,3]).subscribe(console.log)
// Log :
// 1
// 2
// 3
```

### Transformation

**[map](https://rxjs.dev/api/operators/map)**

Transform value to a new one

```ts
import { map, Subject } from 'rxjs';

const obs1 = new Subject();

obs1.pipe(map((res) => res + ' mapped')).subscribe(console.log);

obs1.next('test1'); // Log : test1 mapped
```

**[mapTo](https://rxjs.dev/api/operators/mapTo)**

Transform to a constant

```ts
import { Subject } from 'rxjs';
import { mapTo } from 'rxjs/operators';

const obs1 = new Subject();

obs1.pipe(mapTo('TOTO')).subscribe(console.log);

obs1.next('test1'); // Log : TOTO
```

**[mergeMap](https://rxjs.dev/api/operators/mergeMap)**

When first emit, call second and map the value.

```ts
import { of, from } from 'rxjs'; 
import { delay, switchMap } from 'rxjs/operators';

const getData = (param) => {
  return of(`retrieved new data with param ${param}`).pipe(
    delay(1000)
  )
}

from([1,2,3,4]).pipe(
  mergeMap(param => getData(param))
).subscribe(val => console.log(val));
// Log after one second : 
// retrieved new data with param 1
// retrieved new data with param 2
// retrieved new data with param 3
// retrieved new data with param 4
```

**[switchMap](https://rxjs.dev/api/operators/switchMap)**

When first emit, call second and map the value. The difference with mergeMap is that second cancel first call if loading.

```ts
import { of, from } from 'rxjs'; 
import { delay, switchMap } from 'rxjs/operators';

const getData = (param) => {
  return of(`retrieved new data with param ${param}`).pipe(
    delay(1000)
  )
}

from([1,2,3,4]).pipe(
  switchMap(param => getData(param))
).subscribe(val => console.log(val));
// Log after one second : retrieved new data with param 4
```