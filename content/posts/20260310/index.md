---
tags:
  - Oneliner-Py
  - Python
---

# break/return合并

考虑这样的代码：

```py
def check_sum():
    i, j = 0, 0
    while i<10:
        i+=1
        while j<10:
            j+=1
            if i+j > 20:
                print("nope")
                return
    print("yep")
```

以下是转为Oneliner之后大概的样子（简化以方便阅读）：

```py
check_sum := lambda: 
    (__ol_ret_ccgpmcgcrd := False),
    (__ol_break_jpijwiipeo := False),
    [
        [
            (i := i + 1),
            (__ol_break_dqdzzxrfhe := False),
            [
                [
                    (j := j + 1),
                    (
                        [
                            print("nope"),
                            [
                                (__ol_break_jpijwiipeo := True),
                                (__ol_break_dqdzzxrfhe := True),
                                (__ol_ret_ccgpmcgcrd := True),
                            ],
                        ]
                        if i + j > 20
                        else ...
                    ),
                ]
                for _ in itertools.takewhile(
                    lambda _: not __ol_break_dqdzzxrfhe and j < 10,
                    itertools.count(),
                )
            ],
        ]
        for _ in itertools.takewhile(
            lambda _: not __ol_break_jpijwiipeo and i < 10, 
            itertools.count(),
        )
    ],
    print("yep") if not __ol_ret_ccgpmcgcrd else ...,
```

可以看到，对于break和return，总共使用了三个临时变量来处理。然而这三个临时变量完全可以合并为一个：

```py
check_sum := lambda: 
    (__ol_ret_ccgpmcgcrd := False),
    [
        [
            (i := i + 1),
            [
                [
                    (j := j + 1),
                    (
                        [
                            print("nope"),
                            (__ol_ret_ccgpmcgcrd := True),
                        ]
                        if i + j > 20
                        else ...
                    ),
                ]
                for _ in itertools.takewhile(
                    lambda _: not __ol_ret_ccgpmcgcrd and j < 10,
                    itertools.count(),
                )
            ],
        ]
        for _ in itertools.takewhile(
            lambda _: not __ol_ret_ccgpmcgcrd and i < 10, 
            itertools.count(),
        )
    ],
    print("yep") if not __ol_ret_ccgpmcgcrd else ...,
```

怎么实现呢？值得商榷

