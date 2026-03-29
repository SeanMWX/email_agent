# email_agent

SMTP email demo skill for sending simple emails through mailbox accounts such as `126`, `QQ`, `Sina`, and `Aliyun`.

## What This Repo Contains

- `SKILL.md`: the skill definition and usage guidance
- `scripts/send_email.py`: a no-dependency SMTP sender script
- `.env.example`: the configuration template
- `references/providers.md`: provider presets and authentication notes

## Features

- Supports preset SMTP providers: `126`, `qq`, `sina`, `aliyun`
- Supports `custom` SMTP host, port, and SSL mode
- Loads config from `.env`
- Can fall back to SMTP config embedded in `SKILL.md`
- Supports SMTPS or `STARTTLS` depending on `SMTP_USE_SSL`
- Uses `SMTP_USERNAME`, or falls back to `FROM_EMAIL`
- Accepts recipient, subject, and body as runtime arguments
- Provides `--dry-run` for config validation before sending

## Quick Start

1. Copy `.env.example` to `.env`.
2. Fill in your mailbox account, secret, and sender settings.
3. Validate the resolved SMTP config.
4. Send the email.

```powershell
python scripts/send_email.py --to-email someone@example.com --email-subject "Test" --email-body "Hello" --dry-run
python scripts/send_email.py --to-email someone@example.com --email-subject "Test" --email-body "Hello"
```

## Minimal `.env` Example

```dotenv
EMAIL_PROVIDER=126
SMTP_PASSWORD=your_smtp_secret
FROM_EMAIL=your_email@126.com
```

If you use `custom`, also set:

```dotenv
SMTP_HOST=smtp.example.com
SMTP_PORT=465
SMTP_USE_SSL=true
```

## Supported Providers

Current presets:

- `126` -> `smtp.126.com:465`
- `qq` -> `smtp.qq.com:465`
- `sina` -> `smtp.sina.com:465`
- `aliyun` -> `smtp.aliyun.com:465`

More detail is in [references/providers.md](references/providers.md).

## Notes

- `126` and `QQ` commonly require an SMTP authorization code instead of the mailbox login password.
- `Sina` and `Aliyun` may accept the mailbox password, depending on account policy.
- In most cases, `FROM_EMAIL` should match the authenticated mailbox.
- Pass the recipient, subject, and body at runtime instead of hardcoding them into `SKILL.md`.
- Do not commit `.env`.

## Repository Notes

- `.env` is ignored by git.
- `skill_creator/` and `test/` are local-only and ignored by git in this repo.
- Temporary send files such as `.tmp-send-*.env` are ignored.
