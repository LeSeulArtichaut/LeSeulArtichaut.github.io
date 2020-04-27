---
layout: post
title: "The basics of memory: the stack and the heap"
---

One thing that I was struggling with when I first tried to learn a low-level
langage was the difference between stack and heap. It's something that you never
have to worry about in high-level languages, because it is abstracted to you
(this is what a high-level langage is for, after all). However, in low-level
langages, there is no interpreter you can rely on to manage memory. You have
to do it yourself.

In this post, I would like to try and picture what the stack and the heap are,
using a quite common analogy for computer memory. Memory can be thought as drawers
where you can put stuff in to retrieve it back later.

### The stack

The stack is like a pile of drawers. Every local variable you introduce has its
content stored on the stack. In fact, every time you call a function, you add a
new drawer on top of the stack, which is big enough to store every local variable
it has, no more no less. When a function ends, you can simply remove the drawer at
the top of the pile, freeing any memory that was allocated for its execution. This
is why, even in C or C++, you don't actually need to manually allocate memory for
local variables , nor to manually drop every local variable at the end of every
function: the compiler can do it for you… so long as the drawer has a constant size
for any given function.

This is an important property of the stack, and, in some situations, a limitation
What if we don't know exactly how much memory we need to store a piece of information?
Let's see a basic example: prompting the user some text. When we compile our program,
we have no idea how long that text will be! It could be a single word, as well as a
novel or, even longer, a religious text! How can we handle that?

The first option we have in our low-level toolbox is creating a local variable with
a given size, maybe for example 100 characters. The compiler will then understand
that it should design a drawer big enough to fit 100 characters in it. However, there
are 2 drawbacks to that approach. The first is pretty obvious: if the user only
needs to store a single word, the rest of the memory we allocated is wasted. This
is a pretty minor drawback for most situations, but it is worth noticing. The bigger
drawback however, is that we cannot store more than 100 characters. Not even a single
additional character! So you now have a dilemma. Do I want to allocate more memory
and waste it in most situations? What if I told you that there is a solution to not
waste any memory, AND allow for any value to be stored? The solution is the heap.

### The heap

The heap is often introduced as a dynamic memory pool you can allocate memory in.
But I like drawers today, so let's picture it as a giant room filled with empty
drawer chests all over the place. In there, you can put drawers of any size you
want, as long as there is room for it of course. Oh, and also, there is a manager
in the room. You come with your drawer, and they'll tell you where to place it.
Randomly, but making sure that there is no other drawer in that spot. You shouldn't
touch someone else's drawer, or you'll be in trouble, that seems obvious, doesn't it?
Also, unlike the stack where you know for sure where your drawer is, (it's on top
of the pile, you cannot be mistaken!), in the heap room it might be anywhere. So,
you'll have to remember where it is to find it later. It's in alley 716, chest 81062,
at position 8179281? Write that on a piece of paper, and store that on the stack.
A piece of paper isn't big and it can be always the same size, so you can put that
in your stack drawer. Don't forget that it isn't the manager's job to know whether
you actually use the drawer location it gave you! It isn't paid for that, that is
the garbage collector's job. If you forget about your drawer, if don't notify the
manager that you don't need it anymore, it will stay there and prevent any other
drawer to be stored there.

Alright, back to computer memory! Let's call the heap manager the allocator, because
it allocates memory for our data to prevent collisions between multiple drawers.
Instead of alleys, chests and positions, we'll use memory addresses. An address
in memory can be represented using just an integer, so just like a piece of paper
you can easily store that on the stack. Also we need to name that address. Let's 
all it a “pointer”, because it “points” to a certain place in memory, where our data is.

Now we can solve the user-inputted string problem. We know that we'll store the
string on the heap, so we can tell the compiler that we will need to store an address,
so it can design a drawer of the right size to put on the stack when our function
is executed. Then, when we need to store the user's string, and we finally know
how long it is, we can go on the heap, ask the allocator to allocate just enough
memory for the string, and everything will be fine! We didn't waste memory by
allocating more memory than we actually needed. We aren't much limited in the size
of the string we can store, because we have plenty of room, as long as Chrome isn't open.

Of course, as you might expect, there are drawbacks to this approach. First,
allocating room for your drawer takes some time, because the allocator has to find
a place big enough to store your data. This is nothing significant for a single allocation,
but it can become a problem when you need to allocate multiple times. This is why,
if you know that the amount of data you will need to store might grow, you should
consider allocating more than you currenlty need, to avoid another allocation in
the future. Second drawback: the string isn't directly stored on the stack, where
your function lives. You have to take the pointer and walk through the heap to
retrieve your data. Every time you want to access it, you loose a small bit of time.
Again, nothing significant for most cases, but it is still good to remember.
Last but not least, you have to manage the memory you allocate on the heap. Unlike
the stack, where the compiler can help you out, you are responsible for freeing
any memory you allocate on the heap. You are also responsible for making sure
that you fetch the right drawer and not someone else's, or that you don't corrupt
your data if you share it with other processes. Those are memory safety problems.
It's easy to mess things up if you aren't careful enough, even the most experienced
developers can make mistakes there. They are responsible for hard to debug issues
and big security leaks. They also are the main reason why the Rust language exists.
The heap gives programmers new capabilities, but with great power comes great responsibility.

### Conclusion

The stack is a statically sized memory pool for the local variables in your function.
In most cases, it is the safest and most efficient way of storing data in memory
because you know at compile time how much memory to allocate, when to dealoocate it,
and where each variable is stored. This makes it possible for the compiler to handle
the memory for you.

However, when you need to store some data whose size is unknown at compile time, you
can resort to the heap, which is a dynamically sized memory pool. Whenever you need
to store a variable, you can allocate memory for it. However, you are now responsible
for making sure that when you access data through the use of pointers, you only access
the part of the heap that is assigned to you. You also need to deallocate that memory
once you're done with it, to avoid leaking memory.

### Thanks a lot for reading!

If you made it to here, I hope you liked this blog post. This is my very first time
trying to write anything, so I'd really appreciate any feedback. You can find me on
GitHub (@LeSeulArtichaut), on Discord (@LeSeulArtichaut), or on Reddit (u/LeSeulArtichaut).
Also, if you spotted a mistake in this post, please open an issue or a pull request on
[the GitHub repository for this blog][gh]. It would really help!

What inspired me to try to create this was the [2019 Rust Survey][survey]:

> People are in general asking for more learning material about Rust. In terms of expertise
> it's mainly beginner and intermediate level material being requested. A lot of these
> requests also asked for video content specifically.

I would like to know if I should try to adapt this post into a video. It seems interesting
to me, since the analogy with drawers would probably work well in a video. Also, if there are
French people reading this, as you may have guessed, I am French, so I would like to know
if I should also try to write posts in French too.

[gh]: https://github.com/LeSeulArtichaut/LeSeulArtichaut.github.io
[survey]: https://blog.rust-lang.org/2020/04/17/Rust-survey-2019.html
