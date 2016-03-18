# On Asking Job Candidates to Code
http://philcalcado.com/2016/03/15/on_asking_job_candidates_to_code.html

At DigitalOcean, we are making some changes to the recruitment process of back-end developers. We simplified the job description and the interview process, and added a step that asks the candidate to write us some code. This is an account of my experience on hiring processes and their use of code reviews.

Around 2005, I had interviewed for a management position at a Globo.com, the Internet arm of the largest Latin American media conglomerate. I was coming back to product development from a couple of years as a consultant, and super excited about the opportunity.

The week before I started, my new boss sends me an email with what would be my first task: hire four people to join the four others already on my team. It was a weird situation; I couldn’t even tell what kind of people I would need as I hadn’t started working yet!

I began thinking about what kind of people I knew would be perfect for almost any engineering job, the people I would try to poach. I then thought about what particular traits or habits these people shared. Soon enough, I realised a common pattern: the people I thought were great for almost any engineering jobs would be always reading books. Not just the reference books everybody reads for their daily jobs, but texts on software architecture, object-oriented design, arcane programming languages, and advice on how to get better at their profession.

I had a somewhat popular programming blog in Brazil, and I wrote a job ad as a blog post. I described the job the best I could, and asked people to send me their resumé and a list of the three last books they had read.

The response was excellent. The temporary email address I had set up was full resumés, and I was able to confirm my hypothesis: the most interesting resumés correlate with a list containing books from the categories above.

At Globo.com, we’ve hired a large team following this process, and some of us alumni still use something similar in our new organisations.

Fast forward a couple of years, and I apply for a job at ThoughtWorks. I had worked for some international companies in the past, where I would be required to speak English when interacting with other offices, but this would be the first time I would speak English continuously for more than one hour. And over Skype. And having people assess me on my coding skills. I was freaking out.

Luckily ThoughtWorks had a process that was a bit different from the usual standards companies had back then. After a quick recruiter screening, they sent me three options of a code challenge, a small problem I could solve in any programming language I would like to use. My code submission would then be used during follow-up interviews, including a pairing session where a ThoughtWorker and I would try to extend my code adding a new feature.

I spent four years at ThoughtWorks and saw this process producing consistently good results over and over. It was also important that it didn’t particularly dictate what languages or tools a candidate would have to use. My experience with ThoughtWorks was that I could either choose an interesting project or an interesting programming language (I’ve written Internet-scale web crawlers in Drupal and timesheet software in F#), so we truly practiced the hire for attitude, not skill.

After all those years it was time to move on. I’ve had mostly a good experience at ThoughtWorks, but my last project there was probably my worst. As I looked around for a new position, I dreaded having to go through whiteboard coding sessions on some useless puzzle—the kind of stuff my friends reported the big Internet companies had them go through.

Once more I was lucky and ended up applying for a position at SoundCloud. Back then it was just a handful of engineers, and just like ThoughtWorks their process started with a recruiter screen and a coding challenge. Differently from ThoughtWorks, though, their code challenge wasn’t a puzzle but something closer to the work one would perform there. SoundCloud wanted me to build an uploader, from the user interface down to any back-end layers I thought I’d need.

There was one particular challenging part: how to implement a real-time-ish progress bar. As brain-dead as I was after eight hours at my project, I found myself thinking about possible solutions whilst on the train from East Croydon back to London, and immediately heading to my favourite late night coffee shop in Old Street to work on it. Solving the problem was a lot of fun, and if you want to check out what was in vogue in Clojure in 2011 and how one can pretend to know JavaScript by writing Scheme with curly brackets you can see the code here.

A couple of weeks later, I get the gig and move to Berlin. As it happens with small organisations, even before my probation period is over I start reviewing code for new candidates. Unfortunately, the experience was quite disappointing.

It turns out that most people would put together a Rails app with more lines on their Gemfile than lines of actual code they wrote. Worse, way too often people would just get an Adobe Flash uploader from a random Flash component website and not even write a single line of code. As pragmatic as a plugin plus minimal glue code might be, this wasn’t good use of the candidate’s time or mine: I needed them to write some code we could read before inviting them for more interviews.

And then we came up with a different challenge. This time, we would be a bit more strict. We would still not limit whatever programming language the candidate used (just like with ThoughtWorks, in a small startup like SoundCloud was back then we needed T-Shaped people more than anything), but we would ask you to use just your language’s standard library, no third-party libs or frameworks. To make it more feasible, we didn’t ask for a web app but for something all platforms would offer: a socket-server interface and a simple string protocol. To drive it closer to the needs of a company that grew from 10 to 100 million users in less than one year, we also included the need for clients to handle hundreds of clients at once.

But we’ve also done something else. Something that would improve the candidate experience by assuring them that their code fulfils the functional requirements before they expose it to us. Something that has saved us a lot of time by avoiding having to review code that obviously didn’t even work.

With the problem description, we sent to candidates a functional test suíte, a binary that when started would try to connect to the candidate’s server implementation, open lots of sockets, sends lots of messages, and verify the results against what the problem description stated. The candidate was instructed only to send their submission once it passed the functional test on their local box.

The best submission ever was definitely from Flávio Brasil, now at Twitter, who not only found a bug in our test harness but decompiled the (Scala!) code, wrote a patch and submitted it as part of his solution to the challenge. We received code submissions written in every language under the sun. We had so much qualitative data, I even gave a talk on some particular problems we found in code submissions using Node.js.

For this iteration of our recruitment process at DigitalOcean, I am trying to use as leverage these past experiences as much as possible. Overall the code challenge is very similar to the one we developed at SoundCloud, but there are some stark differences.

The first major change is that we are trying a simplified interviewing process, closer to ThoughtWorks’. Previously we had some ad-hoc whiteboarding or pairing sessions. This time, we will be giving the candidate an opportunity to talk about the code they wrote, instead of some generic question already catalogued in a best-selling book.

Another change is that we give more emphasis to path-to-production, or in how the application is tested, built, and executed. Irrespective of how product-centric we are, DigitalOcean is an infrastructure company and we break the fourth wall all the time during application development. Showing interest in build and runtime tools is a good indicator of culture fit.

But the most significant change is how the code is sent to review. In previous jobs, as a reviewer I would receive the code and the candidate’s resumé. Over the past few years, the industry and academia have built compelling evidence that our prejudice influences our decisions when it comes to deciding what good or bad code is. To help keep our bias at bay, we are asking candidates not to add any personally identifiable information to the submission. We will let reviewers know roughly at which level the candidate is and how many years of experience they have, but we will not disclose gender, name, nationaility, geographic location, or which schools or companies they have been through. This requires a lot of work from our incredibly awesome recruiting team, and a lot of patience from our reviewers, but preliminary results were quite good.

I am super excited about this first iteration of the new process, and more than just finding the right people to grow our team I hope we have enough data to share with the industry at some point in the near future.
