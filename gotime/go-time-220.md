**Mat Ryer:** Hello, and welcome to Go Time. I'm Mat Ryer. Today we're talking about logging; something we all do, no need to be embarrassed about it... But are we doing it right? Are we logging the right things, are we logging them to the right place? We're gonna find out today.

Joining me - Jon Calhoun, of course. Hello, Jon.

**Jon Calhoun:** Hey, Mat. How are?

**Mat Ryer:** Not bad, mate. Not bad. How has your week been so far?

**Jon Calhoun:** Pretty good so far.

**Mat Ryer:** Yeah, it's Tuesday, isn't it, so I shouldn't really ask that question. Silly. Never mind... I hope the rest of the week is as good as yesterday. We're also joined by Ed Welch, who is a swell fella who's been two kinds of engineer and two kinds of manager, and will do any job, as long as he's having fun. Hello, Ed. Welcome to Go Time.

**Ed Welch:** Hey, Mat. Thanks for having me.

**Mat Ryer:** Absolute pleasure. So yeah, logging. We all, I think, know what logging is, but let's just be clear - logging... What is it?

**Ed Welch:** The fun part about logging is it's probably the first thing that everybody does when they start writing software. Everybody's first intro into running a program is usually a Hello World, which is at least in my opinion a form of logging. So it's probably the most common way, the most ubiquitous way that we get state out of our application. Some kind of view into what it's doing, some kind of idea of what's happening. So at its most basic, it's just some indicator of what our application is even up to.

**Mat Ryer:** Yeah. Sometimes I'll have -- in the beginning, when I'm writing something, I'll have lots and lots of logging going on, because like you say, it's a great way to see what's going on and get some insights. It's like a really easy, simple way to do that. But usually, I'll go and kind of clear that all out and remove it all, because it stops being useful at some point. Is that right? Should I do that? Or should I leave it in there?

**Ed Welch:** \[04:14\] You're probably ahead of most. I think that largely people when they write a log statement, it's probably there forever. I'm not sure how often people really go back to scrutinize what they logged. So I would certainly think that you're gonna gain from that; having more valuable log messages and more relevant is useful, but I think that in terms of that quantity, as long as they contain useful context or information, you shouldn't really be looking to \[unintelligible 00:04:41.26\]

**Jon Calhoun:** I'm imagining some of your scenarios are writing logs to help debug stuff... And if you're anything like me, there's probably some time where you catch yourself doing a sanity check of like "We're in this function", just because you're like "Is the code actually getting to this point where it's supposed to get?" And I can imagine that those ones, over time, stop adding value. Once you have tests and everything, you know it's all working, it's kind of like "Is that running every single time that function runs really adding value?" Whereas other parts of it definitely could be.

**Mat Ryer:** Yeah, I tend to do that if I'm marking bits. I'll just put monkey, or like monkey2, monkey3. So that's probably why I go through and remove the log statements. But also, if I'm doing test-driven development, I will tend to log a bit less, really, unless there's a specific kind of tricky behavior, something that I don't understand. So I really do use it to sort of observe what's going on.

**Ed Welch:** I inherited a codebase a few years back, where --

**Mat Ryer:** Congratulations. From an old uncle?

**Ed Welch:** Yeah, it was really -- \[laughs\]

**Mat Ryer:** An old uncle died? "I'll leave this codebase to Ed..."

**Ed Welch:** Yeah, let's go with that. So my uncle left me this sweet codebase, and he was very -- I don't know, thorough and consistent... Every function call had a log line at the beginning that would say the name of the function, and the entry, and then the values that were passed in, and then an exit. And to be honest, I've never done that; I've never gone and built software that has that level of verbosity, even if they were at debug level. There were times it was extremely useful, especially as an inheritance, and his untimely passing, and that I wasn't able to ask him questions about those log lines... So it did make understanding the application a little bit better.

So I think my learning from that had more to do with "Maybe don't log monkey1 and monkey2", instead log something like "Here's where I am and here's the state at the time. Here's some values." Because that particularly ended up being useful, trying to understand why something was or wasn't working. It's like, "It got to here, and things look correct."

There's a lot of tools that exist now that maybe make that easier, like debuggers and tracing, and even in your tests... But there's certainly an element of logs are generally always available, and so having some (at least) checkpoints through a process that give you insight into that process.

**Mat Ryer:** Yeah, one trick that reminds me of, that does work for me - if I've got a program where there's quite a bit of logging going on, I'll do a thing where I log out a load of hyphens, just some dashes, and then in Go defer immediately the same thing... And that's a neat way of wrapping up really the in and out of a function, just so you can then see -- you know that the logs between those lines are relevant just to that function.

That works if you've just got one instance of the program running, which you normally do in dev... But it doesn't work if you've got multiple instances running, really, does it?

**Ed Welch:** \[07:45\] I think it becomes -- one of the things that I'm gonna give people advice on logging is that the more context you can put in the log line, the more useful it is. Whether or not you have access to the hostname, and if your logging application or frameworks or systems introduce that at another level, but a log line without context isn't really very useful. It doesn't give you -- I would go a little bit farther than that; not only you wanna know the machine and the distributed system that it's executing on, but you need to know the order number, or the user, or the trace ID, or something that lets you trace that through \[unintelligible 00:08:19.23\] of the application. Typically, it's hard to coordinate events over big systems, especially if it's different, disparate database systems and things that all log differently with different formats... So put a lot of context into your log lines, or remove them if they don't have any, because the usefulness of them -- especially, usually, what happens is you go search for that contextual information; you go search for the trace ID, or the order ID, or the user ID, and the results you get back are only gonna be shown to have that info in it. You're likely never gonna see the log lines that don't have that in them.

**Mat Ryer:** Then I wonder, should you standardize the format then, if you're gonna be using these logs in this way? Because obviously, it's just a string, isn't it? You can just print out anything you like from your program. Should we be strict about what format we're printing in?

**Ed Welch:** Probably... It's kind of a hard problem, whether or not you have control over the logs from some of your applications or not... If you're building your own app, you're writing your own logs, having some consistency is always helpful.

One of the things that I think are interesting about log lines is the battle between the humans and the machines. We're talking about humans looking at log lines, but it's not uncommon now that log lines go into analytics, and other systems, security... So having the format be structured becomes really important for how easy it is for a machine to parse those log lines.

Arguably, having a visual format that's easy to parse for humans is true, too. Commonly, I would say JSON is maybe what you would find the most... It's probably not my first pick for a structured format, because I think it's harder for humans to read; it's very easy for machines to read. And specifically, JSON becomes very hard to read if you have complicated, nested objects, or you build large JSON docs.

So one of the things when you're typically viewing a log line is it will exist on horizontal space on your screen, and then vertical space as your number of log. So if you try to pretty-print a JSON object in order to be able to view it, you then turn one log line into line tens, or hundreds, so now you've optimized very poorly for being able to look at those logs quickly.

