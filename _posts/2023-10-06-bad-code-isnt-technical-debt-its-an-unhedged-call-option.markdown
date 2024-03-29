---
layout: post
title:  "Bad code isn't Technical Debt, it's an unhedged Call Option"
category: programming
tags: 
  Programming
  Code Quality
  Metaphors
---

This is all [Chris Matts](https://papachrismatts.uk) idea. He realised that the problem with the "Technical Debt" metaphor is that for managers debt can be a good thing. Executives can be required to take on more debt because it makes the finances work better, it might even be encouraged by tax breaks. This is not the same debt as your personal credit card. Chris came up with a better metaphor, the _Call Option_.

!["Payoff from writing a call."](http://upload.wikimedia.org/wikipedia/commons/thumb/5/5e/Short_call_option.svg/200px-Short_call_option.svg.png)

I "write" a [Call Option]("http://en.wikipedia.org/wiki/Call_option) when I sell someone the right, but not the obligation, to buy in the future an agreed quantity of something at an price that is fixed now. So, for a payment now, I agree to sell you 10,000 chocolate santas[^1] at 56 pence each, at any time up to 10th December. You're prepared to pay the premium because you want to know that you'll have santas in your stores at a price you can sell.

From my side, if the price of the santas stays low, I get to keep your payment and I'm ahead. But, I also run the risk of having to provide these santas when the price has rocketed to 72 pence. I can protect myself by making arrangements with another party to acquire them at 56 pence or less, or by actually having them in stock. _Or_ I can take a chance and just collect the premium. This is called an unhedged, or ["Naked" Call](http://en.wikipedia.org/wiki/Naked_call). In the financial world this is risky because it has unlimited downside, I have to supply the santas _whatever_ they cost me to provide.

Call options are a better model than debt for cruddy code (without tests) because they capture the unpredictability of what we do. If I pop in an a feature without cleaning up then I get the benefit immediately, I collect the premium. If I never see that code again, then I'm ahead and, in retrospect, it would have been foolish to have spent time cleaning it up.

On the other hand, if a radical new feature comes in that I have to do, all those quick fixes suddenly become very expensive to work with. Examples I've seen are a big new client that requires a port to a different platform, or a new regulatory requirement that needs a new report. I get equivalent problems if there's a failure I have to interpret and fix just before a deadline, or the team members turn over completely and no-one remembers the tacit knowledge that helps the code make sense. The market has moved away from where I thought it was going to be and my option has been called.

Even if it is more expensive to do things cleanly (and I'm not convinced of that beyond a two-week horizon), it's also less risky. A messy system is full of unhedged calls, each of which can cost an unpredictable amount should they ever be exercised. We've all seen what this can do in the financial markets, and the scary thing is that failure, if it comes, can be sudden&mdash;everything is fine until it isn't. I've seen a few systems which are just too hard to change to keep up with the competition and the owners are in real trouble.

To me, that makes refactoring like buying an option too. I pay a premium now so that I have more choices about where I might take the code later. This is a mundane and obvious activity in many aspects of business&mdash;although not, it seems, software development. I don't need to spend this money if I know exactly what will happen, if I have perfect knowledge of the relevant parts of the future, but I don't recall when I last saw this happen.

So, the next time you have to deal with implausible delivery dates, don't talk about Technical Debt. Debt is predictable and can be managed, it's just another tool. Try talking about an Unhedged Call. Now all we need is a way to price Code Smells.

[^1]: There is an apocryphal story about a trader buying chocolate santa futures and forgetting to sell them on. Eventually a truckload of santas turned up at the Wall Street headquarters of the bank. There's also [this story](https://thedailywtf.com/articles/Special-Delivery).
