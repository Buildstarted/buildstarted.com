---
layout: post
title: Why do I waste time with View Engines?
---

A few people recently asked me why I bother writing View Engines like [Parrot](http://thisisparrot.com) when it's very likely that nobody will ever use them.

It's not about usage or even getting a popular library out there. It's all about the experience and growing as a developer.

One of my first large projects was a clone of text-based game. The game had the ability to add additional functionality via extensions. I wanted to support that as well but in order to do so (I was 13 at the time) I needed to get a way to put this extension in the game. The only way I knew how was to write my own language. 

I called it "Dark Tempest Scripting Language" or "DTSL" for short. It was a very strange language and very lousy.

    saysomething:
        var gold
        var level
        var str
        var hp
        var weapon
        hp ~ gethp ()
        str ~ getstr ()
        charm ~ getcharm ()
        level ~ getlevel ()
        gold ~ getgold ()
        gem ~ getgem ()
        weapon ~ getweapon ()
    	println "print something"
	end

That was a function. There were no parameters allowed. I didn't even think I could figure that out at the time. The functions you do see are built into the language itself. The parser was very simple. It expected things to look a certain way - if it wasn't formatted properly it would break. This was unacceptable but I didn't know any better at the time.

Fast forward 20 years...wow, has it really been that long? I finally got around to wanting to write another language. But why? What would it do? I've been playing with view engines a lot recently and decided I was going to make my own view engine with a custom language to boot.

This was one heck of a learning experience. I learned about parsers and state machines. Mostly from a friend of mine, Roy. 

It doesn't matter if nobody uses it. I enjoyed the entire process and it'll definitely help me in the future when I come across similar issues that I ran into while building this.

-Ben Dornis