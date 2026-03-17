# Email Configuration

OtterFin uses email for transactional messages such as email address verification. The email provider is auto-detected from environment variables — no configuration is required to get started, as it defaults to a console provider that prints emails to stdout.

## Provider auto-detection

| Priority | Condition | Provider used |
|----------|-----------|---------------|
| 1 | `RESEND_API_KEY` is set | Resend API |
| 2 | `SMTP_HOST` is set | SMTP |
| 3 | Neither | Console (stdout) |

Set only the variables for the provider you want. Leave the others empty or unset.

## Common variables

These apply to all providers:

| Variable | Default | Description |
|----------|---------|-------------|
| `FROM_NAME` | `OtterFin` | Sender display name |
| `FROM_EMAIL` | `noreply@localhost` | Sender email address |

---

## Console (default — zero config)

No environment variables required. All outgoing emails are printed to stdout in a readable format. Verification links are extracted and printed prominently so you can click them directly from your terminal or Docker logs.

!!! warning
    The console provider does not deliver email to any inbox. It is only useful for local or Docker setups where you can read the printed link yourself. For real email delivery, configure SMTP or Resend.

```env
# No variables needed — console is the default when nothing else is set.
FROM_NAME="OtterFin"
FROM_EMAIL="noreply@localhost"
```

**Reading emails from Docker:**

```bash
docker logs otterfin-app -f
```

Verification links are printed to stdout and can be copied directly from the log output.

---

## SMTP

Uses standard SMTP with STARTTLS or implicit TLS.

| Variable | Default | Description |
|----------|---------|-------------|
| `SMTP_HOST` | *(required)* | SMTP server hostname |
| `SMTP_PORT` | `587` | Port — 587 for STARTTLS, 465 for implicit TLS |
| `SMTP_USER` | *(optional)* | SMTP username |
| `SMTP_PASS` | *(optional)* | SMTP password |

```env
FROM_NAME="OtterFin"
FROM_EMAIL="noreply@example.com"
SMTP_HOST="smtp.example.com"
SMTP_PORT="587"
SMTP_USER="user@example.com"
SMTP_PASS="your-smtp-password"
```

**Testing with Mailtrap:**

[Mailtrap](https://mailtrap.io) provides a free SMTP sandbox that catches all outgoing emails without delivering them to real inboxes. Use your Mailtrap inbox credentials for `SMTP_HOST`, `SMTP_USER`, and `SMTP_PASS`.

---

## Resend

Uses the [Resend](https://resend.com) API. Recommended for self-hosted deployments that need reliable email delivery.

| Variable | Default | Description |
|----------|---------|-------------|
| `RESEND_API_KEY` | *(required)* | API key from your Resend dashboard |

```env
FROM_NAME="OtterFin"
FROM_EMAIL="noreply@yourdomain.com"
RESEND_API_KEY="re_..."
```

!!! note "Domain verification"
    `FROM_EMAIL` must use a domain you have verified in the Resend dashboard at [resend.com/domains](https://resend.com/domains). Using an unverified domain will result in a `domain is not verified` error when OtterFin tries to send.

    For testing without a verified domain, Resend provides a shared address:

    ```env
    FROM_EMAIL="onboarding@resend.dev"
    ```

    `onboarding@resend.dev` can only send to your own Resend account's verified email address — it is not suitable for production use.