There's an alternative that I like a lot, that's common in Go probably more than I've seen in other langauges, logfmt. So having key-value pairs that are separated by a space, basically, log format, if you're not Mat?

**Mat Ryer:** Yeah.

**Ed Welch:** I know how much you like fmt...

**Mat Ryer:** I did like it. I wasn't gonna comment, but yeah. I'm pleased you said it. Fmt. Logfmt.

**Ed Welch:** So it's kind of an interesting compromise on machine parsability and human readability. But ultimately, having structure does become really important, because almost guaranteed you're gonna need some tool to help you parse those logs, or strictly, you're gonna parse them with a machine for other purposes.

**Mat Ryer:** Yeah. See, JSON would also probably encourage you to put more complex objects in, whereas with logfmt - we're talking key-value pairs there.

**Ed Welch:** Yeah. Which is a nice thing too, so I generally tell people -- I don't have a problem with JSON logging; it's in fact the easiest and most approachable for most logging frameworks. I would highly recommend keeping a flat structure as a practice though, right? Just do key-value pairs in JSON.

**Jon Calhoun:** I think part of it definitely comes down to how you're logging affects how you're gonna consume it... Because I know the first time I ever used JSON logging, it was game-changing in the sense of like "Oh, I can filter on these things, and it'll make my life a lot easier." But you're right, the first time you see it in just like plain text, you're like "Oh gosh, I can't read this anymore." But depending on the tools, you have certain tools that will help alleviate that pain, so you can start filtering things and all of a sudden it will make things pretty for you... And if you have the right tooling to do that - there are services out there that do it, and different options - then it's kind of okay... But I get what you're saying with the key-value pair; it's kind of like a nice middle ground, where you can still read it, but it's still gonna have that machine readability.

**Ed Welch:** \[12:17\] Yeah. The other reason to keep the object flat -- because you're right, the tooling that exists out there usually facilitates this, but it also introduces in a lot of cases another query language. So to manipulate JSON documents, you usually need to use -- I like JQ, JMESPath, or some other query language that you have to figure out how to access elements of the JSON object to return them... So there would be, I'm sure, a reasonably good debate about what makes a good log line, versus what's information that should go in a database. If you're generating huge documents with hundreds of lines and values... And I see this in even some pretty famous places; a lot of the logs that we get out of Google are massive JSON documents, with massive nested elements in them... And I find that they're hard to work with, both as humans, and for parsing for machines.

I think the more you can keep that structure flat, I think the happier you would generally be... And if you need a complex nested object, is the log line the right place for storing that information? Maybe a sniff test... We won't go as far as sanity test yet.

**Mat Ryer:** No.

**Jon Calhoun:** Thinking about it that way - you said some things are better fit for a database... Do you consider things written in a database to potentially be logs? An example I can give is I was building a web server once, and I forget why, but for some reason we wanted a way to actually record web requests, and the response to them, to make sure things were going correctly and to -- there was some other reason; I don't remember why off the top of my head. But I know that one of the things we did is we were like "Well, you can't really take an entire web request and just throw it into the output. It's gonna be really hard to do anything with it." So we took the parts we cared about, and we were storing that in an actual database table, and we would essentially clean that up every so often; luckily, it wasn't a huge project at the time, so we could get away with it. I imagine at Google scale that might get a little bit tricky... But for us, that worked. And I consider that logging, but with the caveat that we're storing this in a database and it's kind of a little bit different.

**Mat Ryer:** Yeah, but that's kind of structured logging, isn't it?

**Jon Calhoun:** It had that feel to me of like - you entered a function, and then when you exit the function, you're kind of like "These are the things that happened." And that's roughly what we were doing; we were like "We need to store this somewhere where we can query it and actually look at the data and try to do stuff with it."

**Ed Welch:** It's really fascinating in the sense that it touches a little bit on the types of logging that we see, too. That falls a little bit into the category that are more like event logs, or access logs, or they're very specific things that happen, that have useful contexts. I guess the question there that I find interesting is "Is there a right amount, or too much data?" From an implementation standpoint, the tools that you have available and the tools that you're using maybe dictate what's a better fit there...

The other types of logging that we kind of started talking about are more like what people introduce when they're writing code. So this is the stuff that helps me understand if my application is working, or where it's working, or how it's working... Maybe even just the sort of overall lifecycle of the program itself in logs... But you think about access logs from reverse proxy, or you're describing orders and events where you have complex information coming in... Yeah, I think that's a really good question about where you would draw the line on, and what systems it makes sense for.

The only advice that I could give around this, in some experiences that I've had, is don't make your primary logging in terms of what gives you visibility into your application stored in the same thing that you need to run -- so like don't store those logs in the database, because what happens is when you have trouble with your database, you can't view your logs and you can't see what the trouble is.

**Mat Ryer:** \[15:59\] Or if your database -- if there's an error, then it logs that there's an error, but it can't log to the database, so that's an error... \[laughs\]

**Ed Welch:** Yeah. It's very useful. I've seen in older software systems that I've worked with before that systems that exist today for aggregating working with logs - it was pretty common to store logs in the database, but then when you had trouble with the database, which was usually the thing you had the most trouble with, you couldn't tell. You couldn't see what was going on.

**Jon Calhoun:** Yeah. A lot of that probably comes down to like the lifecycle of the product, too... Where I tend to see that type of approach being - not okay, but kind of okay, is if you have like a two-person startup and they're just trying to slap something together and get it up and going, then you kind of cut some corners here and there. You can't have six different services up and running, because it's just hard to manage that. But as something gets much bigger and grows, then it's like "Okay, now it's time to actually look at like we need a dedicated place to put these logs and actually consume them."

**Mat Ryer:** Yeah, on  that question of consuming the logs then, Erik from the Gophers Slack - and by the way, everybody, don't forget, we're on Slack. You can join the chat live, GoTimeFM channel. Erik has done just that. Erik asks "Will the key-value pairs always be in the same order with logfmt?" Because I imagine if you visualize and you're looking at the logs, it would be quite useful if they were lined up.

**Ed Welch:** It's gonna be subject to whatever library implements that. In most of the applications that I work with, we use GoKit, and they're always consistently in order of the way that they're written. Like I said, the libraries that implement logfmt - JSON is more common, but I think there's support in others as well. I can't speak to their consistency and ordering, but I think that would be a very valuable -- like, humans are really, really good at pattern recognition. So if you can get data in front of us in a way, even in log lines -- I can tell a funny story here... I had a job where I worked with computers that would run vehicles, and we would have trouble where sometimes they would boot and behave differently... And I found myself -- we were shipping the syslogs from those machines back to the central place, and I would review the syslogs to try to find instances of the computers that misbehaved... And the fastest way that I could do that was I could zoom way, way out, so that the text wasn't legible, but I could look at the length of the log lines and you could see the pattern, and you could scroll through the log lines very consistently, because it was really easy to spot the ones that were all the same. And then you can add a little bit of color to that too, and it made it even easier. But it became very easy to see the ones that didn't act properly.

