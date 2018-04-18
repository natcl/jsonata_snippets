# Random arrays

## Random array of 512 values between 0 and 255

```
[1..512].($random() * 255 ~> $floor)
```

## Random array of 4 values between 0 and 255 repeated over 512 values

```
(
	$arr := [1..4].($random()*255~>$floor);
    [1..128].($arr)
)
```
