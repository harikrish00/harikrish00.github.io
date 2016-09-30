# Learn Regex With Me

#### Validating IP4 Address With Python Regex

I assume you have some basic knowledge about regular expression, know python comfortably and its `re` library.

A typical IP4 address will look like this

`10.45.44.123`

An IPv4 address has 4 parts, all numbers and each one can vary from 0 - 255.
The parts are separated by a dot. Enough of basics, lets jump into solving this.

In a regex world `[0-9]` or `\d` denotes digits from 0 to 9.
So quantifier { } can specify the repetition. You would think
  `\d{3}\.\d{3}\.\d{3}\.\d{3}` or  `[0-9]{3}\.[0-9]{3}.[0-9]{3}.\[0-9]{3}`
  would do the trick. Though the pattern might work for most of the scenarios, but it ll match even this `999.999.999.999`. This is totally an invalid ip address.

  Lets take a slightly different approach.
  do you think `[0-255]` will cut it? NO :). Its ok if you got this crazy idea, I did too and lazy right?
  Why it won't work? because its a `character class` and you can only account for single character not a range of numbers.

  With that said, take a moment and see if your logical & lateral thinking kicks in...

  Anything yet?

  No, nothing?

  Ok here we go, a small tip for you ... you can use [0-9] or similar in a variety of ways to cover the range `0 - 255`.

We need to split the range into multiple character classes which can do the trick for us.

`[0-9]|[1-9][0-9]`

whats happening here?

1. the first character class before the pipe | will match between `0 - 9`
2. the two character class after the pipe | will match between `10 - 99`.
Wait a second I understand #1 above, but #2? I can hear your mind voice.

When multiple character classes are present only with numbers then to figure out the range you can do one thing.
Take the first digit from all the classes, which will make the lower range. For example 1 and 0, and when put together that becomes 10.
Now take the second digit and put together, what comes of it?
9 and 9 = 99 which is the higher range. Now you understand better ?

Try making the remaining ranges and I ll reveal this in a bit.

**Reveal**:
`[0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5]`

Maps to

`0 - 9 | 10 - 99 | 100 - 199 | 200 - 249 | 250 - 255`

Yes we did it, now any offending address part which is beyond 255 doesn't validate.

Lets pretty it up a bit
- Now you can make the regex pattern shorter by replacing `[0-9]` with `\d`.
- Add a capturing or non-capturing group to hold them together
- Also adding the `\.` for matching the dot character after every block.

`((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.)`

Still the pattern is incomplete. In order to match the whole address, we need to somehow repeat this pattern number of times. I can hear you saying lets use quantifier. Yay ! you are awesome ....
what? wait, what number you came up with for the quantifier?
3 or 4 ? Either way you're a genius unless you did not think of one consequence for the number 4. Lets see this in detail

#### Number 3 saga

`((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.){3}` matches `127.2.4`

(the pattern is repeated 3 times)

Now just add the pattern one more time at the end, but this time without the `\.` since the last part doesn't have a dot at the end.

`((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.){3}(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])`

#### Number 4 saga ultimate

Its pretty neat  what we did in #3 above except the regex looks a little longer.

Lets use {4} and see what happens?

`((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.){4}` matches  `124.5.6.122.`

Did you notice the `.` at the end?

No IP ends with a `.`

This is the consequence I was talking about earlier.
But if you thought about adding an additional `.` to the given IP4 address then pattern matching works just fine and validation is 100% legit.

You can do pattern matching like below. Smart right?
```python
re.match(pattern, address + '.')
```

Lets crunch all the above lines into a few lines which will validate IP4 address for eternity to come.

```python
import re

def validate_ip4(address):
  pattern = re.compile(r'((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.){4}')
  return True if re.match(pattern, address) else False
```