So having consistency in your log output is a huge value for a human to parse. Machines tend to not care. Maybe there's some efficiency gains if they're consistent, but it would be a good feature to have, that's consistently orienting your -- and I think probably most JSON serializers are gonna be consistent. I think most serializers in general are probably gonna be consistent. It's just a question of whether they guarantee that over dates, and things.

**Mat Ryer:** I imagine if you're passing in key-value pairs, it probably logs out in the order that you do it, and therefore it's up to you to make sure you're consistent in your code to get it right. And that thing of context is interesting... I have a project where we actually use the context to carry -- like, Go's context.context to carry some... Context. You guessed it. And that gets passed into the logger; when you call a log line, the first argument is a context. How do you feel about that, Ed? Because it's very useful for -- you talk about some of the things of like user ID maybe, and the hostname, and things like that. It's the kind of thing that you could do in some middleware somewhere, or do it in one place, and then it is consistent.

**Ed Welch:** \[19:47\] Yeah, definitely. The applications that I work with do this pretty commonly. Trace ID is often propagated through the context; or is always propagated through the context. Something like a tenant ID in a multi-tenant system... And there's helper functions that we have that will pull that out to -- I don't remember if we implemented the interface for GoKit to just sort of make a logger that automatically did that, or if you just have the functions that pull them out, but it's really useful.

I wish Go went one step farther here... One of my griefs with Go is the context deadline exceeded, and context canceled errors, because in a distributed system you get a context deadline exceeded and I don't know who canceled that context, or what deadline rather exceeded or timed out... It could have been four systems away. So it would be up to you to introduce context into the context (sorry), that when you're the one that times out, you'd have to say "Hey, it's --" It doesn't make it easy. It would be nice if the functions that you called with timeout, or with whatever, could take a string that would just print in whatever error block ultimately catches that context being canceled, so that you could find it easier.

**Mat Ryer:** Yeah, that's a nice one. You could write a package that did that, couldn't you?

**Ed Welch:** I thought about this last night... \[laughs\] I'm not sure what it would look like to -- I mean, you could do definitely something that catches it; where it's tricky is how easy and how organic would it be to use... Because a lot of times you're catching an error on a function, and you have no idea what the error was. The error might have been that the context was canceled while you were executing that function, and you're just logging the error. You're basically now thinking about wrapping all of your errors with this thing that would look for someone that's better at this than I am.

As much as I love how well that context cancelation allows you to control over network connections and things, I find that it leaves a lot of times confusion around what piece of the puzzle was the one that actually said \[unintelligible 00:21:57.15\]

**Mat Ryer:** Yeah, because in an HTTP world if you are getting a request and the browser -- like, someone just closes the browser, that'll cancel it. That'll cancel the request. And in your own code, you might be canceling context and relying on that, and then you can't tell the difference.

**Ed Welch:** Yeah. My favorite is -- so like NGINX, or anybody that sits in the middle, that times out, will result in a context canceled error. I mean, in my argument here that would be not the most helpful, but somebody should know that it was their context that was canceled, and say "Hey, it's me over here. I don't know who did it, but somebody closed this connection."

**Mat Ryer:** I wonder if you could pass a string into the cancel function... Because you get back -- when you do with the timeout, or the other one, with cancel, you get back the little cancel callback function thing. Could you pass in a string there, I wonder...? Actually, that's an interesting problem. It'd be quite fun to explore that, but I don't think we're going to.

**Ed Welch:** I think the idea of how well and how useful the context is, but it does often lead -- I know when I first started writing Go, I found that I was doing a thing that was very Java-esque, because I really missed checked exceptions and I was trying to understand why an error... So I'm looking at the type of an error, and looking to see that it was deadline exceeded, and then I logged a message that said "Timed out", because everywhere else in the rest of the world it's timed out, and in Go it's deadline exceeded... The error handling in Go makes this a bit bumpy; I wish it was a little bit easier to -- we're way out into the weeds; I'm just gonna shut up right now... \[laughs\] Before I start passing opinions. I've gotta save my unpopular opinions for the end.

**Mat Ryer:** Yeah, you've gotta save it for the theme tune. They don't count unless it's been played.

**Break:** \[23:43\]

**Mat Ryer:** I usually end up having dedicated code for context errors at the top of -- wherever they unwind to, I'll have some code, because I probably want to do something different. And the other thing is - it's very normal to have context that gets canceled. It's a sentinel error, term coined by our friend and friend of the show, Dave Cheney. It's a sentinel error value type that you can compare against and you can pass around. But it's not really an error. Not necessarily. And if someone closes the browser and cancels a request, you want to stop doing the work; that's not an error, that's great. It should return "That's great", or something instead, that's great.

**Ed Welch:** I would support that.

**Mat Ryer:** Yeah. I'll do a PR.

**Ed Welch:** It's something to think about for folks that are writing applications and they're using contexts - and they should be; there's the context -- you can add values to it, so take advantage of that. Like, most things probably don't get carried away, keep the context simple, but you could consider... I don't know off my head, like I said, how ergonomic you could make this, but trying to include information about parent contexts and ultimately who cancels it, be able to log something to make your life a little bit easier, to track down where in a complex system something took too long.

**Mat Ryer:** Yeah. Another interesting little thing that's quite nice to do in Go that I did once was when adding final use to the log, using those helpers that you talk about, that these packages have - that would also return a little cancel function, which you defer immediately, remove that value, essentially... Which depending on where you're passing things around and how you pass things too, you may not need that. But it's quite nice to be able to use Go's actual language features when it comes to doing this and solving these problems. Logging then in a way becomes really a first-class concern of your application, doesn't it?

**Ed Welch:** Yeah, I'm a little bit biased when it comes to the way we observe applications. I spend all of my time working on a system that's designed around handling logs, so...

**Mat Ryer:** That's why you're here though, Ed.

**Ed Welch:** \[27:49\] It's true, yeah. The nice thing about logging is it's kind of always there. It's the one most accessible way that we have to get info out of an application. The other forms that we have of observability tend to lend themselves to better use cases or different options... Distributed tracing can do a lot of stuff that's hard to do with a logger. I could make an argument, but I would lose that argument... But it's there, it's always there to use. So to use it, just make sure you have useful information in your log lines. Do yourself a favor and make sure you can search for that log line when it's printed. So it has to have something in there you'd be able to search for.

And there's another one, I think, when it comes to error messages. A lot of times, depending on how you structure your logging and whether your logger is gonna include the line number something comes from - I don't have strong opinions if you need to do that or not. Honestly, I would say just make sure you write error messages that are unique within your app. If you search for that error message, it should go to one place.

