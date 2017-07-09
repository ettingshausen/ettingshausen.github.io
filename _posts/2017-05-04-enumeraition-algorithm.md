---
layout: post
title:  "枚举算法之最大素数"
date:   2017-05-04 09:09:06 +0800
categories: algorithm
---

问题：求解（0，N）中的最大素数
```js
function getPrim(n) {

    let begin = new Date();

    let prim = [2];

    let isDivided = function (x, y) {
        return x % y === 0;
    };

    for (let i = 3; i < n; i += 2) {

        for (let index = 0; index < prim.length; ++index) {
            if (isDivided(i, prim[index])) {
                break;
            } else {
                if (index === prim.length - 1) {
                    prim.push(i)
                }
            }
        }
    }

    console.log(prim)
}

getPrim(100000);
```