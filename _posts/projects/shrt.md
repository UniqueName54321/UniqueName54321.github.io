--------------------------------
layout: post
title: "optimizing https://ha.mr/ even more"
date: 2026-08-14 04:09:00 +0800
categories: projects, oped, work
--------------------------------

I love URL shorteners. Always have. Ever since I was 12, I saw the potential use in shortening URLs. At the time, I was only aware of a few shorteners, all of which rely on databases.

And then, just today, I was made aware of a project called [ha.mr](https://ha.mr/), which is basically the exact opposite. Unlike most URL shorteners, ha.mr is entirely run locally and *compresses* your URLs rather than shortening them through a database.

The difference is really technical, and if you're interested, [this video is worth a watch](https://www.youtube.com/watch?v=TOr1Vvji6jA).

However, I watched that video and went:

> "hmm... I could do better"

So, I am writing this blog post to show you how I optimized it.

## Part 0 - The Basics

I'm going to be testing both ha.mr and my own URL shortener using four URLs:

* http://uw.edu, the shortest URL in [the dataset of URLs I'm using](https://raw.githubusercontent.com/ada-url/url-dataset/refs/heads/main/out.txt)
* https://www.youtube.com/watch?v=TOr1Vvji6jA, as one example of the kinds of URLs people are typically going to be shortening
* https://rickgriffinstudios.com/housepets/comic/0614/, as another more typical URL
* [THIS URL](https://gist.github.com/UniqueName54321/2b313925b5a9074be620ef4d233f872d), which is the longest URL in [the same dataset](https://raw.githubusercontent.com/ada-url/url-dataset/refs/heads/main/out.txt)

I'm also going to be testing them on my URL shortener, obviously, as well as testing ha.mr in both its text-only form ("Normal ha.mr") and its emoji-only form ("Emoji ha.mr").

There *is* also t.ha.mr, which relies on Transformers to do the shortening, but Transformers are nondeterministic and as such there's no easy way to test them consistently.

## Part 0.5 - Baseline Testing

Before doing anything myself, I wanted a baseline. So here's what plain old ha.mr produces for each URL.

### Test 1: `http://uw.edu`

This is deliberately the shortest URL in the dataset, which means compression doesn't really have much room to work with.

**Normal ha.mr**

`http://ha.mr/#GkIU`

That's **66% larger** than the original.

**Emoji ha.mr**

`http://ha.mr#🧑🏻‍🎓🧍👨🏻‍❤️‍💋‍👨🏻`

That's **50% larger** than the original.

So, for extremely short URLs, ha.mr actually makes things worse. Not exactly surprising, since there is a certain amount of unavoidable overhead involved.

### Test 2: YouTube

Original:

`https://www.youtube.com/watch?v=TOr1Vvji6jA`

**Normal ha.mr**

`http://ha.mr#b~9nFB([Ccz4dv.A*PQW#`

That's **23% smaller**.

**Emoji ha.mr**

`http://ha.mr#🙅🏼‍♀️🧍🏻🌋🥂🖐🏻🤜🏼🧑🏻‍🦼🪛😒🧑🏻‍🐰‍🧑🏽🏃🏼‍♀️`

That's **52% smaller**.

Now we're getting somewhere.

### Test 3: Housepets!

Original:

`https://rickgriffinstudios.com/housepets/comic/0614/`

**Normal ha.mr**

`http://ha.mr#jr#is=w@8Rzg8wecY+gQ@(hw~,4_/ys)!`

That's **14% smaller**.

**Emoji ha.mr**

`http://ha.mr#🐑👳🏽‍♂️🤾🙇🏾‍♂️🧏🏻‍♀️👩🏿‍⚕️👈🏼🕢🗜️🌩️👰🏼‍♀️📙🛍️💇🧚🏼🔢👩🏽‍❤️‍👩🏽`

That's **48% smaller**.

Again, emoji ha.mr wins by quite a bit.

### Test 4: The Horrific One

Finally, we have [the longest URL in the dataset](https://gist.github.com/UniqueName54321/2b313925b5a9074be620ef4d233f872d).

I could paste both compressed URLs here in full.

I am not going to do that.

The Normal ha.mr result is still **thousands of characters long**, while the Emoji ha.mr result is an equally ridiculous wall of Unicode. You can probably imagine why this article was beginning to make VSCode reconsider its career choices.

The important part is the compression:

* **Normal ha.mr:** 3% smaller
* **Emoji ha.mr:** 48% smaller

And that gives us our baseline.

Normal ha.mr does reasonably well once URLs get long enough, but the emoji version is *much* more effective. Even on the enormous stress-test URL, it manages to nearly halve the size.

Which raises a pretty obvious question.

## Part 1 - The Elephant In The Room

Normal ha.mr is restricted to only Base85. Well, more technically, a modified URL-safe version of ASCII.

That means:

`!#$&'()*+,-./0123456789:;=?~@ABCDEFGHIJKLMNOPQRSTUVWXYZ[]_abcdefghijklmnopqrstuvwxyz`

is the *entire* alphabet.

Emoji ha.mr shifts this up to a cool Base3953, which is about 46 and a half times larger.

However...

What if you were to just use the entire Unicode plane?

Well, firstly, then you'd run into unprintable characters like controls and other nasty critters.

Secondly, p2r3, the creator of ha.mr, already tried that. He eventually decided that ha.mr would focus on URLs that are relatively easy to type.

This is fair.

But that's not what I'm going for with shrt.

I'm going for **maximum compression**.

Therefore, I implemented the entire printable Unicode plane.

This gives us Base159629, or **"Unicode shrt"**, right off the bat.

Base159629 is 40x larger than Emoji ha.mr, and *1877%* bigger than Normal ha.mr!

And that's where things start getting interesting.

## Part 1.5 - Base159629 Testing

Before going any further, it's probably worth actually testing this thing.

**PLEASE NOTE:** for consistency with the previous tests, I'm going to keep using `http://ha.mr` as the root URL.

This **does not** mean ha.mr itself suddenly has any of these features. I simply added the ability for shrt to use an arbitrary root URL.

As such, the actual length of a generated URL may be longer or shorter depending on **where you're accessing shrt from**. I'm just using the ha.mr root here so that the results can be compared directly with the previous tests.

With that out of the way:

### Test 1: `http://uw.edu`

Original:

`http://uw.edu`

Base159629:

http://ha.mr#u:𑁤ü

That's **183% larger** than the original.

...yeah.

This is the same problem ha.mr had earlier, except even worse. When your original URL is already microscopic, the overhead from `http://ha.mr#u:` completely overwhelms any compression you might gain.

Not exactly Base159629's finest hour.

### Test 2: YouTube

Original:

`https://www.youtube.com/watch?v=TOr1Vvji6jA`

Base159629:

http://ha.mr#u:홙爵𒔜𐿤𗝓𑆫䲔ű

That's **35% smaller** than the original.

This is already pretty decent, although interestingly it's still not enough to beat Emoji ha.mr's **52%** reduction on the same URL.

### Test 3: Housepets!

Original:

`https://rickgriffinstudios.com/housepets/comic/0614/`

Base159629:

http://ha.mr#u:䐎𢿨ꫧ튳𮢐𐖨𐴐憊𑆳𪃘𓠘Ⱥ

That's **39% smaller**.

Again, we're getting a respectable reduction, although Emoji ha.mr still manages **48%** on this particular URL.

But then we get to the thing this ridiculous alphabet is actually good at.

### Test 4: The Horrific One

Yes.

**That URL.**

The enormous one.

Base159629 manages to compress it by **64%**.

That's substantially better than either of ha.mr's previous results:

* Normal ha.mr: **3% smaller**
* Emoji ha.mr: **48% smaller**
* Unicode shrt: **64% smaller**

The actual output starts like this:

`http://ha.mr#u:皚𱣑욱𰠁𪺬𘁑𩗶𭶍䧩𨴕𣂙䋹𓏎𧨔気𩈬ꤼ𣱔...`

...and then continues for an absolutely unreasonable amount of time.

It is still **not small enough to comfortably display in its entirety**, which should tell you something about how catastrophically huge the original URL is.

Still, this is where Base159629 finally gets to stretch its legs.

For normal human-sized URLs, the fixed overhead means that simply throwing a gigantic alphabet at the problem doesn't automatically win. Emoji ha.mr can actually beat it on some of the smaller tests.

But once the input becomes sufficiently enormous, the advantage of having **159,629 possible symbols per character** starts becoming extremely obvious.

And we've gone from compressing the monster URL by **3%**, to **48%**, to **64%**.

Now we're cooking.

## Part 2 - A Burrows–Wheeler transform walks into a bar

Okay. We have a giant alphabet.

Can we make the actual data itself easier to compress?

Enter the **Burrows–Wheeler transform**, or BWT.

A BWT is a reversible transformation that rearranges the characters in a string so that characters which occur in similar contexts tend to get grouped together.

Importantly:

**BWT is not compression.**

It does not take a 100-character string and magically turn it into 70 characters. In its basic form, the transformed data is essentially the same size as the input, plus a little bit of information required to reverse the transformation.

Instead, BWT is supposed to be a **before step**.

The idea is roughly:

`original URL -> BWT -> compression -> encoded URL`

rather than:

`original URL -> compression -> encoded URL`

Why bother?

Because compression algorithms tend to really like repetition.

For a simple example, imagine some data containing a bunch of repeated patterns:

`BANANA_BANDANA`

A BWT rearranges the data in a way that tends to shove similar characters together. Instead of having useful patterns scattered throughout the string, you can end up with long-ish groups of the same or similar symbols.

That's extremely convenient for later stages like run-length encoding, move-to-front encoding, Huffman coding, arithmetic coding, and other compression techniques.

This is, in fact, one of the ideas behind **bzip2**.

So BWT itself isn't supposed to make the URL smaller.

It's supposed to hand the compressor a version of the URL that's **easier to make smaller**.

At least...

that's the theory.

## Part 2.5 - BWT Test

Time to find out whether the theory actually survives contact with shrt.

For these tests, the URL goes through BWT **before** the usual compression step.

I'm testing all three output alphabets again:

* text-only
* emoji-only
* Unicode / Base159629

And the results are...

Well.

### Test 1: `http://uw.edu`

Original:

`http://uw.edu`

**Text-only BWT**

`http://ha.mr#b::*pZihIB~-&.xH]Oh!VD3GgiK&,wLRW:+&H*`

**750% larger.**

**Emoji-only BWT**

`http://ha.mr#b:💵🇵🇭👨🏼‍🐰‍👨🏾🇸🇲🧎🏼‍♀️🧩🧙🏼‍♂️🌇🇺🇸👨🏾‍🚀🇦🇶👌🏽👌🏽👨🏿‍❤️‍💋‍👨🏽🔬✌️🅰️🧑🏻‍🤝‍🧑🏽🛴`

**466% larger.**

**Unicode BWT**

`http://ha.mr#bu:莪🖥𗎄𖧱𠬳𘈠硕녶𪞪𢃋𦔼훾츦#`

**400% larger.**

Beautiful.

We have taken one of the shortest URLs in the dataset and, through the power of computer science, made it **eight and a half times its original size** in the worst case.

This isn't particularly surprising, though. BWT has overhead, the shrt format itself has overhead, and there is basically no data here for BWT to reorganize into anything useful.

### Test 2: YouTube

Original:

`https://www.youtube.com/watch?v=TOr1Vvji6jA`

**Text-only BWT**

`http://ha.mr#b:,y77(~AtgNz+-xYSVos_/YM*dmfp7)YRbWf7~jHyAF'=6a7W1XGK(f,sKRgOwq='0w9-,y2y@N7-7wc8MH!q0AbQfmbu;jYU~jeJ)+7b&-a:uN~J0&`

**268% larger.**

**Emoji-only BWT**

This produces another respectable wall of emoji.

**117% larger.**

**Unicode BWT**

`http://ha.mr#bu:󠅘𬻦𝁀띎𡸨𘷘뎭𧊹𓬶𨖶藵𤮷蔨𓖌𗴵戚𡳱𖽉𠱛𩸽𬜤𫯱𦈈𰙻抉𨥠𰻜ᮝ䑺힁𦖾뻸𒊇𲚘𬗖禌𰲨𝦖酯𧹡𡳗ﭗ`

**65% larger.**

So even on an ordinary real-world URL, BWT still loses badly.

Unicode BWT loses the *least* badly, but that's not exactly the victory parade I was hoping for.

### Test 3: Housepets!

Original:

`https://rickgriffinstudios.com/housepets/comic/0614/`

**Text-only BWT**

**209% larger.**

**Emoji-only BWT**

**81% larger.**

**Unicode BWT**

`http://ha.mr#bu:𣯏𱎭⨬めઠ𰗭흤𬅕𭮊𣁱紅𡿍𫛾🧖𰹢𭃴𜽣𧗓篼󠆴𨸢圴𢥭ꁙ袐𣩔𧄥𡒂𤫠𦣓멆𫴻倒𘌅春𒊲𬩀𢾑𬑖𣸷𓾅𒅼🎡პኋ`

**38% larger.**

We're getting closer!

Unfortunately, "only 38% worse than doing absolutely nothing" is still not what I'd personally call a compression breakthrough.

For comparison, plain Base159629 without BWT made this same URL **39% smaller**.

So on this test, adding BWT takes us from:

**39% smaller**

to:

**38% larger**

That is not an improvement.

That is a U-turn.

### Test 4: The Horrific One

And finally:

**the monster URL.**

Surely this is where BWT gets its revenge.

This is an enormous input with plenty of repeated structure, which is exactly the kind of thing BWT is supposed to become more useful on.

The outputs are once again far too large to sensibly reproduce here in full, but the results are:

* **Text-only BWT: 181% larger**
* **Emoji-only BWT: 51% larger**
* **Unicode BWT: 4% larger**

And that last one is actually interesting.

Unicode BWT gets **extremely close**.

The monster URL goes from being thousands upon thousands of characters long to an encoded BWT result that's only **4% larger** than the original.

But there's one rather large problem.

Plain Unicode shrt, **without BWT**, already made this URL **64% smaller**.

So the progression is:

* Normal ha.mr: **3% smaller**
* Emoji ha.mr: **48% smaller**
* Unicode shrt: **64% smaller**
* Unicode shrt + BWT: **4% larger**

...

# Well, shit.

BWT is doing basically the opposite of what I wanted.

And to be clear, this doesn't mean BWT is a bad transform or that the entire compression industry has collectively failed to notice something obvious.

BWT is useful when it's paired with compression stages that can exploit the structure it creates.

My compressor, however, apparently looks at that newly rearranged structure and responds by setting it on fire.

The interesting part is that the results **do improve as the input gets larger**.

Unicode BWT goes from:

* **400% larger** on `uw.edu`
* **65% larger** on YouTube
* **38% larger** on Housepets!
* only **4% larger** on the monster URL

So BWT clearly becomes less disastrous as it gets more data to work with.

It's just that "less disastrous" is not the same thing as "better."

For now, BWT goes into the rapidly growing folder labelled:

**GOOD IDEA, WRONG COMPRESSOR.**
