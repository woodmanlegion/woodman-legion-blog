---
name: "woodman-legion-blog"
description: "Publish blog posts via google-api Blogger. Also supports an email-send backend (inline images), when that separate skill is installed — not bundled, currently absent on most installs."
metadata:
  {
    "openclaw":
      {
        "emoji": "✍️",
        "requires": { "bins": ["python3"] },
      },
  }
---

# woodman-legion-blog

Blog post publisher with automatic backend selection. Uses `google-api blogger` by
default. If a separate `email-send` skill is installed, that becomes the preferred
backend instead (supports inline images via HTML email) — **check with
`woodman-legion-blog status` before assuming either is present; `email-send` is not
a bundled or commonly-installed skill, do not assume it exists.**

## Config (`~/.config/woodman-legion-blog/config.json`)

```json
{
  "blog_id": "YOUR_BLOGGER_BLOG_ID",
  "post_email": "YOUR_BLOGGER_POST_BY_EMAIL_ADDRESS@blogger.com"
}
```

- `blog_id` — required for `blogger` backend. Find it in Blogger settings or via `google-api blogger blogs`.
- `post_email` — required for `email` backend. Enable post-by-email in Blogger Settings → Email and generate the secret address.

## Commands

```bash
# Publish a post (auto-selects backend)
woodman-legion-blog post --title "Hello World" --body "<p>Body HTML</p>"

# Publish from a file
woodman-legion-blog post --title "Hello World" --body-file post.html

# Save as draft (blogger backend only)
woodman-legion-blog post --title "Draft" --body "..." --draft

# Force a specific backend
woodman-legion-blog post --title "Hello" --body "..." --backend email
woodman-legion-blog post --title "Hello" --body "..." --backend blogger

# Check which backend is active
woodman-legion-blog status
```

## Backend selection

| Priority | Backend | Requires | Image support |
|----------|---------|----------|---------------|
| 1 (preferred, if present) | `email` | `email-send` skill — separate, not bundled here | Yes — inline HTML images |
| 2 (default) | `blogger` | `google-api` skill | No inline images via API |

The `email` backend posts via Blogger's post-by-email feature. HTML content including
`<img>` tags with data URIs or hosted URLs will render correctly.

## Notes

- Body is always HTML — wrap plain text in `<p>` tags
- `--draft` only works with the `blogger` backend; the email backend publishes immediately
- Run `woodman-legion-blog status` to confirm which backend will be used before posting
- The `blogger` backend calls `google-api blogger post --yes`, skipping that command's
  interactive confirm prompt — this tool is meant to be invoked headlessly (by an agent),
  so there is no confirmation step of its own either. Only run it when you actually intend
  to publish (or pass `--draft`, where that backend supports it).
- `google-api`'s `gmail send` only builds a plain-text/HTML single-part message — it cannot
  carry inline images via `cid:` references. That limitation is real and is the actual
  reason `email-send` (SMTP, app-password auth) was the originally intended inline-image
  path, not a documentation error — see the repo-level note if/when that skill is built.
