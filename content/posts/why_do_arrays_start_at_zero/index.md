+++
title = "Why do arrays start at 0?"
date = "2023-12-17T11:45:50+0000"
authors = ["kn"]
cover = ""
tags = ["programming", "lowcode"]
keywords = ["programming", "lowcode"]
description = "you will never ask about pointer arithmetic"
showFullContent = false
+++


Do you guy know why arrays are indexed at zero?
So what's actually happening under the hood here, let takes this example:

```c
#include <stdio.h>

int main() {
    int test[3] = {1,2,3};
}
```

so if I want to get a second element here of `test`, I should write  `test[2];` right?

What's actually happening here is this `test[2];` converts the the address of `test` plus size of an `int` times two

```c
test[2]; // &test + sizeof(int)*2 
```

And because the first element lives at the zeroth location (its the zeroth offset), that why you could do like this:

```c
2[test] // that is legal C hehe
printf("%d\n", 2[test]);
```

that's taking the address two and then offseting by `test`.

C is fun...