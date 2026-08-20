# Lab 5 – SAML Single Sign-On with Microsoft Entra ID

## Objective

In this lab, I configured SAML-based Single Sign-On (SSO) using Microsoft Entra ID. 
Entra ID served as the Identity Provider (IdP) for a SAML enterprise application acting as the Service Provider (SP). 
I created a pilot group and assigned it access to the enterprise application, then added Alice as a member of the group. 
When Alice attempted to access the application, she was redirected to Entra ID for authentication and authorization. 
After successful authentication, Entra ID generated a signed SAML response containing identity claims and sent it to the application's 
Assertion Consumer Service (ACS) URL. 
I also tested access with an unassigned user to verify that unauthorized users were denied access.

## Environment

- Microsoft Entra ID
- Microsoft Entra Enterprise Applications
- Microsoft Entra SAML Toolkit
- Entra security groups
- SAML 2.0
- Microsoft Entra sign-in logs

## Architecture

**Alice requests access to the enterprise application

        ↓
Application redirects Alice to Microsoft Entra ID

        ↓
Entra ID authenticates Alice
"Is this really Alice?"

        ↓
Entra ID checks application assignment
"Is Alice authorized to use this application?"

        ↓
Alice is a member of GRP-APP-SSO-Pilot

        ↓
Authorization succeeds

        ↓
Entra ID creates and cryptographically signs
a SAML response containing Alice's identity claims

        ↓
SAML response is sent to the application's Assertion Customer Service URL

        ↓
Application validates the SAML response

        ↓
Application identifies Alice from the NameID/claims

        ↓
Application creates an authenticated session

        ↓
ACCESS GRANTED**


For the negative version with user Rachel,

Rachel requests application

        ↓
Entra authenticates Rachel

        ↓
Entra checks application assignment

        ↓
Rachel is not assigned directly
and is not in GRP-APP-SSO-Pilot

        ↓
Authorization fails

        ↓
AADSTS50105

        ↓
ACCESS DENIED



## Configuration

- Created the `GRP-APP-SSO-Pilot` security group and assigned it to the enterprise application.
- Added Alice to the pilot group while leaving Rachel outside the group to test authorized and unauthorized access.
- Configured the Microsoft Entra SAML Toolkit enterprise application for SAML-based SSO.
- Set **Assignment required** to `Yes` so that only users who are directly assigned or members of an assigned group can access the application.
- Configured the **Identifier (Entity ID)** to uniquely identify the Service Provider within the SAML trust relationship.
- Configured the **Reply URL (ACS URL)** to tell Entra ID where the application expects to receive the SAML response.
- Configured the **Sign-on URL** as the application's SAML sign-in/start URL.
- Verified that **NameID** used `user.userprincipalname` in Email Address format so the application could identify the authenticated Entra user using their UPN.
- Configured the SAML signing certificate so the Service Provider could verify the authenticity and integrity of SAML messages issued by Entra ID.

## Testing

### Positive Test – Authorized User

For the positive test, I used Alice because she was a member of the `GRP-APP-SSO-Pilot` security group that had been assigned access to the enterprise application.
I expected Alice to successfully authenticate through Microsoft Entra ID and access the SAML application. The test succeeded. 
Entra authenticated and authorized Alice, generated the SAML response, and the application established an authenticated session.

This confirmed that group-based application assignment and SAML SSO were functioning as expected.

### Negative Test – Unauthorized User

For the negative test, I used Rachel. Rachel had a valid Entra ID account but was not a member of `GRP-APP-SSO-Pilot` and had not been directly assigned to the enterprise application.
I expected Rachel to be denied access. Her sign-in attempt failed with `AADSTS50105`, indicating that the user was not assigned access to the application.

This confirmed that a valid identity and successful identification by Entra ID do not automatically provide authorization to an application. 
Application access was controlled through assignment and group membership.


## Troubleshooting

During the initial SSO test, I encountered an application-side error in the SAML Toolkit. I investigated the issue using Microsoft Entra sign-in logs and 
discovered that the SSO attempt was using my existing administrator browser session instead of Alice's account, which was the identity configured for the test.
Rather than changing the SAML configuration, I opened a new private browser session and explicitly authenticated as Alice. 
The SAML SSO flow then completed successfully and Alice was granted access to the application.

This demonstrated the importance of using sign-in telemetry to identify where an SSO failure occurs before making configuration changes.


## Security Concepts Demonstrated

### Authentication vs. Authorization

Authentication verifies the identity of a user and determines whether they are who they claim to be, while authorization determines whether that authenticated identity has permission to access a particular resource.
In this lab, Alice was authenticated and authorized to access the application. Rachel had a valid Entra identity but was not authorized for the application because she was not assigned directly or through the pilot security group.

### Federated Single Sign-On

Microsoft Entra ID acted as the Identity Provider (IdP), while the SAML Toolkit acted as the Service Provider (SP). 
The application trusted Entra ID to authenticate users and provide signed identity information through SAML rather than requiring the application to perform the corporate authentication itself.

### Group-Based Access Control

Application access was assigned to `GRP-APP-SSO-Pilot` instead of individually assigning every user.

This provides a more scalable access-management model because users can be granted or revoked access through group membership rather than managing application assignments separately for each user.

### SAML Claims and NameID

Claims carry information about the authenticated user's identity from Entra ID to the Service Provider.

The NameID identifies the user to the application. In this lab, `user.userprincipalname` was used as the NameID so the SAML Toolkit could identify the 
corresponding user using their Entra UPN.

### SAML Signing Certificate

The signing certificate establishes trust in the SAML response. Entra ID cryptographically signs the SAML message, allowing the Service Provider to verify that the message came from the trusted Identity Provider and was not altered after it was issued.


## What I Learned

Before this lab, I understood SSO mainly as a way for users to access applications without repeatedly entering credentials. 
Through this lab, I learned that federated SSO relies on a trust relationship between an Identity Provider and a Service Provider. 
Microsoft Entra ID authenticates the user and sends trusted identity information to the application through a cryptographically signed SAML response. 
This allows applications to rely on the organization's identity provider instead of maintaining a separate corporate authentication process.

The positive and negative access tests reinforced the difference between authentication and authorization. 
Alice could access the application because she was authenticated by Entra ID and authorized through membership in `GRP-APP-SSO-Pilot`. 
Rachel had a valid Entra identity but was denied access because she was not assigned to the application. This reinforced the importance of using group-based assignments to manage application access in a scalable way.

I also learned not to make hasty configuration changes when troubleshooting SSO failures. During testing, an existing administrator browser session caused the wrong identity to be used. 
Instead of immediately modifying the SAML configuration, I investigated the Entra sign-in logs to determine which user authenticated and whether the sign-in succeeded. 
This reinforced a troubleshooting approach of reviewing logs, error codes, Conditional Access results, and policy evaluation before changing configuration.
