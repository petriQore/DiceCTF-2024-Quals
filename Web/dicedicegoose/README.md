![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/a73d0c66-fd17-432c-bd12-d45729e02030)

Upon opening the website, we're met with a simple game where we can control a dice using WASD keys, there's also a black square that moves randomly whenever we move.
In order to win, we have to touch this black square. Simple.

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/5b2ee62a-38e7-4e00-bc6d-36a0f2286f84)


Let's check the source code:


![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/538fb70a-c03b-4bde-9a8f-29a2d770f116)


As you can see in the win function, we need to win with exactly 9 moves to get the flag, which is composed of a constant first part concatenated with the the encoded history.
you can paste this in the console to get an idea of how history looks like:
```
for (const element of history) {
  console.log(...element);
}
```

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/18d7f6c0-c637-4974-972b-b8893ec604d5)

as the name implies, it's the dice and the square's history of movement through the grid

So now that we know what we have to do to win, let's actually think about how we can win, because if you haven't noticed yet it's very unlikely that we can win in 9 moves, since that square moves randomly, 
luckily there's only 1 single spot in the grid where the win condition can be met, and that's the one where we keep moving down and the square keeps moving left.

There's 2 things we can do here:

First the boring method which is make our own version of the history array with the valid coordinates and then call the encode function on it.

Or we could copy the source code and change the logic that controls the square's movements making it only be able to move left, like this:

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/365ac286-9d6a-42a7-a0dd-67636a0b14c3)

instead of a switch statement and what not, the only direction left to move is left, so now if we play the game the flag should be in the console.

![image](https://github.com/petriQore/DiceCTF-2024-Quals/assets/123587287/41542d95-953f-46af-90d6-8ace73cb4902)

There it is!

``` 
dice{pr0_duck_gam3r_AAEJCQEBCQgCAQkHAwEJBgQBCQUFAQkEBgEJAwcBCQIIAQkB}
```
