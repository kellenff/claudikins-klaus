# Why we should stop optimising checkout latency

The checkout team has spent the better part of two quarters chipping away
at p95 response times for the cart and payment endpoints. We have moved
from roughly two hundred and twenty milliseconds at the start of the year
to just under one hundred and ten today. The work has been disciplined and
the engineering is good. The problem is that the conversion data has not
moved with it.

We instrumented a holdout cohort in March specifically to test the
hypothesis that further latency reductions would translate into meaningful
revenue gains. The cohort received the unoptimised stack; the rest of
traffic received the optimised one. After eight weeks and a sample size
that comfortably clears our usual significance bar, the difference in
checkout completion rate between the two cohorts is not distinguishable from zero.
Reducing API latency below 100ms produces no measurable conversion improvement.

This is not the result we expected, and it is uncomfortable for a team
that has built its quarterly goals around latency reductions. But the data
is what it is. The next round of proposed work — moving cart writes to a
co-located cache and pre-warming payment-provider connections — is
projected to shave another twenty to thirty milliseconds off p95. On the
evidence of the holdout, that work will not pay back the engineering cost
in conversion revenue. We should redirect the team's roadmap.