**Mat Ryer:** Yeah. Like monkey1, monkey2.

**Ed Welch:** Exactly, yeah. So maybe you were really onto something there... \[laughs\]

**Mat Ryer:** Well, that's why I use different numbers.

**Ed Welch:** Yeah. How do you keep track of how many monkeys there are though?

**Mat Ryer:** Yeah, that is difficult. To be fair, it gets way out of hand very quickly.

**Ed Welch:** Do you have like a global monkey tracker that you...?

**Mat Ryer:** You do need to implement that monkey tracking, yeah. And I did once mess up a loop and ended up with infinite monkeys... And they produced no works of any kind of discernable literature whatsoever, so - very disappointed there.

**Ed Welch:** Yeah. One way with error messages that you can accomplish this uniqueness is the idea that you could consider error messages part of the runbooks for operating your system. So it's one thing to say something timed out, it's another to say that it timed out but that it's gonna retry; it's gonna retry ten times more, or it's not gonna retry, and somebody's gotta go fix it.

It's funny, when you're writing applications and you get to where you're logging an error message, you likely have the most context of anybody that's gonna see that error message in years to come. So what can you put in there that helps someone make a decision about what they should do when they're operating that software. So they got paged, and they searched the logs, and they found your error messages; how can you help them get out of that trouble? Or let them know what the system is gonna do to recover from it itself... Because that is fairly common - there's lots of error messages about connections timing out, or failing, and is there a way to communicate that the app can still handle it, go and fix the source of the problem.

**Mat Ryer:** I kind of love this about the UX of logs, is what we're really doing... And honestly, anytime I see a sophisticated approach in any field, they reach that sophistication by caring about the audience or the user of that thing. And it works for web UIs and APIs that you write in Go, and packages, programs... It works for everything. Writing... So I like the idea; I think that's quite an interesting change, and I'm not sure everyone -- because normally, I think people are quite selfish loggers. They're logging stuff they need. But I like that; think about your audience and log for them. Log for your great nephew.

**Ed Welch:** One other place that's been useful is propagated all the way back through your API. We have limits in our application that you can hit for various things. So we will log something like "You've exceeded this limit. Contact your administrator, or reach out to whoever administrates that." Or try again, or reduce your requests, or fix your error... Not uncommon in 400 type errors and user errors to give some insight into what people should do, but the more you can describe how to get out of the error situation in the error message, the more self-serving and self-documenting and probably less trouble tickets you're gonna have.

**Mat Ryer:** Hm. Would you link to like a runbook, or link to a doc even?

**Ed Welch:** \[31:51\] I don't know, because my experience with that is that whatever you link to ultimately will break, and no one will ever go back and repair that link. It's very hard to maintain... If you can link it back to the source code in a pretty stable way... And can you have a linter or something that finds broken links? I don't know, because it's most likely just gonna be broken.

**Mat Ryer:** Good point. Yeah, keep itself contained; it's probably good advice. I once wrote a package for a co-worker. He was gonna then implement it. So we kind of broke the work up like that. And I customized all the errors, all the error messages just for him. So I was saying "Come on, mate... This is obviously. Obviously, you're not supposed to pass that value in." I was trolling him in the error messages. That was fun, but not for him.

**Ed Welch:** \[laughs\] I've logged messages before that are along the lines of like "This shouldn't happen..." I wrote those log lines, which is -- my favorite was in Java, an exception handling of - you would catch an exception and then catch an exception within the exception, and in a lot of those cases I don't even know, but it's important to do this, because somebody somewhere said I should do this, so... Even that is useful though, right? Anything that explains your mindset around why you wrote the error message or what you should do can be useful to the person that's troubleshooting that problem. So maybe have a little fun with it.

Another consideration here would be - what would maybe make this easier if you pull your error strings into a central spot. If you're doing internationalization of your application, this can be helpful. There was some tooling that made that a little bit easier to swap filenames and you could just have your error messages be in multiple languages, and make it easy to swap them out at compile time or runtime for the configuration flag. But putting them out inside where you can see them both lets you kind of easily review them to see if they're providing useful info, as well as -- and I'll say this is good advice that I don't use myself.

**Mat Ryer:** \[laughs\] Yeah. Well, on context then - maybe we could talk about log levels, because this is something else that is quite divisive subject I've seen around. This is where you can -- each line has a level, so it's usually debug, info, warning, error, critical... There's loads of options. I'm sure you've come across even more. But I find error and debug information to be helpful, warnings sometimes... But how do you feel about log levels?

**Ed Welch:** So I have some opinions here...

**Mat Ryer:** Are they popular or unpopular?

**Ed Welch:** These are probably popular, but...

**Mat Ryer:** Okay, I'll leave the music.

**Ed Welch:** Not everyone will agree, but errors is the easiest one for me. So I can start at error and warn, and then it gets kind of murky from there. But the error levels - I like to log at an error level if there's something wrong with my application. It's my job, it's my responsibility to fix it.

**Mat Ryer:** It makes sense.

**Ed Welch:** I like to use warning level when it's not my responsibility or my job to fix it. So I'm getting bad data... Some other downstream system is misbehaving, something that is useful for me to go find, but it's gonna be more useful probably for somebody else than me. That's my opinion on that.

Now, when you go beyond that -- so debug is really fascinating, because it ends up just being sort of the dumpster of log levels; everything ends up in there, and it usually ends up being so verbose that people don't run with it. And if you're not running with it - and most applications in my experiences don't have an easy way to do basically runtime changing of the log level output... It's like, what good is it, right?

So they can see the argument to seeing that you just log everything at sort of info, and that would force you to go back through and remove log lines that aren't as useful... But you do have these cases where -- I don't know, you write these log lines and instrument something in a way that's really interesting for specific cases, and you think "I could see myself hitting this again. What do I do with this?" I would say that my experience with Go loggers is they lack some of the functionality I've seen in others where you can do like for package level, different levels, or runtime reloading... And maybe those packages are out there and I've just not used them, but those can be really nice to handle that. Or trace level is maybe what you would do there, or something that's really specific, but only would want to enable in a very small case if you have that control available.

**Jon Calhoun:** \[36:20\] One of the people in Slack had mentioned - I think it was Gabriel - that he actually uses separate packages for developer type logging versus production style logging, just to keep the two separate... And I think one of the main benefits there is that when you go to production, you can basically set everything that's a development style logging to just be a no-op, essentially, and the code's there... Because I think that's one of the things -- like you said, we've all written Hello World, but there's kind of like two mentalities with logging. There's the logs that are just for me right now while I'm writing this code or debugging some issue or whatever I'm doing, and then there's the logs that have a lot more context and need to have trace IDs, and things like that. Because if you're running locally, chances are you probably don't need trace IDs. Generally speaking, you don't have enough requests coming in locally if that type of stuff is an issue. So it is interesting to think of it as like two separate approaches to something similar.

