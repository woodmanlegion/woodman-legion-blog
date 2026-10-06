---
name: "woodman-legion-blog"
description: "Publish blog posts via google-api gmail send (SMTP, preferred — inline images — when a Gmail App Password is configured) or google-api Blogger (fallback, no inline images). Auto-detects which is available."
metadata:
  {
    "openclaw":
      {
        "emoji": "✍️",
        "requires": { "bins": ["python3", "google-api"] },
      },
  }
---

# woodman-legion-blog

Blog post publisher with automatic backend selection, both backends implemented by
`google-api` — there is no separate email-sending skill.

- **`email`** — `google-api gmail send`, over SMTP with a Gmail App Password
  (`~/.config/google-api/app.passwd`). Preferred whenever that file is present, since
  it's the only one of the two that supports inline images.
- **`blogger`** — `google-api blogger post`. Used when no app password is configured.
  No inline images (the Gmail REST API path that backs this, with no app password,
  can only build a single-part message).

Run `woodman-legion-blog status` to see which is active before posting — don't assume.

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

This tool does not yet expose `google-api gmail send`'s `--image PATH[:CID]` (inline
attachments via Content-ID) — it just forwards `--body` as-is. Data URI `<img>` tags
inside `--body`/`--body-file` HTML go through either backend unchanged and should
render; real `cid:`-attached images currently require calling `google-api gmail send`
directly rather than through this wrapper.

## Notes

- Body is always HTML — wrap plain text in `<p>` tags
- `--draft` only works with the `blogger` backend; the email backend publishes immediately
- Both backends are invoked with `--yes`, skipping `google-api`'s own interactive confirm
  prompt — this tool is meant to be run headlessly (by an agent), so there is no
  confirmation step of its own either. Only run it when you actually intend to publish
  (or pass `--draft`, where that backend supports it).
- Switching backends is just adding/removing `~/.config/google-api/app.passwd` — no
  separate install step.
