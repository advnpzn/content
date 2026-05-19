---
title: "zstd is cool"
date: 2024-11-26
description: "A quick look at zstd compression algorithm and why it's awesome"
tags: ["compression", "zstd", "performance", "tech"]
---

[zstd / Zstandard](https://en.wikipedia.org/wiki/Zstd) is a compression algorithm from Facebook(now Meta) developed by Yann Collet at
around ~2015-2016.

Not gonna lie, but `zstd` is kinda cool to me.

Why ?

Because it is a lot faster than some compression algorithms like `gzip` while still maintaining the same compression ratio. I kind of feel bad that I
only got to know about this today, while skimming through the internet to see how
I can reduce the size of the `*.tar.gz` file that I created. I usually use `gzip` because I haven't really had a reason to look out for options to be honest.

`gzip` is based on the [DEFLATE](https://en.wikipedia.org/wiki/Deflate) format that uses a combination of LZ77 and Huffman coding.

## Tests

I've even done a small test to see which performs better b/w `gzip` & `zstd`.

## Environment

I used a `wsl2` environment. I used [hg-engine](https://github.com/BluRosie/hg-engine) git repository for testing.

I had run some builds and other stuff inside this git repository and the total
size is around 1.8 GiB.

```bash
du -sh hg-engine/
1.8G    hg-engine/
```

|  |  |
| --- | --- |
| OS | Debian GNU/Linux bookworm 12.8 x86_64 (WSL2) |
| Processor | AMD Ryzen 5 5600H (12) @ 3.29 GHz |
| Ram | 7.43 GiB |

I'm going to try and compress this directory by both `gzip` and `zstd` using single thread.

## Results

|  | gzip | zstd |
| --- | --- | --- |
| Size | 537 MiB | 300 MiB |
| Time | 56 secs | 27 secs |

Gzip
```bash
time tar -I gzip -cf test.tar.gz hg-engine/* && du -sh test.tar.gz

real    0m56.044s
user    0m0.002s
sys     0m0.006s
537M    test.tar.gz
```
Zstd
```bash
time tar -I zstd -cf test.tar.zst hg-engine/* && du -sh test.tar.zst

real    0m27.091s
user    0m0.006s
sys     0m0.000s
300M    test.tar.zst
```

## Conclusion

From this very basic and small test, you could see that `zstd` performed extremely well in terms of both the size of the final compressed file
and the time taken to compress.

I know that I should have tested this more thoroughly like,

• Multiple threads  
• varying compression levels  
• Decompression time

And a lot…

For modern hardware, `zstd` seems to be the most suitable one. And I'm going to use `zstd` a lot :)