**Mat Ryer:** Yeah. I've seen it where you'll have like a logger type that supports nil, because in Go, of course, you can write methods that are safe to call on nil; you literally just check the receiver, and if it's nil, it just usually is returned at that point. That's quite nice... But I've also seen it where you explicitly have nil or no-op loggers, or whatever. Does this all just depend on which package you're using, or are there good practices there?

**Ed Welch:** I don't know that I have a good practices suggestion for that. My personal opinion would be to lean towards just the always log this stuff. Basically, turn your debug logging level on and -- actually, I've gotta decide if that's really my opinion... Because it is nuanced, right? There are situations where the volume can be tremendous, and you mostly don't care. But you do care. So that's where I think -- in my opinion, the state of the art of logging needs to be better at this, which is either your application or your logging framework or something that gives you the ability to control at runtime what ends up being stored.

I know more times than not you don't have debug logging on. There's some information that you want, but you can't get it because the application is running, and likely restarting it resets the error condition, trying to troubleshoot. So, it would be better if you could leave debug logging on, and then just drop it at the source until you need it... Or have runtime log-level changing as an option. It gets a bit more sophisticated. I don't know that I see this a ton; at least in Go I'm not seeing it a lot where you have that runtime capability. Somebody will probably point me -- which would be great, because there's an element of Go and logging that I've always found a little bit frustrating, which is the lack of a standardized interface and the fact that your logger becomes very important to your system, and therefore very hard to change... So we've talked about changing our logger in our app, but it's an effort, it's an opportunity cost; we'd have to be really sure that we need to do it. It works fine, but there are loggers out there now that have a much lower allocation rate, which can really matter a lot too if you're gonna log a lot. If you're gonna log thousands of line a second, you're paying for that in allocations in CPU.

**Mat Ryer:** It's not free, is it?

**Ed Welch:** More interestingly, you're potentially bottlenecking your application, because almost every infrastructure that I see still logs to disk. Usually, like say in Kubernetes, it's the node disk, and node disks aren't terribly fast. So you're more likely than not at some point blocked on your logger, trying to write to a file on disk, which can slow your application down.

**Break:** \[40:00\]

**Mat Ryer:** Bill Kennedy, the famous hatted gopher actually showed me an example once where there was a bug in the program and by putting a log line in, because there was a cost to doing that, it changed the behavior of the program. So that act of observing it almost affected it, which is kind of amazing.

**Ed Welch:** Right. There's some Heisenberg in here, or something... \[laughter\]

**Mat Ryer:** Yeah... It feels like that, doesn't it? Imagine if you didn't realize that though, and you're logging out, and then it's telling you something that is different to what you see when you then take the log line out. How infuriating is that...?

**Jon Calhoun:** I think that's the risk of using any language where you have concurrency and other things like that going on, is that it's just -- you always have to take for account that if you're using concurrency, there could be some case where adding anything in there could change the behavior.

**Mat Ryer:** While I've got you, Jon - it's quiz time! We'll put some music in. I've just made up this segment.

**Jon Calhoun:** Oh, boy...

**Jingle:** \[42:40\] to \[42:58\]

**Mat Ryer:** Jon. Jon Calhoun is joining me from - where are you from, Jon?

**Jon Calhoun:** Pennsylvania.

**Mat Ryer:** Pennsylvania.

**Jon Calhoun:** Can't get more specific than that with you. I don't know what you'll do with that information.

**Mat Ryer:** Turn up at your house, I might...

**Jon Calhoun:** It's possible.

**Mat Ryer:** Yeah, I do want to know your full address now...

**Jon Calhoun:** Well...

**Mat Ryer:** No?

**Jon Calhoun:** Nah.

**Mat Ryer:** Okay.

**Ed Welch:** Was that the quiz?

**Mat Ryer:** Yeah. What's your mother's maiden name, Jon? No, the real quiz is - fmt.println and log.println, where does each of those get printed to?

**Jon Calhoun:** Where does each get printed to?

**Mat Ryer:** Yeah.

**Jon Calhoun:** I assume fmt is os, so that's standard out... Or sorry, the fmt one. The log.println - I don't know if it's actually differentiated or not. I would assume it's just os.stdout. That would be my guess.

**Mat Ryer:** Okay. Let's find out. \[sound effect 00:43:49.18\] That sound effect tells us that - no, Jon, unfortunately you're wrong. Log goes to standard error by default. So that's interesting...

**Jon Calhoun:** See, I wasn't sure if this was a trick question or not. That's the hard part. With Mat you can never tell.

**Mat Ryer:** \[laughs\] Yeah, but that's been an odd quiz...

**Jon Calhoun:** The worst part is in most terminals you can never tell the difference.

**Mat Ryer:** Yeah, that's true.

**Jon Calhoun:** Unless you're doing something specific, like piping it to an output file, or something.

**Mat Ryer:** \[44:13\] This is the question then - where should the logs go to? Do we want them in stdout? Should we put only the error level logs in stderr, and the other logs in stdout? What do we think?

**Jon Calhoun:** I think people have a lot of weird opinions on logging in general. I swear I've talked to somebody who said "If you're printing out a log statement instead of recording a metric, that it should be an error that some engineer has to go fix."

**Mat Ryer:** Right.

**Jon Calhoun:** To the point where their app apparently has no logs, it's just all metrics. And I think sometimes how people think about logs vs. metrics - they might kind of be the same thing, they're just differentiating them differently, if that makes sense...

**Ed Welch:** I could give an opinion that I think is a little bit researched on that...

**Mat Ryer:** Ed, before you do, we should just be honest with our listeners here... You kind of built Loki, which is Grafana's logging thing. Tell us about Loki just very briefly. Because there's some important context, I think, there.

**Ed Welch:** I've certainly been a part of Loki. Lots of other smarter folks than me have done the lion's share of the building.

**Mat Ryer:** It's not what I've heard... I think you're just being modest.

**Ed Welch:** Actually, it's been about three years that I've been working on Loki now, so I'm kind of trying to think of how I've evolved describing it over time... This may dovetail a little bit into what Jon was saying, but... Loki I tend to describe now as the time series database for string. So I guess before I talk about Loki, the part about the metrics versus logs that I think is interesting is the information that they can contain and store - there's an associated cost with that.

One of the things when I talk about Loki as the time series database for strings is... When we talked about that context you can put in a log line - you can put a lot of information in the log line, right? Within Loki we log for every query, the results of that query and things like throughput and the lines process per second. And the query, and the org ID, or the tenant ID, and all this information... And it tends to be extremely high cardinality. So where metrics systems - it gets a bit more costly when you're trying to store huge amounts of cardinality. If I had a metric for every one of those things I've just said that had a tenant dimension on it, it would be a lot.

