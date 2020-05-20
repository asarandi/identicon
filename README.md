# identicon

go package to generate github identicons

based on dgraham's rust identicon

https://github.com/dgraham/identicon

## algorithm

github identicons are generated from the md5 hash of a user's id.

there are two components to an identicon: the **pattern** and the **color**

for example: my user id is `32273214`
```sh
$ echo -n "32273214" | md5sum
3a44233aabcac24ed7c6e160b30f4289  -
```
#### pattern
the first 15 nibbles of the md5 hash make the identicon pattern
(nibble == half byte == 4 bits)

even nibbles result in colored (foreground, o) pixels, odd nibbles result in gray (background, -) pixels

in my case:
```
3 a 4 4 2
3 3 a a b
c a c 2 4
```
produces:
```
- o o o o
- - o o -
o o o o o
```
the final identicon pattern is made by rotation and mirroring:
```
o - - - o
o - o - o
o o o o o
o o o o o
o - o - o
```

#### color
color is calculated from the last 7 nibbles (28 bits) of the hash `HHHSSLL`:

the `HHH` (0..4095) value is remapped to a value between `0..360` degrees and is used as the **hue**

the `SS` (0..255) value is remapped to a value between `0..20` and is subtracted from a max **saturation** of `65` percent

the `LL` (0..255) is remapped to a value between `0..20` and is subtracted from a max **lightness** of `75` percent


in my case `30f4289`:

`0x30f` is remapped to `68.83` degrees, which is my hue,

`0x42` is remapped to `5.17`, so my saturation is 65 - 5.17 == 59.83%,

`0x89` is remapped to `10.74`, so my luminance is 75 - 10.74 == 64.26%.

further these values are converted into the rgb color space resulting in ![#cada6d](https://via.placeholder.com/16/CADA6D/CADA6D)`#cada6d` rgb color code


the combination of the **pattern** and **color** create the final identicon image

![](img/32273214.png)



## usage
all exported functions take `data []byte` and `size int`

`data` is used as input to md5.

**note**:
`size` is identicon pixel size (not final image size).

final image will be 6 x 6 pixels of given `size`

(that is a 5 x 5 matrix plus margins)

## example
```go
package main

import (
	"fmt"
	"github.com/asarandi/identicon"
)

func main() {
	for i := 0; i <= 10; i++ {
		s := fmt.Sprintf("%d", i)
		identicon.File([]byte(s), 8, s+".png")
	}
}
```

![0](img/0.png)
![1](img/1.png)
![2](img/2.png)
![3](img/3.png)
![4](img/4.png)
![5](img/5.png)
![6](img/6.png)
![7](img/7.png)
![8](img/8.png)
![9](img/9.png)
![10](img/10.png)

### license
MIT
