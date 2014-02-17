---
title: Finding duplicate elements in an array
date: 2012-10-14 8:31
template: article.jade
tags: Algorithm
---

>We have an array of length 100, which contains numbers ranging from 0 and 99. There is exactly one duplicate number in this array. Write a program to find out this number.

I was asked this question yesterday during a phone interview. Like almost all phone interview questions, it appears to be simple. But there are actually several interesting `O(1)` space `O(n)` time solutions.

<span class="more"></span>

[This article](http://aperiodic.net/phil/archives/Geekery/find-duplicate-elements.html) addressed this problem thoroughly. I will try to write a brief summary of possible solutions and some javascript code below. 

### By counting

```javascript
// Find duplicate element by counting
// O(n) time, O(n) space
function findDups1(input) {
    var count = {};
    for(var i=0; i < input.length;i++){
        if(count[input[i]]){
            return input[i];
        } else {
            count[input[i]] = 1;
        }
    }
    return "Not found.";
}

console.log(findDups1([0, 2, 2, 1, 4]));
// output: 2
```

Above solution is simple, but it requires O(n) extra space. We can actually do better.

Below is *a in-place solution*  that uses only O(1) space. The idea is to use the input array itself as the counter by mapping element's value to the array's index. That is, for a value v, we swap it to index v.  Similar to previous counter-based solution, when we find a collison, the duplicate element is found.

```javascript
// find duplicate element by in-place counting
// O(n) time, O(1) space
function findDups2(input) {
    for(var i=0;i < input.length;i++){
        while(input[i]!=i){
            if(input[input[i]]==input[i]){
                return input[i];
            }
            swap(input, i, input[i]);
        }
    }
}

function swap(array, i, j){
    var tmp = array[i];
    array[i] = array[j];
    array[j] = tmp;
}

console.log(findDups2([4, 2, 2, 1, 0]));
```

Although `findDups2` contains two nested loops, its running time is actually O(n). Each element is visited at most three times: once for the iteration(line 3), once for the check(line 5) and once for the swap(line 8).

###By graph algorithm

If we modifided the quesiton a little bit, we can have a graph algorithm solution. This solution is O(1) space, O(n) time. Also, it doesn't modify the input array, which can be useful if the input is immutable. However, it comes with a price: we need to restrict that elements' values range from 0 to 98 (smaller than the maximum index). The reason for this is we need to use the last element as a starting point. Details is explained below.

First let's think of the input array as a directed graph. `input[i] = j` represents an edge from node i to node j. Then finding the duplicate element is equivalent to finding the beginning of a cycle. To efficiently find the cycle, we need to start from a node that is not in the circle(To be accurate, we need a node in the tail of that circle). That is why we made that restriction - so that no node is pointing to the last node so it is guaranteed a node in the tail. 
Below I use the well-known [Floyd's cycle finding algorithm](http://en.wikipedia.org/wiki/Floyd's_cycle-finding_algorithm#Tortoise_and_hare).

```javascript
// find duplicate element using cycle-finding algorithm
// Restriction: all elements in input must be smaller 
// than input.length - 1
function findDups3(input) {
    var fast_runner = input.length - 1, 
        slow_runner = input.length - 1;
    do {
        slow_runner = input[slow_runner];
        fast_runner = input[input[fast_runner]];
    } while(fast_runner != slow_runner);
    slow_runner = input.length - 1;
    do {
        slow_runner = input[slow_runner];
        fast_runner = input[fast_runner];
    } while(fast_runner != slow_runner);
    console.log(fast_runner);
}
findDups3([3, 2, 2, 1, 0]);
// output: 2
```

###By algebra

Lastly a special case of this problem: each possible value appears exactly once except the duplicate one(no value is missing). Then we can calculate the duplicate value from the sum of the array.

```javascript
// find duplicate element using algebra
// Restriction: each possible value appears exactly once 
// except the duplicate one.
function findDups4(input){
    var sum1 = input.reduce(function(a,b){
        return a+b;
    });
    var m = input.length - 2;
    return sum1 - m*(m+1)/2;
}

console.log(findDups4([0,1,2,2,3,4]));
```