The other side of that coin though is that's a long string, and to process that in a system requires parsing it, and doing math on it that's more expensive, it's more data stored on a disk. So to me, the two play very nicely together in the sense of metrics make a really nice roundoup or sort of overall approximation of high volumes of that kind of data. So you can look at metrics, because it's a float, over much longer periods of time than it's gonna take to parse a 80-byte or 200-byte string.

Where those become a bit interesting is -- so with Loki we have a thing for recording rules where I can generate a metric from those log lines as they come in. So what we'll tend to do is generate roll-ups. So we'll generate a 99% quintile on the query times by tenant. So now maybe I have cardinality of thousands of tenants, but the 99th percentile is taking all of those individual values and rolling them up into one metric.

So the systems (I think) complement each other really well when you set them up. You can have very high resolution, very high cardinality data, it's just gonna be a lot more expensive to try to query it over time and put those in your logs, and then you can roll that stuff up. Or create metrics, just use the metrics you have in your system for higher volume requests, being able to keep an eye on a system that's doing thousands of requests per second a lot easier, showing that data over time a lot easier... So I think they play nicely with each other. I think maybe both extremes -- I guess I could argue that you could monitor an app only with... Well, you can make that argument either direction.

\[47:58\] To be honest, once your apps start getting big enough, you probably want specialized tooling for those types of aspects, metrics, logs... And traces, for big distributed systems, being able to view how your information propagates through - a lot easier in a tracing system than it tends to be in searching for a trace ID amongst all your logs, and looking at how each of those logs were formatted differently, and the timestamps between them, and trying to kind of reconstruct that same view.

**Mat Ryer:** Yeah, we had an interesting question in the Slack channel... Erik is back, and Erik says "What about logs and testing? Do you ever include assertions about should be logged out?" Have you ever done anything like that?

**Jon Calhoun:** Jumping back to your previous question, can we say that when you're logging and testing them, maybe you should write `os.standard` there. I'm just kidding, but... Just split it up that way. We never did answer that question, if you wanna go back to it later.

**Mat Ryer:** Oh, yeah.

**Jon Calhoun:** But Ed, logging and testing.

**Ed Welch:** I don't know, I mean... It happens from time to time. It always feels a little weird when you're validating a log line, I guess. I'm trying to think of an application where I've seen that done. Usually, it's in catching like an error message or something, like a sort of specific error you would catch, which becomes a log line...

**Mat Ryer:** Yeah, I've done it once where it mattered what was printed out from the program; it was very important. And so, this is -- Because I always do this little abstraction where I have a run method, and main only just calls out to a run method, and passes the dependencies in. And I include stdout and stderr if I wanna use them; I include even those as io writers. So in test code then you can use buffers, or whatever else you want, to capture it. And that is a nice way to -- then you can make assertions about what is printed out. I don't know if it was logging out; it mattered what was printed out in that case. But yeah, that was just stdout. So we never did \[unintelligible 00:49:55.25\]

**Ed Welch:** It could be interesting if you have downstream processing under your log lines. It is interesting, because you build dependencies, and now you're logging - you're talking about it being kind of an API of your application, and... Maybe API is not the right term, but it's an interface that...

**Mat Ryer:** It kind of is.

**Ed Welch:** ...goes out to. So you could do testing on it to make sure you're not making breaking changes to the downstream systems. It's interesting if you find yourself in that world where you have those tight couplings. But if you're using JSON or even logfmt, you could have tests to make sure that certain values are there... I'm not sure quite how to dig into that one.

**Jon Calhoun:** It seems like it comes down to the point where if not having it there and it changing could break something, then you probably need to test it.

**Mat Ryer:** Yeah, if it's part of your API...

**Jon Calhoun:** But if it's not something that's gonna be broken and it's just gonna be like a visual difference, then you're probably okay getting by without it.

**Mat Ryer:** Yeah, by the way - Jon, you won't believe this, but the little elves in the Slack channel have found the episode that Scott Mansfield talked about Go at Netflix on Go Time episode \#9.

**Jon Calhoun:** Was it \#9?

**Mat Ryer:** So if anyone wants to go back...

**Jon Calhoun:** I swear there was one that I was in as well that got mentioned... So maybe it's been in multiple.

**Mat Ryer:** It's possible.

**Jon Calhoun:** I swear it was one of the episodes we did on metrics and things like that with a company that did metrics software...

**Mat Ryer:** Yeah.

**Jon Calhoun:** But I swear they had like a similar opinion, along the lines of like "If there's a log message, somebody should be getting paged right now." So it's just interesting to see the different approaches that people take to writing software.

**Mat Ryer:** Yeah, and that's what I wonder also, is whether this sort of depends in each case. Anytime you have people with really strong opinions about tech, often they're working on something different. That's why I try to be -- you know, unpopular opinions loosely held.

**Ed Welch:** \[51:49\] One area that I think could matter in that argument of metrics versus logs is that logs aren't events; they have a distinct timestamp that's very accurate. Metrics do not have that kind of accuracy, at least most of the time. You're sort of forced to do a roll-up, because that's the optimization they make, which is really valuable... So maybe that event timestamp isn't -- I don't know, it can be really important for troubleshooting to know exactly when the sequence of events happened, and a lot of times when you're working within whatever the bound of your metric is, if the event happened inside, say, a Prometheus scrape interval, you don't have any information to tell you what happened, and a lot of times logs can hide or show that context. Without knowing more context -- I'm sure it's a well thought out argument, in that it works well, you can build a system that works well, that doesn't log... But logging is just a sort of -- it's the thing, it's everywhere.

We were gonna talk a little bit more about Loki - I think the reason Loki exists is because of how ubiquitous logging and some of the problems are with it. We talk about the variety of formats that exist for logs. You know, do you pre-process or post-process those to try to make them useful? How do you catalog and store and index that information? Where do you store it? People generate tremendous amounts of log data. The numbers vary; the big side is usually terabytes a day, although definitely no people say they generate petabytes of logs a day, which is just mind-blowing amount of data to have to deal with and store.

So when it comes to logging, that's the real question of "Where does it go?" You had that question in there, "Where did the logs go?" \[laughs\] It's a good question, because that's usually a disconnect between the person that writes code and the system and infrastructure that runs it. So it becomes a little bit more important than what you log.

**Mat Ryer:** I can't imagine petabytes of logs... I mean, knip through and delete some log lines, surely.

**Jon Calhoun:** I can't imagine how you'd use those at all. I feel like you just end up with so many -- either that, or you're like a crazy busy service, or something... And then it's like, we need to do something different.

**Mat Ryer:** Or you use a technology that then works in some abstract way on that data. That must be the answer. I'm not gonna keep promoting Loki; other log platforms are available...

