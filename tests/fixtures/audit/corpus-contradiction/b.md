# The latency holdout was measuring the wrong thing

I have read the checkout team's writeup of the March holdout and I want to
register a concern before its conclusions calcify into roadmap decisions.

The headline finding from that writeup is the claim that "Reducing API
latency below 100ms produces no measurable conversion improvement." I do
not believe the holdout supports that claim, and I think repeating it as
established fact will lead us to make a real strategic mistake.

The holdout cohort was assigned at the session level, but our checkout
funnel is dominated by returning users whose first impression of the site
was formed long before the cohort assignment took effect. A user who
abandoned us six months ago because the cart felt sluggish does not come
back during an eight-week window to discover that it has improved; they
simply never come back. The holdout measures the conversion rate of users
who already made it to checkout, which is precisely the population for
whom latency matters least. The users for whom it matters most have
already been filtered out of the sample by prior bad experience.

A proper test would need to compare acquisition cohorts — users who first
encountered the optimised stack against users who first encountered the
unoptimised one — over a window long enough to capture return visits. That
test has not been run. Until it is, the holdout's conclusion is at best
premature and at worst actively misleading. I would urge the team not to
reallocate the roadmap on the basis of it.
