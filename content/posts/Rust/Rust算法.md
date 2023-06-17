---
title: "Rust排序算法"
description: "Rust排序算法"
keywords: "Rust"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Rust
tags:
  - Rust
---

## 1. 归并算法
```toml
[dependencies]
rand = "0.8.5"
stopwatch = "0.0.7"
```
```rust
use rand::prelude::*;
use stopwatch::Stopwatch;

fn merge_sort(arr: &mut [i32]) {
    if arr.len() > 1 {
        let mid = arr.len() / 2;
        merge_sort(&mut arr[..mid]);
        merge_sort(&mut arr[mid..]);
        merge(arr, mid);
    }
}

fn merge(arr: &mut [i32], mid: usize) {
    let left = arr[..mid].to_vec();
    let right = arr[mid..].to_vec();

    let mut l = 0;
    let mut r = 0;

    for data in arr {
        if r == right.len() || (l < left.len() && left[l] < right[r]) {
            *data = left[l];
            l += 1;
        } else {
            *data = right[r];
            r += 1;
        }
    }
}

fn main() {
    // 创建计时器
    let sw1 = Stopwatch::start_new();

    // 创建随机数组
    let mut rng = rand::thread_rng();
    let mut nums: Vec<i32> = (1..100000).collect();
    nums.shuffle(&mut rng);
    println!("创建数据耗时 {}ms", sw1.elapsed_ms());
    // 开始排序
    let sw2 = Stopwatch::start_new();
    merge_sort(&mut nums);
    println!("排序耗时 {}ms", sw2.elapsed_ms());
}

```
## 2. 选择排序
```rust

struct Solution;

impl Solution {
    pub fn select_sort(arr: &mut Vec<i32>) {
        for i in 0..arr.len()-1 {
            let mut k = i;
            for j in i+1..arr.len() {
                if arr[j] < arr[k] {
                    k = j
                }
            }
            arr.swap(i, k);
        }
    }
}

fn main() {
    let mut arr = vec![27, 38, 13, 49, 76, 97, 65];
    Solution::select_sort(&mut arr);
    println!("arr = {:?}", arr);
}
```
## 3. 冒泡排序
```rust
struct Solution;

impl Solution {
    pub fn bubble_sort(arr: &mut Vec<i32>) {
        for i in 0..arr.len() - 1 {
            let mut change = false;
            for j in 0..arr.len() - 1 - i {
                if arr[j] > arr[j + 1] {
                    arr.swap(j, j + 1);
                    change = true;
                }
            }
            if !change {return;}
        }
    }
}

fn main() {
    let mut arr = vec![27, 38, 13, 49, 76, 97, 65];
    Solution::bubble_sort(&mut arr);
    println!("arr = {:?}", arr);
}

```
## 4. 插入排序
```rust
struct Solution;

impl Solution {
    pub fn insert_sort(arr: &mut Vec<i32>) {
        for i in 1..arr.len() {
            let mut j = i;
            while j > 0 {
                if arr[j] < arr[j - 1] {
                    arr.swap(j, j - 1);
                }
                j -= 1;
            }
        }
    }
}

fn main() {
    let mut arr = vec![27, 38, 13, 49, 76, 97, 65];
    Solution::insert_sort(&mut arr);
    println!("arr = {:?}", arr);
}

```