**Ed Welch:** So the way you could approach that that would fit with Loki is -- what Loki does is a little different from other systems is the indexing model. So the idea is to index a relatively small -- we don't index the content of the logs themselves, we index metadata; think about where the logs come from, typically.

You were gonna ask the question of like where would I go to find the logs... Maybe like a table of contents a little bit... You keep a relatively small index because an index in a database tends to be one of the more expensive operational pieces. So you keep a small index. So for that petabytes, you would chunk that up into smaller and smaller pieces, and then Loki sort of optimizes around that scale side of it first, or the idea that your expensive parts tend to be on ingesting and storing that index, and keeping that cost lower, or that complexity lower. And then on the read side of things, it's a little bit more simplistic; it's just kind of brute force. So if you don't fully index something, you don't know where it is, you've gotta go look through it.

So you use those labels to narrow down that result set. But one of the things that computing is generally really good at now is parallelism. Loki optimizes around object stores, so it's basically a strings database that's built against object stores, which also support parallelization really well, and take a lot of the nightmare of working with storage and make it somebody else's problem... And you can force your way in parallelism, at many gigabytes a second, to be able to get back to specific events that you're looking for. So it's kind of a trade-off between - you pay more for that query time, but logs are typically something you write once and maybe query never, or query infrequently. So if your operating model is one that you write a lot of logs and you wanna query them all the time, this might not be the best approach. But for the developer and operator use case, it's usually an advantageous trade-off to be able to control the query costs.

**Mat Ryer:** \[56:02\] Okay. I'm gonna have to return an error to you though, Ed... Too many mentions of Loki.

**Ed Welch:** Done, and done.

**Jon Calhoun:** I thought you were gonna tell him deadline exceeded.

**Mat Ryer:** Yeah. Unfortunately, we're coming up to deadline exceeded is on the minute. But, there's still got time, so Ed, Jon, shut your faces, it's time for Unpopular Opinions.

**Jingle:** \[56:19\] to \[56:35\]

**Mat Ryer:** Okay, whooooo's gonna -- it's not Halloween, I don't know why I'm doing the spooky voice... Who's gonna start us off with an unpopular opinion today? I notice you've got six, Ed.

**Ed Welch:** \[laughs\] I think it's funny, because I laugh every time I think about the goal is to have an opinion that people don't agree with.

**Jon Calhoun:** So like if we had a KKK member, he would probably win. I hope he would win.

**Mat Ryer:** Yeah, yeah.

**Jon Calhoun:** Not in a good winning way.

**Mat Ryer:** Yeah, but we don't have them on.

**Jon Calhoun:** No.

**Ed Welch:** Right. So I'll phrase this -- this is my favorite unpopular opinion. I'll phrase it in a way that's likely to be received as it's most unpopular, but it's a nuanced conversation. My experience is that integration testing is usually a net loss, and I would largely recommend not doing it.

**Mat Ryer:** Oh, that is an interesting one.

**Jon Calhoun:** I have such mixed feelings on integration tests...

**Ed Welch:** I've definitely fixed and found bugs in integration tests. I've definitely caught significant bugs. However, the tremendous amount of anxiety and time that I spend and have spent looking at CI builds running for minutes or tens of minutes or more, combined with just the propensity for integration tests to have false positives, in my experience, leads to... Those couple bugs - it probably would have been better if they'd just made it through to prod and we didn't catch them, versus the overall costs. So that's where I am at within that loss, is that they do tend to catch bugs, but I don't know if it's worth it.

**Mat Ryer:** Hm. Interesting one.

**Jon Calhoun:** See, I agree with you fully about the fact that integration tests or tests that cover more scope -- like, integration or larger is how I'd put it in scope, tend to be the ones in applications that I've worked on that just break and then nobody has time to fix them, for some reason, and then you just continuously have breaking tests. And then somebody eventually has to fix it... It just can be a headache. But at the same time, they've been incredibly valuable in some situations, to the point that if I was taking your opinion and like "Okay, we're gonna act on this", it would be more along the lines of "I could maybe agree with not putting integration tests inside of continuous integration", but having them available for local development... A developer has to actively say "Okay, we're gonna pass on this for now.

**Ed Welch:** Yeah.

**Jon Calhoun:** But I could see value in not being a hard requirement whereas a lot of times unit tests - it's very clear, this should be passing; if it's not, something's broken or the test needs fixed.

**Ed Welch:** Yeah. If I gave that as my opinion, it wouldn't be as unpopular... \[laughs\] I really like the idea of running against operational data or clusters, so having tests that are running against systems, but kind of the "I built some database inserts and run them into an in-memory database, and then it verified some kind of data, and that runs as part of a CI build..." My experiences around those is it takes a long time to build them, it's really hard to change the data if you have to go in and make a change to the system... It's hard to understand what the original creator of the tests maybe even had in mind... And when it does flake out, because testing a distributed system and it maybe didn't do retries properly, or at all, or something changed - like you said, the people that usually are the ones that have to deal with it don't have the context to fix it, and it's just holding them up. It's like, "I didn't even change this code and this thing is breaking. I'm really unhappy with my life."

Maybe the key to success with integration tests is keeping their scope really small and purposeful, and then running them on-demand, kind of thing.

**Mat Ryer:** \[01:00:03.13\] Yeah, that's very interesting. We'll put this out on Twitter @gotimefm to see if indeed that is unpopular. You wrote down another one though, at which I feel like you might have an even better chance of winning this whole thing.

**Ed Welch:** The one that you have highlighted, that Windows is the best desktop OS?

**Mat Ryer:** Say it again clearly for the Twitter clip.

**Ed Welch:** I believe Windows is the best desktop OS.

**Mat Ryer:** Okay, really?

**Ed Welch:** Specifically Windows with WSL 2 is everything that I've ever needed from a desktop OS. A close second would probably be macOS, although my experiences with Docker and macOS are frustrating, and my experiences with updating macOS and having it become less stable after every time it does an update... And in general, the expense of the Mac hardware and hostility of the Mac support community has shied me away from it.

And I love Linux. I've tried for years and years to run desktop Linux, and I just don't -- I just want my computer to work. I wanna join this podcast today and expect not to have any trouble. And that's Windows, man... That's where I'm at with Windows.

Also, it's not -- I'm not gonna say that I love it, right? That Windows is great and it doesn't have a whole host of its own problems. I just think it has the least problems.

**Mat Ryer:** Yeah. It does have Minesweeper.

**Ed Welch:** Does it? I don't even know... Does Windows 11 still have Minesweeper?

**Jon Calhoun:** I have no idea.

**Mat Ryer:** Can we find out? That'll be the clincher for me.

**Ed Welch:** No, it doesn't.

**Jon Calhoun:** Every time I've tried to code it all in Windows there's always little things that -- I'm sure I could eventually get past them. Just one example - I can press Ctrl+A to go to the start of a line and Ctrl+E to go at the end of the line. In Windows, Ctrl+A is gonna select everything. So it all of a sudden doesn't do what I want... And I don't know if there is a way to just go to the start of the line; there probably is, I just don't know it.

