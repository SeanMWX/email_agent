---
name: send_smtp_email
description: Send email through SMTP providers such as 126, QQ, Sina, or Aliyun Mail using an SMTP authorization code or mailbox password. Use when the user wants Codex to scaffold SMTP mail sending, configure a `.env` file, or send a simple test email.
---

# Send SMTP Email

Use this skill when the task is to send a simple email through SMTP with a mailbox account.

## Trigger Cues

Use this skill when the user asks for any of the following:

- send a one-off test email through SMTP
- configure `.env` for a mailbox account such as `126`, `qq`, `sina`, or `aliyun`
- scaffold a reusable SMTP email sender
- debug SMTP host, port, SSL, login, or authorization-code issues

## Supported Providers

Built-in presets are available for:

- `126`
- `qq`
- `sina`
- `aliyun`
- `custom`

See [references/providers.md](references/providers.md) for preset host and authentication notes.

## Workflow

1. Read `.env` from the repo root. If it does not exist, use `.env.example` as the input contract.
2. Resolve provider defaults for `126`, `qq`, `sina`, or `aliyun`. If the provider is not listed, require `SMTP_HOST` and `SMTP_PORT`.
3. Prefer `scripts/send_email.py --dry-run` first when validating configuration.
4. Run `scripts/send_email.py` to send the message once the sender, recipient, and secret are confirmed.

## Default Behavior

- `SMTP_USERNAME` defaults to `FROM_EMAIL` when left empty.
- `SMTP_USE_SSL=true` means SMTPS, typically on port `465`.
- `SMTP_USE_SSL=false` means SMTP with `STARTTLS`, typically on port `587`.
- `SMTP_PASSWORD` is treated as the one secret field, whether the provider expects an authorization code or the mailbox password.

## Safety Rules

- Never print the full value of `SMTP_PASSWORD`.
- Treat `SMTP_PASSWORD` as either an SMTP authorization code or the mailbox password, depending on provider policy.
- Before a real send, make sure `FROM_EMAIL` and `TO_EMAIL` are not placeholder values unless the user explicitly wants a dry run.
- In normal SMTP usage, keep `FROM_EMAIL` aligned with the authenticated mailbox unless the provider supports aliases.
- Prefer a single test message before attempting repeated sends.

## Required Inputs

- `EMAIL_PROVIDER`
- `SMTP_PASSWORD`
- `FROM_EMAIL`
- `TO_EMAIL`
- `EMAIL_SUBJECT`
- `EMAIL_BODY`

## Optional Inputs

- `SMTP_HOST`
- `SMTP_PORT`
- `SMTP_USE_SSL`
- `SMTP_USERNAME`
- `FROM_NAME`
- `SMTP_TIMEOUT`

## Minimal `.env` Contract

For preset providers, the minimum practical configuration is:

```dotenv
EMAIL_PROVIDER=qq
SMTP_PASSWORD=your_smtp_secret
FROM_EMAIL=your_email@qq.com
TO_EMAIL=target@example.com
EMAIL_SUBJECT=Test email
EMAIL_BODY=Hello from SMTP
```

For `custom`, also set:

- `SMTP_HOST`
- `SMTP_PORT`
- `SMTP_USE_SSL`

## Common Commands

Validate config without sending:

```powershell
python scripts/send_email.py --dry-run
```

Send a real email:

```powershell
python scripts/send_email.py
```

Use a different env file:

```powershell
python scripts/send_email.py --env-file .tmp-send.env
```

## Failure Handling

- Authentication error: verify whether the provider expects an authorization code instead of the normal mailbox password.
- Connection or timeout error: verify host, port, SSL mode, and whether the mailbox enabled SMTP.
- Sender rejected: make `FROM_EMAIL` match the authenticated mailbox unless the provider explicitly supports aliases.
- Custom provider: set `SMTP_HOST`, `SMTP_PORT`, and `SMTP_USE_SSL` explicitly in `.env`.

## Example Requests

- "Send a test email to `someone@example.com` through QQ SMTP."
- "Set up `.env` for 126 mailbox SMTP."
- "Why does my SMTP login pass but the server rejects the sender address?"

## Resources

- Provider notes: [references/providers.md](references/providers.md)
- Sending script: `scripts/send_email.py`
