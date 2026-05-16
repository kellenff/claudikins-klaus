# Expected findings: equivocation.md

## About this fixture

The piece argues against introducing API keys and rate limits, on the
grounds that doing so would make the platform "less open" and that history
shows open systems beat closed ones. The equivocation is on the word
**open**. In the historical examples (the web, Linux, SMTP/HTTP) "open"
means _open standards / open source / no licensing gatekeeper_ — anyone can
implement the protocol or fork the source. In the proposal under
discussion, "open" is redefined mid-argument to mean _no authentication
required before first use_ — anyone can hit the endpoint without
registering. These are different properties. SMTP requires that you run a
mail server; HTTP requires that you point a client at a host; Linux
requires that you install it. None of these are "open" in the sense the
author uses against the API-key proposal. The historical track record of
the first sense is being smuggled in to support a conclusion about the
second sense.

## Expected findings (most obvious first)

1. **What:** The argument equivocates on the term **"open"**. In paragraphs
   one and four it means _open standards / open source / unrestricted
   re-implementation_; in paragraph three it is redefined to mean _requires
   no registration before first request_.
   **Where:** Paragraph one establishes the historical sense ("open web",
   "open-source operating systems", "open protocols like SMTP and HTTP").
   Paragraph three states the new sense explicitly: "An open platform is
   one that anyone can build on without asking permission... Once a
   developer has to register for credentials before making their first
   request, the platform is no longer open in the sense that matters."
   **Why it matters:** The conclusion ("openness wins, do not require API
   keys") only follows if the historical track record applies to the second
   sense of "open". It does not — SMTP and HTTP are open in the first sense
   while routinely being deployed behind authentication, rate limits and
   credentials.

2. **What:** The inferential move that smuggles the shift is the sentence
   that introduces the redefinition while presenting it as a clarification.
   **Where:** Paragraph three: 'I want to be precise about what "open"
   means here.'
   **Why it matters:** The rhetorical framing ("be precise") signals
   tightening the definition, but in fact the author is substituting a
   different definition than the one the historical evidence supports.
   The auditor should flag this sentence as the load-bearing equivocation
   point.

3. **What:** The historical examples cited are not counter-examples to API
   keys or rate limits. SMTP servers regularly require authentication;
   HTTP services routinely sit behind credentialed APIs; Linux distributions
   have package signing and repository access controls. None of these
   features made the underlying systems "less open" in the sense the
   examples are meant to illustrate.
   **Where:** Paragraph one's list of historical winners.
   **Why it matters:** The examples do not survive even shallow scrutiny
   once the two senses of "open" are pulled apart. The argument's
   strongest-feeling evidence collapses under the equivocation.

4. **What:** The conclusion treats "openness" as a single property whose
   historical track record can be transferred wholesale to the local
   decision, rather than asking which specific property of openness drove
   the historical wins.
   **Where:** Final paragraph ("The lesson of the last forty years of
   platform history is consistent. Openness wins.").
   **Why it matters:** Even if we grant the historical premise, the
   transfer is invalid without an argument that the _same_ property is at
   stake here. The piece never makes that argument because doing so would
   expose the equivocation.
