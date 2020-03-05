---
layout: post
title: To Infinity And Beyond
subtitle: The Quest For Arbitrarily Large Numbers in C#
categories: Coding
---

Incremental games face a rather unusual programming challenge: the need to represent numerical values without any upper bound. Since the whole point of an incremental game is to gain exponentially more power after every reset, without ever reaching a final limit, conventional numerical types of any size are simply too restrictive.

The largest possible value of any conventional type is that of the double precision floating point value: about 1.7e308. See [Numerics in .NET](https://docs.microsoft.com/en-us/dotnet/standard/numerics) for a good reference on all of the numerical types and their limits. That’s quite large, but many an incremental game will eventually exceed it.

As I began exploring ways to approach this problem, I first discovered [BigInteger](https://docs.microsoft.com/en-us/dotnet/api/system.numerics.biginteger), which is part of the .NET framework. This type does allow for arbitrarily large values, but only integers. This could potentially work for an incremental game, as they often don’t need decimal precision for large values, but it’s a problem when the values are still small at the start of the game. For example, if a value starts at 10, and an upgrade adds 25%, the value would become 12.5, no longer an integer.

We really want to keep decimal precision, combined with arbitrary size. Along these lines, I next found [BigDecimal](https://github.com/AdamWhiteHat/BigDecimal), which does meet both requirements. This class works by storing two values: a BigInteger for the mantissa, and a regular integer for the exponent. If you need a quick refresher for those terms, read up on [Significand](https://en.wikipedia.org/wiki/Significand). For example, the value 123.45 would have a mantissa of 12345, and an exponent of -2.

So, problem solved, right? Yes, but with one big caveat: BigDecimal is concerned not only with arbitrary size, but arbitrary precision. It uses BigInteger for the mantissa so that it can keep any number of significant figures, and the tradeoff for doing this is speed. Once you start operating on many large BigDecimal values, and tiny rounding errors are introduced, it’s easy to end up with mantissa values that are hundreds of digits long, and further operations are slowed down considerably. This is a major problem for a game that may need to do hundreds of operations on these values every frame.

Luckily, while incremental games do need arbitrary size, they don’t need arbitrary precision. The player doesn’t need (or want) to see a value of, say, 4.7633902e412, rather than 4.8e412. So we don’t need to be using BigInteger to store the mantissa, we could just use a regular double. Enter [BigDouble](https://github.com/Razenpok/BreakInfinity.cs), which does exactly that, using a double for the mantissa, and a long (int64) for the exponent. Now we have it: arbitrary size, sufficient decimal precision, and high performance.

If you’re coding an incremental game, or perhaps a tool or website about an incremental game, BigDouble is an essential piece of the puzzle. Technically, the max value of a BigDouble is about 1e(9e18), since the exponent is a long. Not quite infinity, but close enough.

## Links
- <https://docs.microsoft.com/en-us/dotnet/standard/numerics>
- <https://docs.microsoft.com/en-us/dotnet/api/system.numerics.biginteger>
- <https://github.com/AdamWhiteHat/BigDecimal>
- <https://en.wikipedia.org/wiki/Significand>
- <https://github.com/Razenpok/BreakInfinity.cs>
