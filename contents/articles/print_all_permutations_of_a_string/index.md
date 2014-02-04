---
title: Generating all permutations of a string
date: 2012-10-01 13:02
template: article.jade
---

>Given a string, print all its permutations.

There is an elegant in-place recursive solution for this problem. The idea is, for the first position, select one character from the string. Then select another character for the second position from remaining characters. Continue util we have selected a character for every position.

<span class="more"></span>

First let us simplfy the problem a little bit by assuming there is no duplicate charactors in the input string.

```javascript
function permute1(input, index){
    // found a permuation
    if (index == input.length) {
        return console.log(input);
    }
    for(var i = index; i < input.length; i++) {
        swap(input, index, i);
        permute1(input, index+1);
        swap(input, index, i);
        // input array is restored
    }
}

function swap(array, i, j){
    var tmp = array[i];
    array[i] = array[j];
    array[j] = tmp;
}

permute1([1,2,3], 0);
```

Notice that the `permute` function doesn't change the `input` array. This can be proved recusively. In the end of the recursive call chain, `permute1` simply outputs the result, so `input` is not changed. In each outer call, `input` is changed in line 7 then restored in line 9.

What if the input string contains duplicate characters? We just need to sort the input then skip duplicate characters during selection.

```javascript
function permute2(input) {
    input.sort();

    function permute_recur(input, index) {
        // found a permutation
        if (index == input.length) {
            return console.log(input);
        }
        
        for(var i = index; i < input.length; i++) {
            // skip duplicate elements
            if(i > index && input[i]===input[i-1]){
                continue;
            }
            swap(input, index, i);
            permute_recur(input, index+1);
            swap(input, index, i);
        }
    }

    return permute_recur(input, 0);
    
}

permute2([1,1,2]);
```