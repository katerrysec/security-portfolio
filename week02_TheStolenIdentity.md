# [Title, outcome-flavored: "Investigating an Identity Attack in Entra ID"]

## Scenario

## Environment
Microsoft Azure


## Investigation
ENTRY. A user's credentials was phished and completed MFA prior to their session token being stolen. This resulted in a MFA-satisfied claim, and gave the attacker access that did not trigger Conditional Access policies.
 
ESCALATE. The user also has Owner's rights to a legacy app, allowing the attack to elevate from standard user permissions to a highly privileged Service Principal, granting access to API permissions. The attacker used said Owner rights to create a new client secret on the legacy app with an expiration date far exceeding normal policy.
 
PIVOT. Further investigation found that the attacker created a new app registration and added its service principal to the Owner's list of the legacy app, allowing requests for new credentials after rotating the client secret.

PERSIST. Another means of persistence was established through a custom scope published on the legacy app's Expose an API blade. With this 

LOOT. Finally, a redirect URI on the rogue app pointing at attacker-controlled infrastructure. Combining the rogue app's client ID, that redirect URI, and the exposed API scope produces a working phishing URL. A victim who is already signed in on a corporate device clicks Accept on a consent prompt, and the authorization code lands on the attacker's server.
## What broke / what surprised me
The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.

## What I learned
