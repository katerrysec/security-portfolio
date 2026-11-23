# Investigating a Consent-Phishing Kill-Chain in Entra ID

## Scenario
The scenario is a reconstructed a five-stage OAuth consent-phishing kill chain in a live Azure tenant through forensic analysis of two linked app registrations. Logs show normal sign in traffic with no alarms.

## Environment
- Microsoft Azure
- App Registrations
- Branding & properties
- Owners
- Expose an API

## Investigation
ENTRY. The user was targeted by a phishing campaign and completed multifactor authentication before their session token was stolen. Because the token carried an MFA-satisfied claim, the attacker's subsequent use of the token did not trigger Conditional Access, having an already authenticated session rather than bypassing authentication controls directly.
 
ESCALATE. The compromised user also held Owner rights on a legacy application registration. This ownership relationship allowed the attacker to act as the application's service principal rather than the user, inheriting whatever Application permissions had been granted to it. The attacker exercised this Owner access to add a new client secret to the legacy app, configured with an expiration date significantly longer than standard policy allows indicating a long-term persistence mechanism.
 
PIVOT. The attacker established a second, independent Owner relationship on the legacy app by creating a new app registration and then used their existing Owner rights on the legacy app to add the new app's service principal as an additional Owner. This gave the attacker continued persistence that no longer depended on the originally compromised user account, allowing them to continue after secrets are rotated.

PERSIST. Another means of persistence was established through a custom scope published on the legacy app's Expose an API blade. This means the legacy app can be referenced as a callable backend resource and the attacker can request a delegated token scoped to it, allowing for subsequent phishing campaigns.

LOOT. In the final portion of the investigation, the attacker combined the rogue app's client ID, registered redirect URI, and the custom scope to construct a phishing URL designed to harvest OAuth tokens. When the user accepts the consent prompt, Entra ID issues an authorization code to the rogue app's redirect URI, OAuth2PermissionGrant, which the app's backend exchanges for a delegated access token using the standard OAuth authorization flow. The delegated token allows the attacker to act on the exposed API as the phished user without a password and bypassing MFA.

## Findings and recommendations
A user's credentials were compromised during a phishing campaign, which carried a MFA authenticated session that bypassed Conditional Access. This user held Owner rights to a legacy app registration, allowing the attacker to act as the app's service principal, elevating their access, and persisting after adding a new client secret with an abnormal expiration. The attacker then created a new rogue app registration, and registered it's service principal as an Owner of the legacy app, a secondary persistence mechanism that is not tied to the original compromised account. The attacker then published a custom scope on the legacy app's Expose an API blade, making the legacy app a backend callable resource, permitting requests for delegated tokens scoped to it. The attacker then used the app's client ID, redirect URI, and exposed scope to build a consent-phishing URL, which, when accepted, gives the attacker a delegated access token via OAuth  which the rogue app exchanges for access. All without a password or MFA.

Recommendations
- Revoke client secret
- Remove the rogue service
- Delete the custom API scope
- Revoke the OAuth2PermissionGrant
- Remove the attacker's redirect URI
- Review and reduce Graph application permissions
- Disable default user app registration
- Audit every app registration's Owner list
- Add alerts for new client secrets and new redirect URIs
## What I learned

