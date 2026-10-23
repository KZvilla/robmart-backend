# Security Policy

## About this repository

This repository is a **public engineering summary** of the RobMart backend. It
intentionally contains no source code, no implementation details, no credentials,
and no customer or patient data. The production source is private.

Security reports about the RobMart service, its API, its web client, or its
Android client are welcome and handled under this policy.

## Supported versions

RobMart is a continuously deployed service. Only the current production version
is supported; there are no backports.

## Reporting a vulnerability

**Do not open a public issue, pull request, or discussion for a security problem.**

Report privately through GitHub Security Advisories:

https://github.com/KZvilla/robmart-backend/security/advisories/new

Or by email: **security@robmart.com.ar**

A useful report includes:

- the component involved (API, web app, Android client, document storage);
- the affected endpoint or flow;
- steps to reproduce, and what an attacker gains: what they need beforehand
  and what they reach afterwards;
- a proof of concept if you have one.

**Never include real credentials, tokens, or personal/health data.** Redact them
or use dummy values; we will ask for anything else we need.

## Scope

In scope:

- authentication and session handling (JWT in httpOnly cookies, Redis session
  allowlist);
- authorization and role boundaries, including PostgreSQL row-level security
  coverage;
- MFA flows (TOTP and WebAuthn/passkeys);
- document storage and presigned URL handling;
- the web client and the native Android client (token storage, deep links,
  WebView);
- secret handling and accidental exposure in public artifacts.

Out of scope:

- findings that require prior control of a user's device, account, or mailbox;
- social engineering, physical access, or denial-of-service / volumetric testing;
- vulnerabilities in third-party services and dependencies (report them to the
  vendor; if RobMart makes one exploitable where it would not be otherwise, that
  part is in scope);
- automated scanner output without a demonstrated impact.

## Safe harbor

We will not pursue legal action for good-faith research that follows this policy,
stays within scope, avoids privacy violations and data destruction, and gives us
a reasonable window to fix the issue before public disclosure. Test only against
accounts and data you own or are explicitly authorized to use.

## What to expect

This is a small, maintainer-run company, so timelines are best-effort:

- acknowledgement within 7 days;
- initial assessment (confirmed or not, with a severity) within 14 days;
- a fix as soon as it is ready; critical issues first.

Fixed issues are published as a GitHub Security Advisory. You will be credited
unless you prefer otherwise.

## Disclosure

Please give us a reasonable window to ship the fix before any public disclosure;
we will agree on a date with you.
