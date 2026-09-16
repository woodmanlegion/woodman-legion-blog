---
name: "woodman-legion-blog"
description: "Publish blog posts via email-send (preferred, supports inline images) or google-api Blogger. Auto-detects available backend."
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

Blog post publisher with automatic backend selection. Prefers `email-send` when
available (supports inline images via HTML email). Falls back to `google-api blogger`
if only that skill is installed.

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
| 1 (preferred) | `email` | `email-send` skill | Yes — inline HTML images |
| 2 (fallback) | `blogger` | `google-api` skill | No inline images via API |

The `email` backend posts via Blogger's post-by-email feature. HTML content including
`<img>` tags with data URIs or hosted URLs will render correctly.

## Notes

- Body is always HTML — wrap plain text in `<p>` tags
- `--draft` only works with the `blogger` backend; the email backend publishes immediately
- Run `woodman-legion-blog status` to confirm which backend will be used before posting