**Mat Ryer:** It's probably got its own button, hasn't it? "Go to start of line", or...

**Ed Welch:** The Home button on my keyboard... \[laughs\]

**Mat Ryer:** You go in the Start menu, and you go to Run, and then you type "Move to start of line."

**Ed Welch:** Yeah, the keyboard shortcuts were the worst part for me trying to move to Mac. I could never get used to copy and paste. I could never figure it out.

**Jon Calhoun:** So I think it's one of those things that -- there's definitely a difference between Cmd and Ctrl, but when you get used to them, it's kind of hard to switch back at times.

**Mat Ryer:** Yeah, that's true. The muscle memory is -- I got a new keyboard recently, and I've just basically stopped using it, because I don't want it. It's meant to be ergonomic, but I've already learned it the non-ergonomic way. My bones have changed now, and adapting back is tough. Especially as you get older, your brain gets less malleable, doesn't it?

**Jon Calhoun:** The other issue I have with that type -- because the keyboard you have, Mat, I have it and I was using it, but my issue I ran into was first off, my hand was broken for the last two months, so I was already really slow.

**Mat Ryer:** That's not the keyboard's fault, Jon...

**Jon Calhoun:** Well, no, no, but I was already slow, so when the cast came off and I had a brace on I'm like, "I need to get caught up on work, not learn a new keyboard." But then on top of that, I would go from this new keyboard... I think by default where a Caps Lock key normally is was Delete. Which is great, because I never used Caps Lock, and I'd start using it for Delete. And then I went and got on my laptop at some point, just on my laptop, trying to do some work, and all of a sudden I'm just writing in crazy caps all over the place, because I'm hitting the wrong button. And I'm like -- I don't know how people do that mental shift of switching from one to the other. And I think that's probably the biggest issue I'd have with Windows, is if I only used it and I got used to it, I could probably make it work. But I'd have to fully commit and be like "I'm diving into Windows, and I'm okay with that."

**Ed Welch:** \[01:03:46.26\] Yeah, I feel that. Because I had always used Windows systems in corporate environments, and like I said, the copy-paste was sort of the worst one for me... And then you could go the route of remapping the keys, but that never seems to be flawless... So yeah, it's hard once you're well-established.

**Jon Calhoun:** If you're going to a Mac, it's definitely not one-to-one... Because Ctrl and Cmd both do different things at different times. So you still use Ctrl a little bit, you use use Cmd more.

**Mat Ryer:** But isn't it also true in Windows that it depends on the app? It's per application, whereas copy and paste is always the same across the whole operating system on a Mac.

**Jon Calhoun:** Windows is pretty much always Ctrl+C.

**Ed Welch:** Unless it's interrupt on the terminal... Which is why I think Mac did it right. It shouldn't be Ctrl+C, or they should have another character for interrupt... But yeah, that's the one that gets you in trouble. When you're working on a terminal in Windows, it's like "Can I use Ctrl+C to copy?" A lot of times it's Ctrl+Shift+C, but other then that, \[unintelligible 01:04:38.06\]

**Mat Ryer:** I remember it used to be you highlight it, and then it's done. Wasn't it that? You'd just highlight it in the terminal...

**Ed Welch:** Mine does that. I set up Windows terminal to do that now; it's my favorite thing ever. Every time I highlight something in the terminal it automatically copies it, and I don't know why anyone would not want it to do that. It's the handiest thing ever.

**Mat Ryer:** Yeah.

**Ed Welch:** Except when you accidentally click on the screen after you copy. But then you use a tool to manage your clipboard history, so that you could just go back.

**Mat Ryer:** Yeah. Okay, great. Alright, great. Good stuff. Well, we're slightly over time, but that was worth it. Thank you so much, Ed Welch, for joining us. Fascinating talk and dive into logs there. There's actually so much more we could talk about. We may do logging part two, Return of the... Something. Let's brainstorm that and...

**Ed Welch:** I'm not sure. I think that was all I had. But I'd be happy to come back though, it was really fun.

**Mat Ryer:** Okay. It's nice to have you. And Jon Calhoun was also here... Weren't you, Jon?

**Jon Calhoun:** I was here. Thanks for having me, Mat.

**Mat Ryer:** Always a pleasure. See you next time, on Go Time. Bye! \[Unpopular Opinions jingle starts 01:05:50.04\] That's the wrong one. It was all so professional until then, wasn't it? \[laughs\] Was it? No, it wasn't. Oh, good.

**Jon Calhoun:** I think I tune out at the exit song, so I didn't even notice. I was just like "Okay, cool. We're listening to a song."

**Mat Ryer:** Yeah, I know. But that's the Unpopular Opinion one, Jon. I pressed the wrong one...

**Jon Calhoun:** Well...

**Mat Ryer:** That's fine. Because honestly, the editors are just gonna clean this up. Clean it up. We love you, editors, by the way... I could do with editors in real life, like on calls and... You know what I mean? When you're having meetings. It'd be great if you could have someone editing...

**Ed Welch:** Yeah...

**Mat Ryer:** Cutting out the gaps...

**Ed Welch:** I could hear how many times I said "Um..."

**Mat Ryer:** You won't hear that in the final podcast.

**Ed Welch:** I know, because I said it usually when someone else started talking, and so like "Great! That's just gonna get cut out, because somebody else just started talking."

**Mat Ryer:** Yeah. They literally will go through and neaten up any gaps we leave, if we talk over each other... Yeah, it is really good.

**Jon Calhoun:** They do a good bit with what they can. Obviously, there are some times where it's like "Nope. That's too hard."

**Mat Ryer:** Yeah, you can't polish a turd, is what you're saying. Normally, we challenge them, you know...

**Ed Welch:** Shine it up a little bit though...

**Mat Ryer:** Yeah, that's my unpopular opinion - I actually think... Yeah, you probably can polish a turd, to be honest.

**Ed Welch:** The Myth Busters actually did.

**Mat Ryer:** And they've polished a turd on Myth Busters?

**Ed Welch:** Yeah.

**Mat Ryer:** Okay. We've said turd too many times for a family-friendly podcast.

**Jon Calhoun:** We start with the potty humor and end with the potty humor.

**Mat Ryer:** We did. It's a potty sandwich.

**Ed Welch:** Is this still on the show? Are we still going?

**Jon Calhoun:** I mean, it's recording right now...

**Mat Ryer:** Yeah, yeah, we're still live. I haven't played the final -- this is all legally binding until I play the final music, so...

**Ed Welch:** Gotcha.

**Mat Ryer:** Well, we'll put that in the show notes, the Myth Busters thing, if this makes it in... Well, thank you so much again, and thanks for listening. See you next time, on Go Time!
