---
layout: post
title:  "Global Day of Code Retreat"
date:   2015-11-17 21:00:00
categories: java code-retreat
---

### Global Day of Code Retreat
Last Saturday was [Global Day of Code Retreat][1] and thankfully [JUG Ostfalen][2] organized a Code Retreat and I took part in this awesome event and want to share my insights.

In day to day work there often is far less time to do things the way it should be done. This neither is satisfying nor pretty efficient in the long run. But still it happens that we are forced to just quickly get this done...

A Code Retreat basically is an event to practice software craftsmanship and can help in countering bad habits and fostering good programming practices, since the constraint is fine software craftsmanship. The group of developers participating in a Code Retreat forms pairs of two. The pair then solves a rather simple problem, classically [Conway's Game of Life][3]. The key is to do it over and over again. Every round is 45 minutes and you have a new partner each round, starting from scratch.

Since the problem is not overly complex, you can focus on the implementation, for instance with TDD. Moreover there is a lot to learn from your partner.

### What I learned

#### crystal clear, isn't it?

Even though the problem space is absolutely limited there are so many ways to look at it. This insight might superficially seem trivial but it 
actually is quite intriguing if you and your partner use the same words, look at the same IDE and still you wonder what he is actually doing. Even more so, if you express your doubt and your partner cannot understand why you can't precisely follow his thoughts - after all it is simple problem you are solving.

This absolutely fits my experience of everyday work. It is always worth to try to be as clear as you can. Do not get trapped in thinking it is totally evident what you mean or what you want to achieve.

#### IntelliJ Idea

I used this event to have a look at IntelliJ Idea since I have developed some dissatisfaction with eclipse. I shied from truly trying it for reasons I cannot precisely name. However, having the chance to be introduced to some basics of Idea by a fellow developer who knows both eclipse and Idea is amazing. That makes it so much easier, especially if you do not have to set it up yourself. I will definitely have a look at IntelliJ Idea. I was most impressed by how responsive it was and how well Maven and JUnit are integrated.

#### Knowing your IDE

There was this one session which enforced doing everything in baby steps. This means your group agrees on a fixed amount of time (we chose 3 minutes). For each step you plan on what you want to achieve, then set the timer and hack the hell out of your fingers. First test, then implementation. But you are only allowed to commit if all tests are green when the time is up, otherwise you have to revert and try something different. 

Luckily I did this session on a fellow developer's laptop with IntelliJ Idea installed. Although I really liked IntelliJ Idea, this session perfectly prove that know your IDE is crucial. I feel this is especially true for keyboard bindings, since moving the mouse is sooo slow. 

There have been some hints I could give my fellow developers for eclipse

* install mousefeed - it displays the keybindings for actions you invoke using the mouse
* *alt+shift + [cursor up / cursor down]* selects the next outer / inner block from you current cursor position or selection 
* *alt+shift + a* enables block selection and modification
* *strg + 3* lets you filter all available views and actions

I also learned that setting the cursor to a method parameter and invoking Quick Fix (*strg + 1*) you can easily assign this parameter to a new field, which will save me some in the in the future :)

A Code Retreat definitely is a good place to learn more about your IDE.

By the way, there is a good talk on [vJUG][4] I can recommend: [Effective IDE Usage][5]

#### Naming things, especially test methods

> There are only two hard things in Computer Science: cache invalidation and naming things.
> 
> -- Phil Karlton

When I wrote tests in my every day style, a discussion arose about following snippet

{% highlight java %}
public class TheClassTest {
	
	@Test
	public void testTheMethod() throws Exception {
		
	}
}
{% endhighlight %}

How verbose it is that a method's name within a test class, that also is annotated with @Test should start with test? Instead I was convinced that naming the tests with regard to the its intention is a little more useful. That way, if you have a list of failing tests, you might already get a clue what's wrong.

Moreover, we talked about how hard it is to name methods upfront, when there is not even a single line of code in it, be it a test method or an implementation method. Of course, you would neeeeeever do such a thing, but I liked the idea of initially naming a method like bla() or foo() and only decide on its name after it is implemented. This way it should be easier to choose a well suited name.

#### Test first makes a difference

I think automatic unit testing is important. A unit test can very well be written after the implementation under test. I used to do it that way and was totally fine with it. 

However, when writing the test first, things somehow change. Creating a method this way is more about what you put in as parameters and what you receive as a result, which makes methods more coherent. For example, when writing the test first, you would hardly produce a test that forces you to write an incoherent method, because the test enforcing this would have been way to complex in the first place.

I am going to incorporate test first more in my everyday work.

#### Getting in touch with Scala

Swapping the partners with each round of the Code Retreat also is an awesome way to get to know another programming language. In the last round we didn't solve Game of Life but instead agreed on writing an ISBN validator. I always wanted to have a look at Scala and luckily my last partner was really decent at Scala and was OK with a little introduction for me. There was not much I can really remember and we didn't finish our validator, but I am going to try it alone and see how it works out, because I liked the result.


### Conclusion

It was a great day with lots of fun, several insights and good food. Thanks to [Lineas][6] for sponsoring this event and to the organizers of JUG Ostfalen. I am looking forward for next year's Global Day of Code Retreat :)

[1]: http://globalday.coderetreat.org/
[2]: http://www.jug-ostfalen.de/
[3]: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
[4]: http://virtualjug.com/
[5]: https://www.youtube.com/watch?v=FwfHxholaEE&feature=youtu.be
[6]: http://www.lineas.de/