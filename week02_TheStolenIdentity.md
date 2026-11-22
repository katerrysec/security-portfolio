# [Title, outcome-flavored: "Investigating an Identity Attack in Entra ID"]

## Scenario

## Environment
Microsoft Azure


## Investigation
ENTRY. The user was targeted by a phishing campaign and completed multifactor authentication before their session token was stolen. Because the token carried an MFA-satisfied claim, the attacker's subsequent use of the token did not trigger Conditional Access policies, having inherited an already-authenticated session rather than bypassing authentication controls directly.
 
ESCALATE. The compromised user also held Owner rights on a legacy application registration. This ownership relationship allowed the attacker to act as the application's service principal rather than the user, inheriting whatever Application permissions had been granted to it. The attacker exercised this Owner access to add a new client secret to the legacy app, configured with an expiration date significantly longer than standard policy allows indicating a long-term persistence mechanism.
 
PIVOT. The attacker established a second, independent Owner relationship on the legacy app by creating a new app registration and then used their existing Owner rights on the legacy app to add the new app's service principal as an additional Owner. This gave the attacker continued persistence that no longer depended on the originally compromised user account, allowing them to continue after secrets are rotated.

PERSIST. Another means of persistence was established through a custom scope published on the legacy app's Expose an API blade. This means the legacy app can be referenced as a callable backend resource and the attacker can request a delegated token scoped to it, allowing for subsequent phishing campaigns.

LOOT. In the final portion of the investigation, the attacker combined the rogue app's client ID, registered redirect URI, and the custom scope to construct a phishing URL designed to harvest OAuth tokens. When the user accepts the consent prompt, Entra ID issues an authorization code to the rogue app's redirect URI, which the app's backend exchanges for a delegated access token using the standard OAuth authorization flow. The delegated token allows the attacker to act on the exposed API as the phished user without a password and bypassing MFA.

## Findings and recommendations
What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.

## What I learned
