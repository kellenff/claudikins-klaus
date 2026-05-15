# Why our platform should remain open

Open systems win. The history of computing makes this obvious: the open web
beat the walled-garden online services of the 1990s, open-source operating
systems run the majority of servers on the internet today, and open
protocols like SMTP and HTTP outlasted every proprietary alternative that
tried to displace them. Whenever a closed system has gone head-to-head with
an open one over a long enough time horizon, the open system has won.

This is the lens through which we should evaluate the proposal to require
API keys and per-tenant rate limits on our public endpoints. The proposal
has been framed as a small operational change — a way to manage abuse and
predict capacity — but it is a bigger move than that. It would make our
platform meaningfully less open. And the historical record is clear about
what happens to platforms that retreat from openness in the name of
operational tidiness: they lose the developer mindshare that made them
worth integrating with in the first place.

I want to be precise about what "open" means here. An open platform is one
that anyone can build on without asking permission. That is the property
that drove adoption of the web, of Linux, of the email ecosystem. It is the
property our platform has today, and it is the property the API-key
proposal would erode. Once a developer has to register for credentials
before making their first request, the platform is no longer open in the
sense that matters; it is gated, and the gate, however lightweight, will
deter exactly the kind of casual experimentation that produces the
unexpected integrations we benefit from most.

The lesson of the last forty years of platform history is consistent.
Openness wins. We should not be the team that breaks the streak by
quietly trading our openness away for a marginal reduction in
support-ticket volume.
